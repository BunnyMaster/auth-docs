# 项目功能清单

## 〇、进度总览

### 功能组统计

| 模块                 | 功能组                     | 后端   | 前端   | 说明                                                                                                    |
| ------------------ | ----------------------- | ---- | ---- | ----------------------------------------------------------------------------------------------------- |
| module-security-\* | 安全认证基础设施                | ✅完成  |—| JWT、鉴权、数据权限拦截、操作日志 SPI                                                                                |
| common-\*          | 公共 Starter              | ✅完成  |—| core / data / web / jwt / cache / ip / nacos                                                          |
| auth-gateway       | 网关鉴权                    | ✅完成  |—| `/api/auth/**`、`/api/system/**`、`/api/local-file/**`                                                  |
| service-auth       | 登录 / 会话 / 画像            | ✅完成  | ✅完成  | 三模式登录、刷新、登出                                                                                           |
| service-auth       | 在线用户管理                  | ✅完成  | ✅完成  | `GET admin/users/online` + `system/online-user/index.vue`                                             |
| service-auth       | 个人中心（自服务）| ✅完成  | ✅完成  | 改密 / 我的会话 / 我的登录日志 / 个人信息自编辑 / 页面 / 顶栏入口                                                              |
| service-auth       | 会话索引僵尸清理                | ✅完成  |—| 在线用户分页主动清理；`listUserSessions` 经 `loadListedSession` 读时逐条懒清理                                           |
| service-auth       | auth-service 调用链收敛      | ✅完成  |—| `OutboundInternalJwtIssuer` + `InternalJwtFeignRequestInterceptor` 透传 USER 内部 JWT                     |
| service-system     | 菜单管理                    | ✅完成  | ✅完成  |                                                                                                       |
| service-system     | 角色管理                    | ⚠️部分 | ⚠️部分 | 1. 数据范围 2. 授权对象 3. 影响分析                                                                               |
| service-system     | 权限管理                    | ✅完成  | ✅完成  |                                                                                                       |
| service-system     | 部门管理                    | ✅完成  | ✅完成  |                                                                                                       |
| service-system     | 岗位管理                    | ✅完成  | ✅完成  |                                                                                                       |
| service-system     | 用户管理                    | ✅完成  | ✅完成  |                                                                                                       |
| service-system     | grant_table 主体授权        | ⚠️部分 | ⚠️部分 | 1. 角色页授权对象维护                                                                                          |
| service-system     | 调度任务                    | ✅完成  | ✅完成  | HTTP/Feign 内置调用；`job_params` 示例模板；Quartz 启停竞态已缓解                                                      |
| service-system     | 调度任务分组                  | ✅完成  | ✅完成  |                                                                                                       |
| service-system     | 角色 `roleType` 收敛        | ✅完成  | ✅完成  | 已删除 `role_type`；增量脚本 `db/system/V20260618_drop_sys_role_role_type.sql`                                |
| service-system     | 邮件模板                    | ✅完成  | ✅完成  | `message/email-template`；不收敛统一模板页（按 channel 分页）|
| service-system     | 消息模板（多渠道）| ✅完成  | ✅完成  | EMAIL / SMS / IN_APP 分页管理；钉钉模板与统一模板页已取消                                                               |
| service-system     | 消息发送引擎                  | ✅完成  |—| EMAIL / DING_TALK / SMS / IN_APP；站内信异步发送                                                              |
| service-system     | 站内信发送任务                 | ✅完成  | ✅完成  | 任务运营 + 收件人分页（已读/软删）|
| service-system     | 渠道投递记录                  | ✅完成  | ✅完成  | `message_channel_delivery` + `message/send-record`                                                    |
| service-system     | 站内信业务分类                 | ✅完成  | ✅完成  | 大类/小类字典；发送页必填；`message/in-app-category`                                                               |
| service-system     | 我的消息（收件箱）| ✅完成  | ✅完成  | 分页/详情/标已读/大类全部已读/用户侧删除 + 顶栏角标                                                                         |
| service-system     | 用户偏好配置                  | ✅完成  | ✅完成  | `sys_user_config` + `CurrentUserPreferenceController` + 布局 debounce 同步                                |
| service-system     | 日志管理                    | ✅完成  | ✅完成  | 登录 / 操作 / 授权审计 / 调度 / 密码历史                                                                            |
| service-system     | 授权失效运维                  | ✅完成  | ✅完成  | `AuthorizationInvalidationStatsOpsController` + `AuthorizationInvalidationFailureRateTrendCard`       |
| service-system     | role_scope / user_scope | ❌未完成 | ❌未完成 | 无管理 API / 无 `system/data-scope/index.vue`                                                             |
| service-system     | 文件管理                    | ✅完成  | ✅完成  | 上传/治理/回收站/个人文件/预签名预览/回收站定时清理/上传审计                                                                     |
| 授权失效               | 运行时链路                   | ✅完成  |—| Outbox → Feign → auth 编排                                                                              |
| 授权失效               | Trigger                 | ✅完成  |—| ROLE / PERMISSION / GRANT / USER / USER_DEPT / USER_POST                                              |
| auth-web           | 按钮级权限（`v-perms`）|—| ✅完成  | `hasAuth` 仅查 `user.permissions`（数组 AND）；按钮码在前端常量 + `v-perms`；`sys_menu_permission` / `meta.auths` 已移除 |

