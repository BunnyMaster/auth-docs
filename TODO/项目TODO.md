# 项目功能清单

## 〇、进度总览

### 功能组统计

| 模块                 | 功能组                     | 后端  | 前端  | 说明                                                                                                                        |
| ------------------ | ----------------------- | --- | --- | ------------------------------------------------------------------------------------------------------------------------- |
| module-security-\* | 安全认证基础设施                | ✅完成 |—| 1. JWT<br>2. 鉴权<br>3. 数据权限拦截<br>4. 操作日志 SPI |
| common-\*          | 公共 Starter              | ✅完成 |—| 1. core<br>2. data<br>3. web<br>4. jwt<br>5. cache<br>6. ip<br>7. nacos |
| auth-gateway       | 网关鉴权                    | ✅完成 |—| 1. `/api/auth/**`<br>2. `/api/system/**`<br>3. `/api/local-file/**` |
| service-auth       | 登录 / 会话 / 画像            | ✅完成 | ✅完成 | 1. 三模式登录<br>2. 刷新<br>3. 登出 |
| service-auth       | 在线用户管理                  | ✅完成 | ✅完成 | 1. `GET admin/users/online`<br>2. `system/online-user/index.vue` |
| service-auth       | 个人中心（自服务）| ✅完成 | ✅完成 | 1. Feature：`home/personal`<br>2. 改密 / 会话 / 登录日志 / 资料自编辑<br>3. 与用户工作站共用 `WorkspaceShell`<br>4. 顶栏入口 `useOpenPersonalWorkspace` |
| service-auth       | 会话索引僵尸清理                | ✅完成 |—| 1. 在线用户分页主动清理<br>2. `listUserSessions` 经 `loadListedSession` 读时逐条懒清理 |
| service-auth       | auth-service 调用链收敛      | ✅完成 |—| 1. `OutboundInternalJwtIssuer`<br>2. `InternalJwtFeignRequestInterceptor` 透传 USER 内部 JWT |
| service-system     | 菜单管理                    | ✅完成 | ✅完成 | 1. 详情瘦身 + 授权面分页<br>2. 登录菜单仍实时查库 |
| service-system     | 角色管理                    | ✅完成 | ✅完成 | 1. 含授权对象<br>2. 影响分析<br>3. 授权面<br>4. 数据范围（role_scope）|
| service-system     | 权限管理                    | ✅完成 | ✅完成 | 详情瘦身 + 授权面分页 |
| service-system     | 部门管理                    | ✅完成 | ✅完成 | 详情瘦身 + 授权面分页 |
| service-system     | 岗位管理                    | ✅完成 | ✅完成 | 详情瘦身 + 授权面分页 |
| service-system     | 用户管理                    | ✅完成 | ✅完成 | 1. 详情瘦身 + 工作台授权面分页<br>2. 含 user_scope |
| service-system     | 授权面与详情瘦身                | ✅完成 | ✅完成 | 1. count 详情 + `controller/authorization` 分页<br>2. 角色影响面已落地<br>3. 设计见 `Security二次封装/授权面与详情设计.md` |
| service-system     | grant_table 主体授权        | ✅完成 | ✅完成 | 1. 主体分配角色<br>2. 角色页授权对象维护（replace + 分页查询）|
| service-system     | 调度任务                    | ✅完成 | ✅完成 | 1. HTTP/Feign 内置调用<br>2. `job_params` 示例模板<br>3. Quartz 启停竞态已缓解 |
| service-system     | 调度任务分组                  | ✅完成 | ✅完成 |                                                                                                                           |
| service-system     | 角色 `roleType` 收敛        | ✅完成 | ✅完成 | 1. 已删除 `role_type`<br>2. 增量脚本 `db/system/V20260618_drop_sys_role_role_type.sql` |
| service-system     | 邮件模板                    | ✅完成 | ✅完成 | 1. `message/email-template`<br>2. 不收敛统一模板页（按 channel 分页）|
| service-system     | 消息模板（多渠道）| ✅完成 | ✅完成 | 1. EMAIL / SMS / IN_APP 分页管理<br>2. 钉钉模板与统一模板页已取消 |
| service-system     | 消息发送引擎                  | ✅完成 |—| 1. EMAIL / DING_TALK / SMS / IN_APP<br>2. 站内信异步发送 |
| service-system     | 站内信发送任务                 | ✅完成 | ✅完成 | 任务运营 + 收件人分页（已读/软删）|
| service-system     | 渠道投递记录                  | ✅完成 | ✅完成 | 1. `message_channel_delivery`<br>2. `message/send-record` |
| service-system     | 站内信业务分类                 | ✅完成 | ✅完成 | 1. 大类/小类字典<br>2. 发送页必填<br>3. `message/in-app-category` |
| service-system     | 我的消息（收件箱）| ✅完成 | ✅完成 | 1. 分页/详情/标已读/大类全部已读/用户侧删除<br>2. 顶栏角标 |
| service-system     | 用户偏好配置                  | ✅完成 | ✅完成 | 1. `sys_user_config`<br>2. `CurrentUserPreferenceController`<br>3. 布局 debounce 同步 |
| service-system     | 日志管理                    | ✅完成 | ✅完成 | 1. 登录<br>2. 操作<br>3. 授权审计<br>4. 调度<br>5. 密码历史 |
| service-system     | 授权失效运维                  | ✅完成 | ✅完成 | 1. `AuthorizationInvalidationStatsOpsController`<br>2. `AuthorizationInvalidationFailureRateTrendCard` |
| service-system     | role_scope / user_scope | ✅完成 | ✅完成 | 1. 角色抽屉 + 用户工作台面板<br>2. 不建独立 `data-scope` 页<br>3. 运行时 SQL 条件见 `DefaultDataScopeHandler` |
| service-system     | 文件管理                    | ✅完成 | ✅完成 | 1. 上传/治理/回收站<br>2. 个人文件/预签名预览<br>3. 异步导出任务（创建/分页/详情/取消/重试/下载/Worker+ 清理 Job）<br>4. 回收站定时清理/上传审计 |
| 授权失效               | 运行时链路                   | ✅完成 |—| Outbox → Feign → auth 编排 |
| 授权失效               | Trigger                 | ✅完成 |—| 1. ROLE<br>2. PERMISSION<br>3. GRANT<br>4. USER<br>5. USER_DEPT<br>6. USER_POST |
| auth-web           | 按钮级权限（`v-auth`）|—| ✅完成 | 1. `hasAuth` → `user.permissions`<br>2. 指令 `v-auth`<br>3. 组件 `<Auth>` / `<AuthDropdown items>`<br>4. 菜单侧不存按钮码 |

