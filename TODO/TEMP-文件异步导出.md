看了你的代码，问题都很清晰。给你直接的优化方案：

## 问题 1: InnerExportPageController - 必须加 @InternalApi

**建议：**

```java
@InternalApi  // ✅ 必须加！只允许内部服务调用
@RequiredArgsConstructor
@Slf4j
@RequestMapping("/api/system/inner/export")
@RestController
public class InnerExportPageController {
    // ...
}
```

**理由：**

1. 这个接口**确实是给 Worker 通过 Feign（loadBalancedRestTemplate）调用的**
2. 不能让外部用户直接调用，否则能绕过任务创建直接拉数据
3. `@InternalApi` 会校验 `X-Internal-JWT`，确保只有内部服务能调

**参数是否过多？**

不过多，这 5 个参数都是必需的：

- `bizType`：路由到哪个 Fetcher
- `querySnapshotJson`：业务筛选条件
- `scopeSnapshotJson`：权限快照（核心！）
- `pageNum` / `pageSize`：分页参数

这是 " 导出中心 " 模式的代价：所有上下文必须通过 HTTP 传递。如果不想显式传这么多字段，可以考虑：

```java
// 优化：Worker 调用时传 taskId，Controller 内部查任务拿上下文
@PostMapping("/page-by-task")
public Result<InnerExportPageVO> pageByTask(
    @RequestParam Long taskId,
    @RequestParam int pageNum,
    @RequestParam int pageSize) {
    // 内部查 file_export_task 拿 bizType / query / scope
    // 但这会让 file 和 admin 耦合（admin 要能访问 file 表）
}
```

**不推荐**，因为跨服务查表又是另一个坑。保持当前显式传参就好。

---

## 问题 2: xxxExportPageFetcher 重复代码 - 抽取基类

**重复模式：**

```java
// 所有 Fetcher 都是这个套路
@Override
public IPage<Map<String, Object>> fetchPage(Q query, int pageNum, int pageSize) {
    String orderBySql = XxxOrderSqlBuilder.buildOrderBySql(query.getSort());  // 有的有，有的没有
    Page<Entity> pageParams = new Page<>(pageNum, pageSize);
    IPage<PO> page = mapper.selectListByPage(pageParams, query, orderBySql);
    
    List<Map<String, Object>> records = page.getRecords()
        .stream()
        .map(po -> BeanUtil.beanToMap(po, false, true))
        .collect(Collectors.toList());
    
    Page<Map<String, Object>> result = new Page<>(page.getCurrent(), page.getSize(), page.getTotal());
    result.setRecords(records);
    return result;
}
```

**优化方案：抽取模板方法基类**

```java
package com.auth.service.system.admin.export.fetcher;

import cn.hutool.core.bean.BeanUtil;
import com.auth.common.core.model.query.PageQueryRequest;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * 导出分页取数器抽象基类（模板方法消除重复）
 *
 * @param <Q> 查询条件类型
 * @param <R> 行类型（PO / Entity）
 * @author Bunny
 */
public abstract class AbstractExportPageFetcher<Q extends PageQueryRequest, R> 
        implements ExportPageFetcher<Q> {

    /**
     * 查询分页数据（子类实现具体 Mapper 调用）
     * @param query 查询条件
     * @param pageNum 页码
     * @param pageSize 每页条数
     * @return 分页数据
     */
    protected abstract IPage<R> queryPage(Q query, int pageNum, int pageSize);

    /**
     * 模板方法：查询 → 转 Map → 返回
     */
    @Override
    public final IPage<Map<String, Object>> fetchPage(Q query, int pageNum, int pageSize) {
        IPage<R> page = queryPage(query, pageNum, pageSize);
        
        List<Map<String, Object>> records = page.getRecords()
            .stream()
            .map(row -> BeanUtil.beanToMap(row, false, true))
            .collect(Collectors.toList());
        
        Page<Map<String, Object>> result = new Page<>(page.getCurrent(), page.getSize(), page.getTotal());
        result.setRecords(records);
        return result;
    }
}
```

**改造后的 Fetcher：**