## 一、common 模块

### 1.1 module-security

| 能力                                  | 状态   | 说明                                                                  |
| ----------------------------------- | ---- | ------------------------------------------------------------------- |
| **module-security-contract**        | ✅完成  | `AuthProfile`、`ScopeGrant`、`AuthorizationChangeKind` |
| **module-security-core**            | ✅完成  | Access/Refresh/Internal Token、`PermissionMatcher`                   |
| **module-security-autoconfigure**   | ✅完成  | 鉴权管道、`@PublicApi`/`@InternalApi`、Feign 内部 JWT、`OperationLogAspect`  |
| **module-security-data-permission** | ✅完成  | `@DataScope`、`DataScopeInnerInterceptor`                            |
| **module-security-web-starter**     | ✅完成  | 聚合 Starter                                                          |
| ├── 操作日志全链路                         | ✅完成  | 注解 → AOP → SPI → `log_operation`                                    |
| ├── 审计 SPI                          | ✅完成  | `AuthorizationAuditHandler`                                         |

### 1.2 module-file / module-message-api

| 能力                     | 状态   | 说明                                                      |
| ---------------------- | ---- | ------------------------------------------------------- |
| **module-file**        | ✅完成  |                                                         |
| ├── Excel 同步导出         | ✅完成  | EasyExcel + 各模块 `/export`                               |
| ├── Excel 导入           | ✅完成  | dept/post                                               |
| └── 文件上传 / 对象存储        | ✅完成  | S3 兼容存储 + `file_record` + `FileUrlSigner` 预签名 + 回收站定时清理 |
| **module-message-api** | ✅完成  | `MessageSender` SPI、`TemplateMessageCommand`            |
| ├── IN_APP 渠道          | ✅完成  | `InAppMessageSender` + 模板管道 + 异步派发                         |
| └── 短信（阿里云 Dypns）| ✅完成  | channel=SMS；当前单厂商，按 provider 可扩展                          |

## 二、service-auth

| 功能                      | 后端   | 前端   | 说明                                                                                           |
| ----------------------- | ---- | ---- | -------------------------------------------------------------------------------------------- |
| **登录 / 登出 / 刷新 Token**  | ✅完成  | ✅完成  | 三模式 + Cookie 刷新                                                                              |
| **登录辅助**                | ✅完成  | ✅完成  | 验证码、频率限制、登录审计                                                                                |
| **内部认证 API**            | ✅完成  |—| `AuthInnerController`                                                                        |
| **会话管理**                | ✅完成  | ✅完成  | 全局在线分页/统计；`system/online-user/index.vue`                                                     |
| ├── 按用户查会话 / 踢人         | ✅完成  | ✅完成  | `AdminSessionController`；用户页 `UserSessionDialog`                                             |
| ├── 会话列表前僵尸清理           | ✅完成 |—| 在线用户 `OnlineUserQueryAdapter` 主动清理；`listUserSessions` 经 `loadListedSession` 读时逐条懒清理           |
| ├── auth-service 调用链收敛  | ✅完成 |—| `OutboundInternalJwtIssuer` + `InternalJwtFeignRequestInterceptor` 透传 USER 内部 JWT                         |
| **个人中心（自服务）**           | ✅完成  | ✅完成  | 改密 / 我的会话 / 我的登录日志 / 个人信息自编辑 / 页面 / 顶栏入口                                        |
| ├── 修改密码 API            | ✅完成  | ✅完成  | `CurrentUserController` `PUT /api/system/user/me/password`；`ProfileSecurityCard`             |
| ├── 我的会话 API            | ✅完成  | ✅完成  | `GET/POST.../me/sessions`；`ProfileSessionsPanel`                                            |
| ├── 我的登录日志 API          | ✅完成  | ✅完成  | `GET.../me/login-logs/page`；`account/login-log/index.vue`                                   |
| ├── 个人信息自编辑 API         | ✅完成  |—| `PUT /api/system/me/profile`；`CurrentUserProfileService`                                      |
| ├── 个人信息自编辑页            |—| ✅完成  | `ProfileBasicInfoCard` + `updateMyProfile`                                                   |
| └── 个人中心页               | ✅完成  | ✅完成  | `account/profile/index.vue` + `account/login-log/index.vue`；顶栏 `useAccountNavigation`        |
| **AuthProfile 构建**      | ✅完成 |—| 部门 / 角色 / 权限 / 数据范围                                                              |
| ├── 部门 / 角色 / 权限 / 数据范围 | ✅完成  |—| `AuthProfileQueryService`                                                                    |
| **授权失效执行**              | ✅完成  |—| 6 个 ImpactResolver + 幂等 + 清理 Job                                                           |
| ├── 影响面刷新分批             | ✅完成 |—| `AuthProfileMaterializationService` 已 `BatchPartition`                                                    |