## 一、common 模块

### 1.1 module-security

| 能力 | 状态 | 说明 |
| ----------------------------------- | ---- | ------------------------------------------------------------------- |
| **module-security-contract** | ✅完成 | 1. `AuthProfile`<br>2. `ScopeGrant`<br>3. `AuthorizationChangeKind` |
| **module-security-core** | ✅完成 | 1. Access/Refresh/Internal Token<br>2. `PermissionMatcher` |
| **module-security-autoconfigure** | ✅完成 | 1. 鉴权管道<br>2. `@PublicApi`/`@InternalApi`<br>3. Feign 内部 JWT<br>4. `OperationLogAspect` |
| **module-security-data-permission** | ✅完成 | 1. `@DataScope`<br>2. `DataScopeInnerInterceptor` |
| **module-security-web-starter** | ✅完成 | 聚合 Starter |
| ├── 操作日志全链路 | ✅完成 | 注解 → AOP → SPI → `log_operation` |
| ├── 审计 SPI | ✅完成 | `AuthorizationAuditHandler` |

### 1.2 module-file / module-message-api

| 能力                     | 状态   | 说明                                                      |
| ---------------------- | ---- | ------------------------------------------------------- |
| **module-file**        | ✅完成  |                                                         |
| ├── Excel 异步导出         | ✅完成  | 1. 任务表 + Worker / 产物清理 Job<br>2. `FilterAsyncExport` 触发<br>3. 各模块只保留异步导出 |
| ├── Excel 导入           | ✅完成  | dept/post |
| └── 文件上传 / 对象存储        | ✅完成  | 1. S3 兼容存储<br>2. `file_record`<br>3. `FileUrlSigner` 预签名<br>4. 回收站定时清理 |
| **module-message-api** | ✅完成  | 1. `MessageSender` SPI<br>2. `TemplateMessageCommand` |
| ├── IN_APP 渠道          | ✅完成  | 1. `InAppMessageSender`<br>2. 模板管道<br>3. 异步派发 |
| └── 短信（阿里云 Dypns）| ✅完成  | 1. channel=SMS<br>2. 当前单厂商，按 provider 可扩展 |

## 二、service-auth