```java
@RequiredArgsConstructor
@Component
public class SysRoleExportPageFetcher extends AbstractExportPageFetcher<SysRoleQuery, SysRoleEntity> {

    private final SysRoleMapper sysRoleMapper;

    @Override
    public String bizType() {
        return PlatformBizCodes.SYS_ROLE;
    }

    @Override
    public Class<SysRoleQuery> queryType() {
        return SysRoleQuery.class;
    }

    @Override
    protected IPage<SysRoleEntity> queryPage(SysRoleQuery query, int pageNum, int pageSize) {
        String orderBySql = SysRolePageOrderSqlBuilder.buildOrderBySql(query.getSort());
        Page<SysRoleEntity> pageParams = new Page<>(pageNum, pageSize);
        return sysRoleMapper.selectListByPage(pageParams, query, orderBySql);
    }
}
```

```java
@RequiredArgsConstructor
@Component
public class SysDeptExportPageFetcher extends AbstractExportPageFetcher<SysDeptPageQuery, SysDeptPageRowPO> {

    private final SysDeptMapper sysDeptMapper;

    @Override
    public String bizType() {
        return PlatformBizCodes.SYS_DEPT;
    }

    @Override
    public Class<SysDeptPageQuery> queryType() {
        return SysDeptPageQuery.class;
    }

    @Override
    protected IPage<SysDeptPageRowPO> queryPage(SysDeptPageQuery query, int pageNum, int pageSize) {
        Page<SysDeptEntity> pageParams = new Page<>(pageNum, pageSize);
        return sysDeptMapper.selectListByPage(pageParams, query);  // 这个没有 orderBySql
    }
}
```

**效果：**

- ✅ 消除了 `BeanUtil.beanToMap` 和 `Page` 构造的重复
- ✅ 每个 Fetcher 只需要实现 `queryPage()`，专注于自己的 Mapper 调用
- ✅ 保持灵活性：有 orderBySql 的传，没有的不传

---

## 问题 3: FileRecordController.exportAsync - 简化调用

**当前问题：**

```java
@PostMapping("/export/async")
public Result<FileExportTaskCreateResult> exportAsync(
    @Valid @RequestBody FileRecordPageQuery query,
    @RequestParam(required = false) String requestId,
    @RequestParam FileExportFormat exportFormat,
    @RequestParam String fetchService,  // ❌ 前端不应该传这个
    @RequestParam String fetchPath) {   // ❌ 前端不应该传这个
    // ...
}
```

**优化方案：**

```java
@Operation(summary = "异步导出文件记录")
@PreAuthorize("@auth.decide('sys:file:export')")
@PostMapping("/export/async")
public Result<FileExportTaskCreateResult> exportAsync(
    @Valid @RequestBody FileRecordPageQuery query,
    @Parameter(description = "幂等请求ID") @RequestParam(required = false) String requestId,
    @Parameter(description = "导出格式") @RequestParam(defaultValue = "XLSX") FileExportFormat exportFormat) {
    
    FileExportTaskCreateRequest request = FileExportTaskCreateRequests.xlsx(
        FileExportTaskBizTypes.FILE_RECORD,
        query,
        requestId,
        SecurityUserUtils.getUserId()
    );

    FileExportTaskCreateResult result = fileExportTaskOperations.createTask(request);
    return Result.success(result);
}
```

**关键：** `FileExportTaskCreateRequests.xlsx()` 工具方法已经默认了 `fetchService` 和 `fetchPath`：

```java
// FileExportTaskCreateRequests.java (你已经有了)
public static FileExportTaskCreateRequest xlsx(String bizType, Object query, String requestId, Long createdBy) {
    return of(bizType, query, requestId, createdBy, FileExportFormat.XLSX,
        FileExportTaskFetchRoutes.SERVICE_SYSTEM,              // 默认值
        FileExportTaskFetchRoutes.INNER_EXPORT_PAGE_PATH);     // 默认值
}
```

**如果未来真的拆服务了：**

- 其他微服务（如 `service-order`）导出时，提供另一个工具方法：

  ```java
  FileExportTaskCreateRequests.of(bizType, query, requestId, createdBy, format, 
      "service-order", "/api/order/inner/export/page");
  ```