## 三、service-system

### 3.1 菜单管理

| 功能 | 后端 | 前端 |

| -------------------------- | ------ | ------ | ---------------------------------------------------------- |

| **菜单管理** | ✅完成 | ✅完成 |

| ├── CRUD / 批量状态 / 拖拽 | ✅完成 | ✅完成 |

| ├── 分配角色 | ✅完成 | ✅完成 | `sys_menu_role`；菜单不再绑定按钮权限 |

| ├── 绑定角色查询 | ✅完成 | ✅完成 | `getDetail` → `boundRoles`；`MenuBindingDescriptionDialog` |

| └── 动态路由 | ✅完成 | ✅完成 |

### 3.2 角色管理

| 功能                       | 后端   | 前端   |                                                                              |
| ------------------------ | ---- | ---- | ---------------------------------------------------------------------------- |
| **角色管理**                 | ⚠️部分 | ⚠️部分 |                                                                              |
| ├── CRUD / 批量启停 / 分配权限 / 导出 | ✅完成  | ✅完成  | `IdsEnableStatusForm`；角色页批量启用/禁用                                            |
| ├── `role_code` 改名级联     | ✅完成  |—| 通过失效触发器实现                                                                    |
| ├── `roleType` 字段收敛      | ✅完成  | ✅完成  | 已删除 `role_type`；增量脚本见 `db/system/V20260618_drop_sys_role_role_type.sql`      |
| ├── Excel 导入             | ✅完成  | ✅完成  | `SysRoleSpreadsheetService`；`role/index.vue`                                |
| ├── 数据范围（role_scope）| ❌未完成 | ❌未完成 |                                                                              |
| ├── 授权对象（grant 角色视角）| ❌未完成 | ❌未完成 |                                                                              |
| ├── 影响分析                 | ❌未完成 | ❌未完成 |                                                                              |
| ├── 详情（绑定权限 / 菜单 / 授权主体）| ✅完成  | ✅完成  | `RoleDescriptionDialog`：`boundPermissions` / `boundMenus` / grant 反查用户·部门·岗位 |
| └── Admin 敏感角色校验策略       |—|—| 决策：Admin 判断仅认常量 `roleCode`；删除/改码时可校验，不做运行时过度兜底                               |

#### 3.2.1 `roleType` 收敛（已落地备忘）

| 议题         | 落地结果                                              |
| ------------ | ----------------------------------------------------- |
| 字段语义     | 已删除 `sys_role.role_type`；前后端无 `roleType` 引用 |
| 菜单专用角色 | 不单独分类型；菜单绑定角色后按角色查权限              |
| 分类替代     | 由业务规则或 `roleCode` 表达，不再用类型枚举          |

### 3.3 权限管理

| 功能                          | 后端   | 前端   |                                                             |
| --------------------------- | ---- | ---- | ----------------------------------------------------------- |
| **权限管理**                    | ✅完成  | ✅完成  |                                                             |
| ├── CRUD / 批量启停 / 引用展示 / 导出 / 失效挂钩 | ✅完成  | ✅完成  | `IdsEnableStatusForm`                                         |
| ├── 详情（绑定角色 / 绑定菜单）| ✅完成  | ✅完成  | `SysPermissionDetailVO`；`PermissionDescriptionDialog`       |
| └── Excel 导入                | ✅完成  | ✅完成  | `SysPermissionSpreadsheetService`；`SpreadsheetImportDialog` |