| 功能                      | 后端   | 前端   | 说明                                                                                           |
| ----------------------- | ---- | ---- | -------------------------------------------------------------------------------------------- |
| **登录 / 登出 / 刷新 Token**  | ✅完成  | ✅完成  | 三模式 + Cookie 刷新 |
| **登录辅助**                | ✅完成  | ✅完成  | 1. 验证码<br>2. 频率限制<br>3. 登录审计 |
| **内部认证 API**            | ✅完成  |—| `AuthInnerController` |
| **会话管理**                | ✅完成  | ✅完成  | 1. 全局在线分页/统计<br>2. `system/online-user/index.vue` |
| ├── 按用户查会话 / 踢人         | ✅完成  | ✅完成  | 1. `AdminSessionController`<br>2. 用户页 `UserSessionDialog` |
| ├── 会话列表前僵尸清理           | ✅完成 |—| 1. 在线用户 `OnlineUserQueryAdapter` 主动清理<br>2. `listUserSessions` 经 `loadListedSession` 读时逐条懒清理 |
| ├── auth-service 调用链收敛  | ✅完成 |—| 1. `OutboundInternalJwtIssuer`<br>2. `InternalJwtFeignRequestInterceptor` 透传 USER 内部 JWT |
| **个人中心（自服务）**           | ✅完成  | ✅完成  | 1. Feature：`home/personal`<br>2. 改密 / 会话 / 登录日志 / 资料自编辑<br>3. 共用 `WorkspaceShell`<br>4. 顶栏 `useOpenPersonalWorkspace` |
| ├── 修改密码 API            | ✅完成  | ✅完成  | 1. `CurrentUserController` `PUT /api/system/user/me/password`<br>2. `ProfileSecurityCard` |
| ├── 我的会话 API            | ✅完成  | ✅完成  | 1. `GET/POST.../me/sessions`<br>2. `ProfileSessionsPanel` |
| ├── 我的登录日志 API          | ✅完成  | ✅完成  | 1. `GET.../me/login-logs/page`<br>2. `personal/panels/login-log` |
| ├── 个人信息自编辑 API         | ✅完成  |—| 1. `PUT /api/system/me/profile`<br>2. `CurrentUserProfileService` |
| ├── 个人信息自编辑页            |—| ✅完成  | 1. `ProfileBasicInfoCard`<br>2. `updateMyProfile` |
| └── 个人中心页               | ✅完成  | ✅完成  | 1. `features/home/personal`<br>2. panels：profile / security / inbox / files / recycle / export-task<br>3. 顶栏 `useOpenPersonalWorkspace` |
| **AuthProfile 构建**      | ✅完成 |—| 1. 部门<br>2. 角色<br>3. 权限<br>4. 数据范围 |
| ├── 部门 / 角色 / 权限 / 数据范围 | ✅完成  |—| `AuthProfileQueryService` |
| **授权失效执行**              | ✅完成  |—| 1. 6 个 ImpactResolver<br>2. 幂等<br>3. 清理 Job |
| ├── 影响面刷新分批             | ✅完成 |—| `AuthProfileMaterializationService` 已 `BatchPartition` |

## 三、service-system

### 3.1 菜单管理

| 功能                       | 后端   | 前端   | 说明                                                                 |
| ------------------------ | ---- | ---- | ------------------------------------------------------------------ |
| **菜单管理**                 | ✅完成  | ✅完成  |                                                                    |
| ├── CRUD / 批量状态 / 拖拽     | ✅完成  | ✅完成  |                                                                    |
| ├── 分配角色                 | ✅完成  | ✅完成  | 1. `sys_menu_role`<br>2. 菜单不再绑定按钮权限<br>3. 前端分配时补齐父级菜单<br>4. 后端角色级联补全见 NEW TODO |
| ├── 详情瘦身                 | ✅完成  | ✅完成  | 1. `boundRoleCount`<br>2. `DetailRelationCountBar` |
| ├── 授权面（绑定角色分页）| ✅完成  | ✅完成  | 1. `MenuAuthorizationSurfaceController`<br>2. `MenuAuthorizationSurfaceDrawer` |
| └── 动态路由                 | ✅完成  | ✅完成  | 登录菜单仍实时查库全量返回，不走授权面分页 |

### 3.2 角色管理

| 功能                          | 后端  | 前端  |                                                                                                      |
| --------------------------- | --- | --- | ---------------------------------------------------------------------------------------------------- |
| **角色管理**                    | ✅完成 | ✅完成 |                                                                                                      |
| ├── CRUD / 批量启停 / 分配权限 / 导出 | ✅完成 | ✅完成 | 1. `IdsEnableStatusForm`<br>2. 角色页批量启用/禁用                                                            |
| ├── `role_code` 改名级联        | ✅完成 |—| 通过失效触发器实现                                                                                            |
| ├── `roleType` 字段收敛         | ✅完成 | ✅完成 | 1. 已删除 `role_type`                                                                                   |
| ├── Excel 导入                | ✅完成 | ✅完成 | 1. `SysRoleSpreadsheetService`<br>2. `role/index.vue`                                                |
| ├── 数据范围（role_scope）| ✅完成 | ✅完成 | 1. `GET/PUT.../role/{id}/scope`<br>2. `RoleDataScopeDialog`                                          |
| ├── 授权对象（grant 角色视角）| ✅完成 | ✅完成 | 1. `PUT.../grant-subjects` + `RoleGrantSubjectsAssignDrawer`<br>2. 分页见授权面                            |
| ├── 影响分析                    | ✅完成 | ✅完成 | 1. `RoleImpactController`<br>2. `RoleImpactAnalysisDrawer`：summary / users/page / refresh            |
| ├── 详情瘦身                    | ✅完成 | ✅完成 | 1. count：`permissionCount` / `menuCount` / `grantUser，Dept，PostCount`<br>2. `DetailRelationCountBar` |
| ├── 授权面                     | ✅完成 | ✅完成 | 1. 权限/菜单/授权对象 page<br>2. `RoleAuthorizationSurfaceDrawer`                                            |
| └── Admin 敏感角色校验策略          |—|—| 1. 决策：Admin 判断仅认常量 `roleCode`<br>2. 删除/改码时可校验，不做运行时过度兜底                                              |

#### 3.2.1 `roleType` 收敛（已落地备忘）