### 3.4 部门管理

| 功能 | 后端 | 前端 |

| ---------------------------------- | ------ | ------ | ------------------------------------------ |

| **部门管理** | ✅完成 | ✅完成 |

| ├── CRUD / 批量启停 / 移动 / 分配角色 | ✅完成 | ✅完成 |

| ├── 闭包健康诊断 | ✅完成 | ✅完成 |

| ├── 详情（绑定用户 / 岗位 / 角色）| ✅完成 | ✅完成 | `SysDeptDetailVO`；`DeptDescriptionDialog` |

| └── Excel 导入导出 | ✅完成 | ✅完成 |

### 3.5 岗位管理

| 功能 | 后端 | 前端 |

| ------------------------------ | ------ | ------ | ------------------------------------- |

| **岗位管理** | ✅完成 | ✅完成 |

| ├── CRUD / 批量启停 / 分配角色 / 失效挂钩 | ✅完成 | ✅完成 |

| ├── 详情（绑定角色）| ✅完成 | ✅完成 | `SysPostDetailVO`；`PostDetailDialog` |

| └── Excel 导入导出 | ✅完成 | ✅完成 |

### 3.6 用户管理

| 功能                         | 后端   | 前端   |                                                                            |
| -------------------------- | ---- | ---- | -------------------------------------------------------------------------- |
| **用户管理**                   | ✅完成  | ✅完成  |                                                                            |
| ├── CRUD / 三分配 / 会话踢人 / 导出 | ✅完成  | ✅完成  |                                                                            |
| ├── 查看（用户档案 + 授权详情）| ✅完成  | ✅完成  | `GET /detail` + `UserDescriptionDialog`：基本信息 Description + 部门/岗位/角色/有效权限表格 |
| ├── 授权详情                   | ✅完成  | ✅完成  | 已并入「查看」；`UserDescriptionDialog`                                            |
| ├── 授权详情聚合 API             | ✅完成  |—| `GET /detail`；有效权限对齐 `UserEffectiveCodesResolver`                          |
| ├── 批量操作分批                 | ✅完成  |—| 大批量加载、批量改状态、批量删除等已分批                                                       |
| └── Excel 导入               | ✅完成  | ✅完成  | `SysUserSpreadsheetService`；`UserTablePanel` + `SpreadsheetImportDialog`  |

### 3.7 grant_table

| 功能                          | 后端   | 前端   |
| --------------------------- | ---- | ---- |
| **grant_table**             | ⚠️部分 | ⚠️部分 |
| ├── DEPT / POST / USER 分配角色 | ✅完成  | ✅完成  |
| ├── 角色页授权对象维护               | ❌未完成 | ❌未完成 |
| └── GRANT 失效 Trigger        | ✅完成  |—|

### 3.8 数据范围

| 功能                                | 后端     | 前端     |
| ----------------------------------- | -------- | -------- |
| **role_scope 管理**                 | ❌未完成 | ❌未完成 |
| **user_scope 管理**                 | ❌未完成 | ❌未完成 |

### 3.9 service-system-schedule

| 功能                                    | 后端   | 前端   |                                                                                                                |
| --------------------------------------- | ------ | ------ | -------------------------------------------------------------------------------------------------------------- |
| **调度任务**                            | ✅完成 | ✅完成 |                                                                                                                |
| ├── CRUD / 启停 / 处理器目录 / 日志     | ✅完成 | ✅完成 |                                                                                                                |
| ├── 断路器 / 启动同步 / SysJobException | ✅完成 |—|                                                                                                                |
| ├── 消息调度任务类型                    | ✅完成 |—|                                                                                                                |
| ├── HTTP 内置调用（`HttpInvokeJob`）| ✅完成 |—| `HttpInvokeJobParams.method` 支持 GET/POST/PUT/DELETE                                                          |
| ├── Feign 内置调用（`FeignInvokeJob`）| ✅完成 |—| `serviceName` + `path` + `method` + headers/body；经 `loadBalancedRestTemplate` + 内部 JWT，已支持微服务间调用 |
| ├── job_params 示例模板                 | ✅完成 | ✅完成 | 后端 `@QuartzTask.jobParamsExample` + catalog API；前端 JSON 编辑器 +「填入示例」|
| ├── Quartz 启停竞态                     | ✅完成 |—| `SysJobBootstrapSynchronizer` / `SysJobScheduleReconciler` 缓解 DB 与调度器短暂不一致                          |
| └── 历史清理 Job 改 cron                | ✅完成 |—| 授权失效 Outbox/Event 清理、文件回收站清理均改为可配置 cron                                                    |
| **调度任务分组**                        | ✅完成 | ✅完成 |                                                                                                                |

### 3.10 service-system-message

| 功能                          | 后端   | 前端   |                                                              |
| --------------------------- | ---- | ---- | ------------------------------------------------------------ |
| **统一消息发送**                  | ✅完成  |—| `MessageSender` SPI；EMAIL / DING_TALK / SMS / IN_APP           |
| ├── EMAIL / DING_TALK / SMS | ✅完成  |—| SMS 当前阿里云 Dypns                                              |
| └── IN_APP                  | ✅完成  |—| `InAppMessageSender`；compose 提交后异步派发                         |
| **邮件模板**                    | ✅完成  | ✅完成  | `message_template`，`channel=EMAIL`；`message/email-template` |
| **消息模板（多渠道）**               | ✅完成  | ✅完成  | 统一表按 channel；独立分页管理，不建统一模板页 / 不做钉钉模板页                        |
| ├── SMS 模板管理                | ✅完成  | ✅完成  | `message/sms-template`                                       |
| └── 站内信模板                   | ✅完成  | ✅完成  | 纯文本 / Markdown；预览与测试发送；`message/in-app-template`             |
| **站内信发送任务**                 | ✅完成  | ✅完成  | `message_send_task`；分页/详情/发送/重试/撤回/删除；`message/in-app-message` + `compose` |
| ├── 收件人分页（已读/软删）| ✅完成  | ✅完成  | `GET.../tasks/{taskId}/recipients`；`InAppSendTaskRecipientDrawer`（可筛 isRead）|
| **渠道投递记录**                  | ✅完成  | ✅完成  | `message_channel_delivery`；`message/send-record`             |
| **站内信业务分类**                 | ✅完成  | ✅完成  | 大类/小类字典 CRUD / 批量启停；发送页必填；`message/in-app-category`           |
| **我的消息（收件箱）**               | ✅完成  | ✅完成  | `home/inbox`；`InAppInboxController`（me）|
| ├── 分页（未读优先）/ 顶栏铃铛角标         | ✅完成  | ✅完成  | `useInAppInboxStore`                                          |
| ├── 详情（打开即标已读）| ✅完成  | ✅完成  | `GET.../inbox/{messageId}`；`InboxMessageDetailView`         |
| ├── 批量标已读 / 大类全部已读           | ✅完成  | ✅完成  | `POST.../inbox/read`、`.../read-all`                         |
| └── 用户侧逻辑删除 / 大类全部删除         | ✅完成  | ✅完成  | `DELETE.../inbox`、`.../inbox/all`；入参 messageId 数组         |

### 3.11 文件管理