| 议题         | 落地结果                                              |
| ------------ | ----------------------------------------------------- |
| 字段语义     | 1. 已删除 `sys_role.role_type`<br>2. 前后端无 `roleType` 引用 |
| 菜单专用角色 | 1. 不单独分类型<br>2. 菜单绑定角色后按角色查权限 |
| 分类替代     | 由业务规则或 `roleCode` 表达，不再用类型枚举 |

### 3.3 权限管理

| 功能 | 后端 | 前端 | |
| ---------------------------------- | --- | --- | ----------------------------------------------------------- |
| **权限管理** | ✅完成 | ✅完成 | |
| ├── CRUD / 批量启停 / 引用展示 / 导出 / 失效挂钩 | ✅完成 | ✅完成 | `IdsEnableStatusForm` |
| ├── 详情瘦身 | ✅完成 | ✅完成 | 1. `boundRoleCount`<br>2. `DetailRelationCountBar` |
| ├── 授权面（绑定角色分页）| ✅完成 | ✅完成 | 1. `PermissionAuthorizationSurfaceController`<br>2. Drawer |
| └── Excel 导入 | ✅完成 | ✅完成 | 1. `SysPermissionSpreadsheetService`<br>2. `SpreadsheetImportDialog` |

### 3.4 部门管理

| 功能 | 后端 | 前端 | 说明 |
| --------------------------- | --- | --- | -------------------------------------------------- |
| **部门管理** | ✅完成 | ✅完成 | |
| ├── CRUD / 批量启停 / 移动 / 分配角色 | ✅完成 | ✅完成 | |
| ├── 闭包健康诊断 | ✅完成 | ✅完成 | |
| ├── 详情瘦身 | ✅完成 | ✅完成 | 1. `boundUserPost，RoleCount`<br>2. `DetailRelationCountBar` |
| ├── 授权面 | ✅完成 | ✅完成 | 1. 用户/岗位/角色 page<br>2. `DeptAuthorizationSurfaceDrawer` |
| └── Excel 导入导出 | ✅完成 | ✅完成 | |

### 3.5 岗位管理