| 功能                      | 后端   | 前端   | 说明                                                                       |
| ----------------------- | ---- | ---- | ------------------------------------------------------------------------ |
| **Excel 导出**            | ✅完成  | ✅完成  |                                                                          |
| **Excel 导入（dept/post）** | ✅完成  | ✅完成  |                                                                          |
| **文件上传**                | ✅完成  | ✅完成  | `FileUploadController`；`bizType`：`avatar` / `attachment` / `internal_export`；前端 `AvatarEditor` |
| ├── 单文件 / 多文件上传         | ✅完成  | ✅完成  | `bizType`：`avatar` / `attachment` / `internal_export`；前端 `AvatarEditor`  |
| ├── 格式与大小校验             | ✅完成  | ✅完成  | 后端 `FileUploadContentValidator`；前端 `validateUploadFile`                 |
| ├── 私有桶 + 预签名预览         | ✅完成  | ✅完成  | `FileUrlSigner`；详情 `accessUrl`（默认 300s）|
| ├── 逻辑删除后资源不可访问         | ✅完成  |—| 软删进回收站；公文件物理删                                                            |
| **文件治理（管理端）**           | ✅完成  | ✅完成  | `file/record/index.vue`                                                  |
| ├── 分页查询 / 时间·类型筛选      | ✅完成  | ✅完成  | `GET /api/system/file/page`                                              |
| ├── 详情 / 图片预览           | ✅完成  | ✅完成  | `FileRecordDescriptionDialog`                                            |
| ├── 批量删除                | ✅完成  | ✅完成  | `@OperationLog` `FILE_RECORD`                                            |
| ├── 下载 / 批量下载（ZIP）| ✅完成  | ✅完成  | `@OperationLog` `FILE_RECORD_DOWNLOAD`                                   |
| ├── 公私有切换               | ✅完成  | ✅完成  | `sys:file:privacy`；`@OperationLog` `FILE_RECORD_PRIVACY`                 |
| **回收站（管理端）**            | ✅完成  | ✅完成  | `file/recycle/index.vue`                                                 |
| ├── 分页 / 详情 / 恢复 / 物理删除 | ✅完成  | ✅完成  | `@OperationLog` `FILE_RECYCLE_RESTORE` / `FILE_RECYCLE_PURGE`            |
| **个人文件**                | ✅完成  | ✅完成  | `file/me/record/index.vue`                                               |
| ├── 分页 / 筛选 / 详情 / 预览   | ✅完成  | ✅完成  | `PersonalFileRecordController`                                           |
| ├── 下载 / 批量下载 / 删除      | ✅完成  | ✅完成  | 按当前用户 `owner_id` 隔离                                                      |
| **个人回收站**               | ✅完成  | ✅完成  | `file/me/recycle/index.vue`；仅 `USER_SELF` 来源                             |
| ├── 恢复 / 物理删除           | ✅完成  | ✅完成  |                                                                          |
| **头像**                  | ✅完成  | ✅完成  | 用户 `PUT /me/avatar`；管理员 `PUT /user/avatar`；旧头像 `OldAvatarCleanupTrigger` |
| **共享 UI 基础设施**          |—| ✅完成  | `_shared` Hook / Columns / `DescriptionDialog`                           |
| **`file_record` 扩展字段**  | ✅完成  |—| `is_private`、`deleted_at`、`deleted_by`、`delete_source`                   |
| **回收站定时物理清理**           | ✅完成  |—| `FileRecycleCleanupJob`；`auth.file.recycle-cleanup.cron`（默认每日 03:00）|
| **上传操作审计**              | ✅完成  |—| `FileUploadController` `@OperationLog` `FILE_UPLOAD` / `FILE_UPLOAD_MULTIPLE` |

### 3.12 日志管理

| 功能                                     | 后端   | 前端   |
| ---------------------------------------- | ------ | ------ |
| 登录 / 操作 / 授权审计 / 调度 / 密码历史 | ✅完成 | ✅完成 |

### 3.13 授权失效运维

| 功能                            | 后端  | 前端  |                                                      |
| ----------------------------- | --- | --- | ---------------------------------------------------- |
| **授权失效运维**                    | ✅完成 | ✅完成 |                                                      |
| ├── Outbox / Event 分页详情       | ✅完成 | ✅完成 |                                                      |
| ├── DEAD/FAILED/PROCESSING 重试 | ✅完成 | ✅完成 |                                                      |
| ├── releaseClaim / 统计摘要       | ✅完成 | ✅完成 |                                                      |
| ├── eventId 互跳                |—| ✅完成 |                                                      |
| ├── 失败率时序统计                   | ✅完成 |—| `AuthorizationInvalidationStatsOpsController`；按日/周聚合 |
| └── 失败率 ECharts               |—| ✅完成 | `AuthorizationInvalidationFailureRateTrendCard`      |

路径：`ops/authorization-invalidation/index.vue`

### 3.14 用户偏好配置

| 功能                     | 后端     | 前端     |
| ------------------------ | -------- | -------- |
| **用户偏好配置**         | ✅完成   | ✅完成   |
| ├── `sys_user_config` 表 | ✅完成   |—| `user_id + config_key + config_value(JSON)`                            |
| ├── 偏好读写 API         | ✅完成   |—| `CurrentUserPreferenceController` `GET/PUT/DELETE /me/preferences`     |
| └── 布局配置同步         | ✅完成   | ✅完成   | `core/preferences/sync.ts`；登录 hydrate + debounce upsert               |

## 四、auth-web

| 能力                          | 状态  |                                                                |
| --------------------------- | --- | -------------------------------------------------------------- |
| 登录页                         | ✅完成 |                                                                |
| 动态路由 / 路由守卫                 | ✅完成 |                                                                |
| 布局 / 分页 / AssignManagePanel | ✅完成 |                                                                |
| Excel 导入共享 Hook             | ✅完成 |                                                                |
| 欢迎页                         | ✅完成 |                                                                |
| 顶栏用户下拉 / 个人中心入口             | ✅完成 | `useAccountNavigation`；顶栏展示主部门 `primaryDeptName`               |
| 会话水印                         |—| ✅完成 | `SessionWatermark`：主部门 + 用户名                                  |
| 按钮级权限（`v-perms`）|—| ✅完成                                                            |

### 业务页面（路径相对 `auth-web/src/views/`）

| 页面                           | 后端   | 前端   | 说明                                                                                           |
| ---------------------------- | ---- | ---- | -------------------------------------------------------------------------------------------- |
| 菜单管理                         | ✅完成  | ✅完成  | `system/menu/index.vue`；含绑定角色查询弹窗（无菜单侧按钮权限）|
| 用户管理                         | ✅完成  | ✅完成  | `system/user/index.vue`                                                                      |
| 部门管理                         | ✅完成  | ✅完成  | `system/dept/index.vue`                                                                      |
| 岗位管理                         | ✅完成  | ✅完成  | `system/post/index.vue`                                                                      |
| 权限管理                         | ✅完成  | ✅完成  | `system/permission/index.vue`                                                                |
| 角色管理                         | ⚠️部分 | ⚠️部分 | `system/role/index.vue`；1. 数据范围 2. 授权对象 3. 影响分析                                              |
| 调度任务                         | ✅完成  | ✅完成  | `schedule/schedule-task/index.vue`                                                           |
| 调度分组                         | ✅完成  | ✅完成  | `schedule/schedule-group/index.vue`                                                          |
| 邮件模板                         | ✅完成  | ✅完成  | `message/email-template/index.vue`                                                       |
| 短信模板                         | ✅完成  | ✅完成  | `message/sms-template/index.vue`                                                         |
| 站内信模板                        | ✅完成  | ✅完成  | `message/in-app-template/index.vue`                                                      |
| 站内信发送任务                      | ✅完成  | ✅完成  | `message/in-app-message` + `compose`；收件人抽屉 `InAppSendTaskRecipientDrawer` |
| 站内信业务分类                      | ✅完成  | ✅完成  | `message/in-app-category/index.vue`                                                      |
| 消息发送记录                       | ✅完成  | ✅完成  | `message/send-record/index.vue`（`message_channel_delivery`）|
| 我的消息                         | ✅完成  | ✅完成  | `home/inbox`：列表/详情/标已读/全部已读/删除；顶栏角标                                                     |
| 授权失效运维                       | ✅完成  | ✅完成  | `ops/authorization-invalidation/index.vue` + `AuthorizationInvalidationFailureRateTrendCard` |
| 登录 / 操作 / 授权审计 / 调度 / 密码历史日志 | ✅完成  | ✅完成  | `log/*/index.vue`                                                                            |
| 在线用户                         | ✅完成  | ✅完成  | `system/online-user/index.vue`                                                               |
| 数据权限配置                       | ❌未完成 | ❌未完成 | `system/data-scope/index.vue`                                                                |
| 文件记录                         | ✅完成  | ✅完成  | `file/record/index.vue`                                                                      |
| 文件回收站                        | ✅完成  | ✅完成  | `file/recycle/index.vue`                                                                     |
| 我的文件                         | ✅完成  | ✅完成  | `file/me/record/index.vue`                                                                   |
| 我的文件回收站                      | ✅完成  | ✅完成  | `file/me/recycle/index.vue`                                                                  |
| 个人中心                         | ✅完成  | ✅完成  | `account/profile` + `login-log`；资料含主部门                                           |

## 五、数据库

| 项                  | 状态   | 说明                                                     |
| ------------------ | ---- | ------------------------------------------------------ |
| 角色删除子表清理           | ✅完成  | 主表删除 + DB CASCADE                                      |
| SUCCESS 失效记录归档     | ✅完成  | Outbox + Event 双 Job，默认 90 天                           |
| `role_type` 字段删除   | ✅完成  | 增量脚本 `db/system/V20260618_drop_sys_role_role_type.sql` |
| `file_record` 治理字段 | ✅完成  | `is_private`、`deleted_at`、`deleted_by`、`delete_source` |
| `sys_user_config`  | ✅完成  | 用户 UI 偏好 KV 存储；`CurrentUserPreferenceController` + 前端 `sync.ts` |

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
| ROLE       | ✅                  | ✅             | ✅完成   |
| PERMISSION | ✅                  | ✅             | ✅完成   |
| GRANT      | ✅                  | ✅             | ✅完成   |
| USER       | ✅                  | ✅             | ✅完成   |
| USER_DEPT  | ✅                  | ✅             | ✅完成   |
| USER_POST  | ✅                  | ✅             | ✅完成   |