| 功能 | 后端 | 前端 | 说明 |
| ----------------------------- | --- | --- | -------------------------------------------------------------- |
| **岗位管理** | ✅完成 | ✅完成 | |
| ├── CRUD / 批量启停 / 分配角色 / 失效挂钩 | ✅完成 | ✅完成 | |
| ├── 详情瘦身 | ✅完成 | ✅完成 | 1. 保留 `boundDept`<br>2. `boundUser<br>3. `RoleCount`<br>4. `DetailRelationCountBar` |
| ├── 授权面 | ✅完成 | ✅完成 | 1. 用户/角色 page<br>2. `PostAuthorizationSurfaceDrawer` |
| └── Excel 导入导出 | ✅完成 | ✅完成 | |

### 3.6 用户管理

| 功能 | 后端 | 前端 | |
| -------------------------- | --- | --- | ---------------------------------------------------------------------------------------------------------- |
| **用户管理** | ✅完成 | ✅完成 | |
| ├── CRUD / 三分配 / 会话踢人 / 导出 | ✅完成 | ✅完成 | |
| ├── 详情瘦身 | ✅完成 | ✅完成 | 1. `deptCount` / `postCount` / `directRoleCount` / `effectiveRole`/`PermissionCount`<br>2. `DetailRelationCountBar` |
| ├── 授权面（工作台）| ✅完成 | ✅完成 | 1. 部门/岗位/直连角色/生效角色/生效权限 page<br>2. `UserAuthorizationSurfaceDrawer`<br>3. 可刷当前用户画像 |
| ├── 数据范围（user_scope）| ✅完成 | ✅完成 | 1. `GET/PUT/DELETE.../user/{id}/scope`<br>2. 工作台 `GrantScopePanel` |
| ├── 部门/岗位分页（已有）| ✅完成 | ✅完成 | 1. `user-dept/{id}/page`<br>2. `user-post/{id}/page` |
| ├── 批量操作分批 | ✅完成 |—| 1. 大批量加载<br>2. 批量改状态<br>3. 批量删除等已分批 |
| └── Excel 导入 | ✅完成 | ✅完成 | 1. `SysUserSpreadsheetService`<br>2. `UserTablePanel` + `SpreadsheetImportDialog` |

### 3.7 grant_table

| 功能 | 后端 | 前端 | 说明 |
| --------------------------- | --- | --- | ------------------------------------------------------------------------------- |
| **grant_table** | ✅完成 | ✅完成 | |
| ├── DEPT / POST / USER 分配角色 | ✅完成 | ✅完成 | |
| ├── 角色页授权对象维护 | ✅完成 | ✅完成 | 1. `PUT.../grant-subjects` + seeds<br>2. `RoleGrantSubjectsAssignDrawer`（USER/DEPT/POST）|
| ├── 授权对象分页查询 | ✅完成 | ✅完成 | 1. `GET.../grant-subjects/page`<br>2. 并入角色授权面 |
| └── GRANT 失效 Trigger | ✅完成 |—| |

### 3.8 数据范围

| 功能 | 后端 | 前端 | 说明 |
| --------------------- | --- | --- | ------------------------------------------------------------------------------ |
| **数据范围管理** | ✅完成 | ✅完成 | 1. 不建独立 `system/data-scope/index.vue`<br>2. 挂在角色页 / 用户工作台 |
| ├── role_scope CRUD | ✅完成 | ✅完成 | 1. `SysRoleScopeService`<br>2. `RoleDataScopeDialog` |
| ├── user_scope CRUD | ✅完成 | ✅完成 | 1. `SysUserScopeService`<br>2. `GrantScopePanel`（可清除覆盖）|
| ├── 共享表单 | ✅完成 | ✅完成 | `SysDataScopeForm` / `DataScopeFormFields`（ALL / SELF / DEPT / DEPT_AND_CHILD）|
| ├── 运行时 SQL 条件 | ✅完成 |—| 1. `DefaultDataScopeHandler`<br>2. 单测（原 `DataScopeSqlHelper` 待办已落地为此）|
| ├── 演示种子 + harness | ✅完成 |—| 1. Release `13-seed-example-order.sql`<br>2. harness `dept-scope` / `dept-scope-list` / `data-scope` / `example-order-export` |
| └── `policyBind` 菜单清理 |—| ✅完成 | 占位菜单项 + i18n 已删 |

### 3.9 service-system-schedule

| 功能 | 后端 | 前端 | |
| -------------------------------- | --- | --- | ------------------------------------------------------------------------------------------------ |
| **调度任务** | ✅完成 | ✅完成 | |
| ├── CRUD / 启停 / 处理器目录 / 日志 | ✅完成 | ✅完成 | |
| ├── 断路器 / 启动同步 / SysJobException | ✅完成 |—| |
| ├── 消息调度任务类型 | ✅完成 |—| |
| ├── HTTP 内置调用（`HttpInvokeJob`）| ✅完成 |—| `HttpInvokeJobParams.method` 支持 GET/POST/PUT/DELETE |
| ├── Feign 内置调用（`FeignInvokeJob`）| ✅完成 |—| 1. `serviceName` + `path` + `method` + headers/body<br>2. 经 `loadBalancedRestTemplate` + 内部 JWT，已支持微服务间调用 |
| ├── job_params 示例模板 | ✅完成 | ✅完成 | 1. 后端 `@QuartzTask.jobParamsExample` + catalog API<br>2. 前端 JSON 编辑器 +「填入示例」|
| ├── Quartz 启停竞态 | ✅完成 |—| `SysJobBootstrapSynchronizer` / `SysJobScheduleReconciler` 缓解 DB 与调度器短暂不一致 |
| └── 历史清理 Job 改 cron | ✅完成 |—| 1. 授权失效 Outbox/Event 清理<br>2. 文件回收站清理均改为可配置 cron |
| **调度任务分组** | ✅完成 | ✅完成 | |

### 3.10 service-system-message

| 功能 | 后端 | 前端 | |
| --------------------------- | --- | --- | ---------------------------------------------------------------------------- |
| **统一消息发送** | ✅完成 |—| 1. `MessageSender` SPI<br>2. EMAIL / DING_TALK / SMS / IN_APP |
| ├── EMAIL / DING_TALK / SMS | ✅完成 |—| SMS 当前阿里云 Dypns |
| └── IN_APP | ✅完成 |—| 1. `InAppMessageSender`<br>2. compose 提交后异步派发 |
| **邮件模板** | ✅完成 | ✅完成 | 1. `message_template`，`channel=EMAIL`<br>2. `message/email-template` |
| **消息模板（多渠道）** | ✅完成 | ✅完成 | 1. 统一表按 channel<br>2. 独立分页管理，不建统一模板页 / 不做钉钉模板页<br>3. 已盘点 `scene_code` 并清理无用站内信模板<br>4. 优先级：数字越大权重越高 |
| ├── SMS 模板管理 | ✅完成 | ✅完成 | `message/sms-template` |
| └── 站内信模板 | ✅完成 | ✅完成 | 1. 纯文本 / Markdown<br>2. 预览与测试发送<br>3. `message/in-app-template` |
| **站内信发送任务** | ✅完成 | ✅完成 | 1. `message_send_task`<br>2. 分页/详情/发送/重试/撤回/删除<br>3. `message/in-app-message` + `compose` |
| ├── 收件人分页（已读/软删）| ✅完成 | ✅完成 | 1. `GET.../tasks/{taskId}/recipients`<br>2. `InAppSendTaskRecipientDrawer`（可筛 isRead）|
| **渠道投递记录** | ✅完成 | ✅完成 | 1. `message_channel_delivery`<br>2. `message/send-record` |
| **站内信业务分类** | ✅完成 | ✅完成 | 1. 大类/小类字典 CRUD / 批量启停<br>2. 发送页必填<br>3. `message/in-app-category` |
| **我的消息（收件箱）** | ✅完成 | ✅完成 | 1. `home/inbox`<br>2. `InAppInboxController`（me）|
| ├── 分页（未读优先）/ 顶栏铃铛角标 | ✅完成 | ✅完成 | `useInAppInboxStore` |
| ├── 详情（打开即标已读）| ✅完成 | ✅完成 | 1. `GET.../inbox/{messageId}`<br>2. `InboxMessageDetailView` |
| ├── 批量标已读 / 大类全部已读 | ✅完成 | ✅完成 | 1. `POST.../inbox/read`<br>2. `.../read-all` |
| └── 用户侧逻辑删除 / 大类全部删除 | ✅完成 | ✅完成 | 1. `DELETE.../inbox`<br>2. `.../inbox/all`<br>3. 入参 messageId 数组 |

### 3.11 文件管理

| 功能 | 后端 | 前端 | 说明 |
| ----------------------- | --- | --- | ---------------------------------------------------------------------------------------------- |
| **Excel 异步导出** | ✅完成 | ✅完成 | 1. 创建 / 分页 / 详情 / 取消 / 重试 / 下载<br>2. `FileExportTaskWorkerJob` + 产物清理 Job<br>3. 个人端 `ExportTaskPanel` + 管理端 `file/export-task`<br>4. `FilterAsyncExport`；仅异步 |
| **Excel 导入（dept/post）** | ✅完成 | ✅完成 | |
| **文件上传** | ✅完成 | ✅完成 | 1. `FileUploadController`<br>2. `bizType`：`avatar` / `attachment` / `internal_export`<br>3. 前端 `AvatarEditor` |
| ├── 单文件 / 多文件上传 | ✅完成 | ✅完成 | 1. `bizType`：`avatar` / `attachment` / `internal_export`<br>2. 前端 `AvatarEditor` |
| ├── 格式与大小校验 | ✅完成 | ✅完成 | 1. 后端 `FileUploadContentValidator`<br>2. 前端 `validateUploadFile` |
| ├── 私有桶 + 预签名预览 | ✅完成 | ✅完成 | 1. `FileUrlSigner`<br>2. 详情 `accessUrl`（默认 300s）|
| ├── 逻辑删除后资源不可访问 | ✅完成 |—| 1. 软删进回收站<br>2. 公文件物理删 |
| **文件治理（管理端）** | ✅完成 | ✅完成 | `file/record/index.vue` |
| ├── 分页查询 / 时间·类型筛选 | ✅完成 | ✅完成 | `GET /api/system/file/page` |
| ├── 详情 / 图片预览 | ✅完成 | ✅完成 | `FileRecordDescriptionDialog` |
| ├── 批量删除 | ✅完成 | ✅完成 | `@OperationLog` `FILE_RECORD` |
| ├── 下载 / 批量下载（ZIP）| ✅完成 | ✅完成 | `@OperationLog` `FILE_RECORD_DOWNLOAD` |
| ├── 公私有切换 | ✅完成 | ✅完成 | 1. `sys:file:privacy`<br>2. `@OperationLog` `FILE_RECORD_PRIVACY` |
| **回收站（管理端）** | ✅完成 | ✅完成 | `file/recycle/index.vue` |
| ├── 分页 / 详情 / 恢复 / 物理删除 | ✅完成 | ✅完成 | `@OperationLog` `FILE_RECYCLE_RESTORE` / `FILE_RECYCLE_PURGE` |
| **个人文件** | ✅完成 | ✅完成 | `file/me/record/index.vue` |
| ├── 分页 / 筛选 / 详情 / 预览 | ✅完成 | ✅完成 | `PersonalFileRecordController` |
| ├── 下载 / 批量下载 / 删除 | ✅完成 | ✅完成 | 按当前用户 `owner_id` 隔离 |
| **个人回收站** | ✅完成 | ✅完成 | 1. `file/me/recycle/index.vue`<br>2. 仅 `USER_SELF` 来源 |
| ├── 恢复 / 物理删除 | ✅完成 | ✅完成 | |
| **头像** | ✅完成 | ✅完成 | 1. 用户 `PUT /me/avatar`<br>2. 管理员 `PUT /user/avatar`<br>3. 旧头像 `OldAvatarCleanupTrigger` |
| **共享 UI 基础设施** |—| ✅完成 | `_shared` Hook / Columns / `DescriptionDialog` |
| **`file_record` 扩展字段** | ✅完成 |—| 1. `is_private`<br>2. `deleted_at`<br>3. `deleted_by`<br>4. `delete_source` |
| **回收站定时物理清理** | ✅完成 |—| 1. `FileRecycleCleanupJob`<br>2. `auth.file.recycle-cleanup.cron`（默认每日 03:00）|
| **上传操作审计** | ✅完成 |—| `FileUploadController` `@OperationLog` `FILE_UPLOAD` / `FILE_UPLOAD_MULTIPLE` |

### 3.12 日志管理

| 功能                                     | 后端   | 前端   |
| ---------------------------------------- | ------ | ------ |
| 登录 / 操作 / 授权审计 / 调度 / 密码历史 | ✅完成 | ✅完成 |

### 3.13 授权失效运维

| 功能 | 后端 | 前端 | |
| ----------------------------- | --- | --- | ---------------------------------------------------- |
| **授权失效运维** | ✅完成 | ✅完成 | |
| ├── Outbox / Event 分页详情 | ✅完成 | ✅完成 | |
| ├── DEAD/FAILED/PROCESSING 重试 | ✅完成 | ✅完成 | |
| ├── releaseClaim / 统计摘要 | ✅完成 | ✅完成 | |
| ├── eventId 互跳 |—| ✅完成 | |
| ├── 失败率时序统计 | ✅完成 |—| 1. `AuthorizationInvalidationStatsOpsController`<br>2. 按日/周聚合 |
| └── 失败率 ECharts |—| ✅完成 | `AuthorizationInvalidationFailureRateTrendCard` |

路径：`ops/authorization-invalidation/index.vue`

### 3.14 用户偏好配置

| 功能                     | 后端     | 前端     |
| ------------------------ | -------- | -------- |
| **用户偏好配置**         | ✅完成   | ✅完成 |
| ├── `sys_user_config` 表 | ✅完成   |—| 1. `user_id` + `config_key` + `config_value(JSON)` |
| ├── 偏好读写 API         | ✅完成   |—| `CurrentUserPreferenceController` `GET/PUT/DELETE /me/preferences` |
| └── 布局配置同步         | ✅完成   | ✅完成   | 1. `core/preferences/sync.ts`<br>2. 登录 hydrate + debounce upsert |

### 3.15 授权面与详情瘦身

| 功能 | 后端 | 前端 | 说明 |
| ----------------------- | --- | --- | ---------------------------------------------------------------------------------------- |
| **授权面与详情瘦身** | ✅完成 | ✅完成 | |
| ├── 包结构 | ✅完成 |—| 1. `controller/authorization`<br>2. `service/authorization/{query,impact}`<br>3. `mapper/authorization` |
| ├── 详情 VO 改 count | ✅完成 | ✅完成 | 1. 去掉关系 List<br>2. `DetailRelationCountBar` +「查看授权」|
| ├── 用户授权面 | ✅完成 | ✅完成 | 1. effective/direct page + summary<br>2. 工作台抽屉 |
| ├── 角色授权面 | ✅完成 | ✅完成 | 权限/菜单/grant-subjects page |
| ├── 部门/岗位/权限/菜单授权面 | ✅完成 | ✅完成 | 1. 绑定关系 page<br>2. 部门/岗位默认不挂刷新主按钮 |
| ├── 角色影响面 | ✅完成 | ✅完成 | summary / users/page / refresh |
| ├── 共享 UI |—| ✅完成 | 1. `_shared/authorization`<br>2. 各主体 Drawer |
| └── 清理全量 `selectBound*` | ✅完成 |—| 只读分页进 `mapper/authorization` |

## 四、auth-web

| 能力 | 状态 | 说明 |
| --------------------------- | --- | ------------------------------------------------ |
| 登录页 | ✅完成 | 1. 可配置背景图<br>2. 主题切换 View Transitions + clip-path（仅登录页）|
| 动态路由 / 路由守卫 | ✅完成 | |
| Feature-Driven 目录 | ✅完成 | 1. 业务在 `features/*`<br>2. `_shared` 按领域收敛 |
| 布局 / 色阶 / WorkspaceShell | ✅完成 | 1. 个人空间与用户工作站共用壳<br>2. TDesign 向样式（圆角 / hover / active）|
| 表格基础设施 | ✅完成 | 1. `DataTable` + `adaptive: fill \| viewport`<br>2. `TableBar`（列/密度/树展开；排序黑名单）|
| 分页 / AssignManagePanel | ✅完成 | AssignPanel v-model 注入见 NEW TODO |
| Dialog / Drawer 事件契约 | ✅完成 | |
| Excel 导入共享 Hook | ✅完成 | |
| 欢迎页 | ✅完成 | |
| 顶栏用户下拉 / 个人中心入口 | ✅完成 | 1. `useOpenPersonalWorkspace`<br>2. 顶栏主部门 `primaryDeptName` |
| 会话水印 | ✅完成 | `SessionWatermark`：主部门 + 用户名 |
| 按钮级权限（`v-auth`）| ✅完成 | 1. `hasAuth` → `user.permissions`<br>2. `v-auth` / `<Auth>` / `<AuthDropdown items>` |
| 前端 Sonar | ✅完成 | 工程接入 + 脚本；Code Smell 清理见 NEW TODO |

### 业务页面（路径相对 `auth-web/src/features/`，入口可经 `views/` 转发）

| 页面 | 后端 | 前端 | 说明 |
| ---------------------------- | ---- | ---- | -------------------------------------------------------------------------------------------- |
| 菜单管理 | ✅完成 | ✅完成 | 1. `system/menu`<br>2. 详情 count + 授权面；分配角色补父级菜单 |
| 用户管理 | ✅完成 | ✅完成 | 1. `system/user`<br>2. 工作台授权面分页 + user_scope |
| 部门管理 | ✅完成 | ✅完成 | 1. `system/dept`<br>2. 详情 count + 授权面 |
| 岗位管理 | ✅完成 | ✅完成 | 1. `system/post`<br>2. 详情 count + 授权面 |
| 权限管理 | ✅完成 | ✅完成 | 1. `system/permission`<br>2. 详情 count + 授权面 |
| 角色管理 | ✅完成 | ✅完成 | 1. `system/role`<br>2. 含授权对象 / 影响分析 / 授权面 / 数据范围 |
| 调度任务 | ✅完成 | ✅完成 | `schedule/schedule-task` |
| 调度分组 | ✅完成 | ✅完成 | `schedule/schedule-group` |
| 邮件模板 | ✅完成 | ✅完成 | `message/email-template` |
| 短信模板 | ✅完成 | ✅完成 | `message/sms-template` |
| 站内信模板 | ✅完成 | ✅完成 | `message/in-app-template` |
| 站内信发送任务 | ✅完成 | ✅完成 | 1. `message/in-app-message` + `compose`<br>2. 收件人抽屉 `InAppSendTaskRecipientDrawer` |
| 站内信业务分类 | ✅完成 | ✅完成 | `message/in-app-category` |
| 消息发送记录 | ✅完成 | ✅完成 | `message/send-record`（`message_channel_delivery`）|
| 我的消息 | ✅完成 | ✅完成 | 1. 个人中心 inbox panel / 顶栏角标<br>2. 亦可独立路由 |
| 授权失效运维 | ✅完成 | ✅完成 | 1. `ops/authorization-invalidation`<br>2. `AuthorizationInvalidationFailureRateTrendCard` |
| 登录 / 操作 / 授权审计 / 调度 / 密码历史日志 | ✅完成 | ✅完成 | `log/*` |
| 在线用户 | ✅完成 | ✅完成 | `system/online-user` |
| 数据权限配置 | ✅完成 | ✅完成 | 1. 无独立页<br>2. 角色 `RoleDataScopeDialog` + 用户工作台 `GrantScopePanel` |
| 文件记录 | ✅完成 | ✅完成 | `file/record` |
| 文件回收站 | ✅完成 | ✅完成 | `file/recycle` |
| 导出任务（管理端）| ✅完成 | ✅完成 | `file/export-task` |
| 我的文件 / 回收站 / 导出任务 | ✅完成 | ✅完成 | 1. 个人中心 panels：files / recycle / export-task<br>2. 亦可独立路由 `home/file/*` |
| 个人中心 | ✅完成 | ✅完成 | 1. `home/personal`<br>2. panels：profile / security / inbox / files / recycle / export-task<br>3. 资料含主部门 |

## 五、数据库

| 项 | 状态 | 说明 |
| ------------------ | ---- | ------------------------------------------------------ |
| 角色删除子表清理 | ✅完成 | 主表删除 + DB CASCADE |
| SUCCESS 失效记录归档 | ✅完成 | Outbox + Event 双 Job，默认 90 天 |
| `role_type` 字段删除 | ✅完成 | 增量脚本 `db/system/V20260618_drop_sys_role_role_type.sql` |
| `file_record` 治理字段 | ✅完成 | 1. `is_private`<br>2. `deleted_at`<br>3. `deleted_by`<br>4. `delete_source` |
| `sys_user_config` | ✅完成 | 1. 用户 UI 偏好 KV 存储<br>2. `CurrentUserPreferenceController` + 前端 `sync.ts` |
| `example_order` 演示种子 | ✅完成 | 1. Release `13-seed-example-order.sql`<br>2. harness `dept-scope` / `dept-scope-list` / `example-order-export` |

## 六、授权失效

### 6.1 链路

| 能力                                   | 状态   |
| -------------------------------------- | ------ |
| Outbox 写入 / 同步投递 / 补偿 Job      | ✅完成 |
| auth 编排 / 幂等 / 占位清理 / 历史清理 | ✅完成 |
| event_id 修复 / 运维重试 / 只读分页    | ✅完成 |

### 6.2 Trigger 对照

| kind       | auth ImpactResolver | system Trigger | 状态     |
| ---------- | ------------------- | -------------- | -------- |
| ROLE       | ✅                  | ✅             | ✅完成 |
| PERMISSION | ✅                  | ✅             | ✅完成 |
| GRANT      | ✅                  | ✅             | ✅完成 |
| USER       | ✅                  | ✅             | ✅完成 |
| USER_DEPT  | ✅                  | ✅             | ✅完成 |
| USER_POST  | ✅                  | ✅             | ✅完成 |

### 6.3 可观测性

| 功能                                  | 状态     | 说明 |
| ------------------------------------- | -------- | ---- |
| 运维统计 / releaseClaim / Outbox 补偿 | ✅完成   |      |
| 失败率时序统计 API                    | ✅完成   | `AuthorizationInvalidationStatsOpsController` |
| 失败率 ECharts 趋势图                 | ✅完成   | `AuthorizationInvalidationFailureRateTrendCard` |