### 6.3 可观测性

| 功能                                  | 状态     | 说明 |
| ------------------------------------- | -------- | ---- |
| 运维统计 / releaseClaim / Outbox 补偿 | ✅完成   |      |
| 失败率时序统计 API                    | ✅完成   | `AuthorizationInvalidationStatsOpsController` |
| 失败率 ECharts 趋势图                 | ✅完成   | `AuthorizationInvalidationFailureRateTrendCard` |

## 变更记录

| 日期         | 说明                                                                                                                                   |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 2026-06-26 | 删除误写的 §1.1「内部 audit-user Feign」待办（无对应实现项）|
| 2026-06-26 | 评审收口：影响面刷新分批、会话懒清理、auth 调用链收敛、用户授权详情、失败率时序 API + ECharts 标 ✅；删除占位菜单隐藏与「集成测试（正向 ⊆ 反向）」待办；同步缺口速览与推荐优先级                                 |
| 2026-06-26 | 对照代码库刷新个人中心完成状态（我的会话 / 登录日志 / 页面 / 顶栏入口）；新增待办：个人信息自编辑、LICENSE 统一                                                                     |
| 2026-06-26 | 按钮权限架构定稿：`hasAuth` 仅 `user.permissions`；删除 `sys_menu_permission` / `meta.auths`；菜单仅分配角色                                              |
| 2026-06-17 | 按代码现状校正：角色详情、用户批量分批、`v-perms`、调度 `job_params` 示例、Quartz 竞态标完成；删除 reconcile Job、DDL 快照、Smoke、删头像等无效待办                                 |
| 2026-06-18 | 对照代码库刷新完成状态：`roleType` 收敛、菜单绑定查询、部门/岗位/权限详情、HTTP/Feign 调度任务、`v-perms` 挂接、改密 API；原 §352–372 游离项并入正文；推荐优先级改为分层索引                       |
| 2026-06-17 | 将文档底部游离待办并入各章节表格：HTTP/Feign 内置任务、job_params 编辑器、按钮权限、`roleType` 收敛、僵尸会话清理、调用链收敛、影响面分批、Quartz 竞态                                      |
| 2026-06-13 | 补充待办：用户授权详情、个人中心、统一消息模板、`sys_user_config`、授权失效 ECharts；调整推荐优先级                                                                       |
| 2026-06-06 | 全文重构 + §3.13 授权失效运维                                                                                                                  |
| 2026-05-31 | 角色分配权限、权限 CRUD、两表说明                                                                                                                  |
| 2026-07-09 | 对照代码库刷新文件管理：上传/治理/回收站/个人文件/头像标 ✅；私有桶预签名预览、操作日志、共享 UI 基础设施标完成；§3.11 展开明细；待补：分享、回收站定时清理、上传审计、权限整体导出导入、|
| 2026-07-11 | 游离完成项并入正文：Docker 本地启动脚本、回收站定时清理、清理 Job 改 cron、上传 `@OperationLog`；删除「文件分享」/`file_share` 待办（私有桶访问由 `FileUrlSigner` 预签名覆盖）；同步缺口速览与推荐优先级 |
| 2026-07-11 | 删除「通用文件上传页」「权限整体导出 / 初始化导入」待办（无此功能）；文件管理标 ✅ |
| 2026-07-21 | 对照代码库刷新消息域：IN_APP Sender、短信（阿里云）、SMS/站内信模板、发送任务、渠道投递、业务分类标 ✅；通知中心收敛为「我的消息」⚠️；取消统一模板页与钉钉模板页；文件上传格式/大小校验、角色/权限/部门/岗位批量启停并入正文；同步前后端 TODO |
| 2026-07-23 | 我的消息收件箱标 ✅；站内信任务收件人分页并入正文；顶栏主部门 + 会话水印并入 §四；清理 NEW TODO / 问题已完成项 |

### NEW TODO

- [ ] 文件异步导出
- [ ] 删 `policyBind` 菜单项 + i18n
- [ ] `DataScopeSqlHelper` + 单测
- [ ] 数据范围：`role_scope` CRUD API + 角色页抽屉
- [ ] 授权对象：角色视角 replace API + 抽屉（复用现有选人/选部门/选岗位组件）
- [ ] 影响分析：基于现有 Impact 查询做「变更前预览」API + 简单列表 UI
- [ ] `user_scope` + 独立 `data-scope` 配置页

## 问题

（暂无）
