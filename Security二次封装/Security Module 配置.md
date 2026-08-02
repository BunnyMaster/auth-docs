> 本文档描述 Security 模块相关的 **Nacos / `application.yml` 配置项** 与 **授权审计 SPI 接入点**。
>
> 架构流程、Redis Key、内部调用等见《[[Security Module 设想]]》。JWT 模块细节见《[[JWT说明（common-starter-jwt）]]》。

## 配置总览

| 配置前缀 | 配置类 | `@RefreshScope` | 主要消费方 |
| -------- | ------ | --------------- | ---------- |
| `auth.common.security` | `SecurityConfigProperties` | 是 | 资源服务（`module-security-autoconfigure`） |
| `auth.common.user-config` | `UserConfigProperties` | 是 | service-auth（登录失败、会话上限）；资源服务（并发会话 SCARD） |
| `auth.common.jwt` | `JwtProperties` | **否** | 网关、service-auth、资源服务（`common-starter-jwt`） |
| `auth.gateway.security` | `GatewaySecurityProperties` | 是 | auth-gateway |

> [!NOTE]
>
> - `auth.common.security` / `auth.common.user-config` / `auth.gateway.security` 支持 Nacos 热刷新；`auth.common.jwt` **变更后需重启** 生效。
> - 用户配置在代码注释中建议 Nacos 文件名为 `common-user-config.yaml`、分组 `AUTH_COMMON`（见 `UserConfigProperties`）。
> - 消息渠道（`auth.message.email` / `sms` / `ding-talk`）、定时任务（`auth.schedule.job`）等**不属于** Security 模块，不在本文展开。

## `auth.common.security`

对应 `com.auth.module.security.autoconfigure.config.security.SecurityConfigProperties`，控制资源服务侧的**免认证路径**与**授权决策审计策略**。

### HTTP 行为前提

> [!IMPORTANT]
>
> - **默认需认证**：未命中免认证规则且无有效 Token 时返回 401。
> - **注解优先于路径**：`@PublicApi` / `@AuthenticatedApi` / `@InternalApi` 由 `SecurityRequirementResolver` 解析，写入请求属性 `SecurityRequirement`；`AnnotationOverridingAuthorizationManager` 优先按注解决策，仅当回落为 `FALLBACK_TO_PATH` 时才看 `permit-paths`。
> - **公开接口优先用注解**：service-auth 登录/刷新等接口已标注 `@PublicApi`，**不依赖** `permit-paths`；`permit-paths` 适用于无法加注解的场景（如 Actuator、静态资源、第三方回调）。
> - **网关独立配置**：网关**无法识别** `@PublicApi`，须单独配置 `auth.gateway.security.*`（见下文）。

### 示例

```yaml
auth:
  common:
    security:
      # 免认证路径（Ant 风格）；默认 []
      permit-paths:
        - /actuator/health
        - /v3/api-docs/**
      # 授权决策审计策略（默认 ALL_RECORD）
      audit-policy: ALL_RECORD
```

### 字段说明

| 配置键（kebab-case） | Java 字段       | 类型             | 默认值          | 说明                                                                                   |
| --------------- | ------------- | -------------- | ------------ | ------------------------------------------------------------------------------------ |
| `permit-paths`  | `permitPaths` | `List<String>` | `[]`         | 命中后允许匿名访问；由 `AnnotationOverridingAuthorizationManager` 在注解回落为 `FALLBACK_TO_PATH` 时生效 |
| `audit-policy`  | `auditPolicy` | `AuditPolicy`  | `ALL_RECORD` | 控制 `@auth.decide()` **授权决策**是否发布审计事件（见下文）                                            |

### 权限版本不匹配

`VersionMismatchStrategy`（`REQUIRE_REFRESH` / `DENY` / `FORCE_REAUTH`）**尚未实现**。当前硬编码：JWT 中 `permVersion` 与 Redis `AuthProfile.permVersion` 不一致时，资源服务返回 **409**（`PERMISSION_VERSION_MISMATCH`）。见 `ExternalRequestAuthenticator.assertPermVersionConsistent()` 与《[[Security Module 设想]]》访问流程。

### `AuditPolicy` 枚举

| 枚举值 | 含义 |
| ------ | ---- |
| `NONE` | 关闭授权决策审计 |
| `PUBLIC_NO_RECORD` | 当前请求被标记为 `SecurityRequirement.PUBLIC`（如 `@PublicApi`）时不记录；其余记录 |
| `PUBLIC_WITH_TOKEN_RECORD` | 在 `decide()` 链路中等价于全量记录（能进入 `decide()` 说明请求已携带 Token） |
| `ALL_RECORD` | 凡触发 `decide()` 均记录 |

> [!NOTE]
>
> `audit-policy` **仅**作用于 `PermissionService.decide()`（即 `@PreAuthorize("@auth.decide('...')")` 触发的**功能权限决策**），**不**覆盖登录审计（`log_login`）或操作日志。公开接口若无 `@PreAuthorize`，不会进入 `decide()`，因此不受此策略影响。
>
> 「公开接口」语义应与 `@PublicApi` 或 `permit-paths` 保持一致，避免各模块对 public 理解歧义。

## `auth.common.user-config`

对应 `com.auth.module.security.autoconfigure.config.user.UserConfigProperties`（`@Validated`），主要被 **service-auth** 消费；`max-session-count` 亦被资源服务 `SessionCountChecker` 使用。

### 示例

```yaml
auth:
  common:
    user-config:
      max-password-attempts: 6
      max-session-count: 3
      session-limit-strategy: EVICT_OLDEST
      # 以下字段已在 Properties 中定义，当前版本尚无业务消费方（预留）
      password-history-retention-days: 30
      password-history-check-count: 5
```

### 字段说明

| 配置键 | Java 字段 | 类型 | 默认值 | 校验范围 | 当前消费方 |
| ------ | --------- | ---- | ------ | -------- | ---------- |
| `max-password-attempts` | `maxPasswordAttempts` | `Integer` | `6` | 3–30 | `LoginFailureRateLimiter` ↔ Redis `LOGIN_FAILURE_COUNT` |
| `max-session-count` | `maxSessionCount` | `Integer` | `3` | 1–100 | 见下表 |
| `session-limit-strategy` | `sessionLimitStrategy` | `SessionLimitStrategy` | `EVICT_OLDEST` | — | **仅登录时** `SessionLimitGuard` |
| `password-history-retention-days` | `passwordHistoryRetentionDays` | `Integer` | `30` | 1–365 | **未接入** |
| `password-history-check-count` | `passwordHistoryCheckCount` | `Integer` | `5` | 1–20 | **未接入** |

### `max-session-count` 的两处语义

| 阶段 | 类 | 行为 |
| ---- | -- | ---- |
| **登录成功时** | `SessionLimitGuard`（service-auth） | 结合 `session-limit-strategy`：`EVICT_OLDEST` 踢最旧会话后放行，`REJECT_LOGIN` 拒绝本次登录（403 `MAX_SESSION_LIMIT_EXCEEDED`） |
| **已登录请求时** | `SessionCountChecker`（资源服务） | 对 `USER_SESSIONS` 做 SCARD；超出 `max-session-count` 直接返回 401（`SESSION_LIMIT_EXCEEDED`），**不**执行踢人策略 |

资源侧会话校验顺序（见《[[Security Module 设想]]》访问流程）：先 `EXISTS :user:session:{jti}`，再加载 `AuthProfile` 比对 `permVersion`，再 `SISMEMBER` / `SCARD` `:user:sessions:{userId}`。

### `SessionLimitStrategy` 枚举

| 枚举值 | 含义 |
| ------ | ---- |
| `EVICT_OLDEST` | 达上限时踢出最旧活跃会话（默认） |
| `REJECT_LOGIN` | 达上限时拒绝本次登录 |

## `auth.common.jwt`

对应 `com.auth.common.jwt.autoconfigure.JwtProperties`（`common-starter-jwt`）。

> [!IMPORTANT]
>
> - 本 Starter **只**提供签名/验签/解析；`access-expired` / `refresh-expired` **不会**自动写入 JWT，须由 **service-auth**（如 `TokenService`）在组装 `JwtBuilder` 时使用。
> - 无 `@RefreshScope`，配置变更需**重启**服务。
> - 字段校验、RS256 keystore 等完整说明见《[[JWT说明（common-starter-jwt）]]》「怎么配」。

### 示例（HS256）

```yaml
auth:
  common:
    jwt:
      issuer: auth-service
      secret: your-256-bit-secret-here-must-be-at-least-32-bytes
      access-expired: 3600
      refresh-expired: 604800
      clock-skew-seconds: 30
      algorithm: HS256
```

### 字段摘要

| 配置键 | 默认值 | 说明 |
| ------ | ------ | ---- |
| `issuer` | （必填） | 解析时 `requireIssuer` |
| `secret` | HS256 必填 | UTF-8 至少 32 字节 |
| `access-expired` | `3600` | 业务侧推导 Access Token `exp` |
| `refresh-expired` | `604800` | 须大于 `access-expired`；业务侧推导 Refresh Token `exp` |
| `clock-skew-seconds` | `30` | JJWT 解析时钟偏移 |
| `algorithm` | `HS256` | `HS256` 或 `RS256` |
| `keystore-path` / `password` / `alias` / `auto-generate` | — | RS256 专用，见 JWT 说明 |

## `auth.gateway.security`

对应 `com.auth.gateway.config.GatewaySecurityProperties`，控制网关在**缺 Token / Token 无效**时是否直接 401。

> [!IMPORTANT]
>
> 网关只做 JWT 轻量校验（验签、类型、可解析），**无法识别**下游 `@PublicApi`。下游公开接口若要从网关放行「无 Token」，不要把对应路径配进严格模式；严格路径在无/坏 Token 时直接 401。

### 示例

```yaml
auth:
  gateway:
    security:
      strict-enabled: true
      strict-patterns:
        - /api/system/**
        - /api/admin/**
```

### 字段说明

| 配置键 | Java 字段 | 类型 | 默认值 | 说明 |
| ------ | --------- | ---- | ------ | ---- |
| `strict-enabled` | `strictEnabled` | `boolean` | `false` | 总开关；为 `false` 时不按路径强制 401 |
| `strict-patterns` | `strictPatterns` | `List<String>` | `[]` | Ant 风格；`strict-enabled=true` 且路径命中时，缺 Token / Token 无效直接 401 |

## 授权审计 SPI

当 `@PreAuthorize("@auth.decide('...')")` 执行时，`PermissionService.decide()` 按 `audit-policy` 决定是否发布 `SecurityAuthorizationAuditPayloadEvent`，并委托所有已注册的 `AuthorizationAuditHandler`。

### 接入方式

接口：`com.auth.module.security.contract.spi.AuthorizationAuditHandler`

```java
@Component
public class MyAuditHandler implements AuthorizationAuditHandler {

    @Async  // 建议异步，不阻塞主请求
    @Override
    public void handle(SecurityAuthorizationAuditPayloadEvent event) {
        // 持久化至 DB / ES / 日志等
    }
}
```

| 组件 | 位置 |
| ---- | ---- |
| SPI 接口 | `module-security-contract` → `AuthorizationAuditHandler` |
| 空实现 | `module-security-autoconfigure` → `NoopAuthorizationAuditHandler`（`@ConditionalOnMissingBean`） |
| 现有实现 | service-auth → `LoggingAuthorizationAuditHandler`（Feign 转发 system）；service-system-admin → `AuthAuditHandler`（写 `log_authorization_audit`） |

未注册任何实现时，`NoopAuthorizationAuditHandler` 启动时输出一条 info 提示；事件仍经 `ApplicationEventPublisher` 发布。

---

## 与架构文档的对应关系

| 架构概念（《[[Security Module 设想]]》） | 本配置项 |
| ------------------------------------------ | -------- |
| 注解优先 / `@PublicApi` 放行 | 不配置 `permit-paths` 亦可；注解由代码声明 |
| `maxSessionCount` + `USER_SESSIONS` SCARD | `auth.common.user-config.max-session-count` |
| 登录时会话踢人 / 拒绝 | `auth.common.user-config.session-limit-strategy`（`SessionLimitGuard`） |
| 登录失败锁定 | `auth.common.user-config.max-password-attempts` |
| `permVersion` 409 | 无配置项（硬编码）；未来或引入 `VersionMismatchStrategy` |
| Access / Refresh 过期 | `auth.common.jwt.access-expired` / `refresh-expired`（业务写入 claim） |
| 网关严格路径 | `auth.gateway.security.strict-enabled` / `strict-patterns` |
| 超级管理员 / ADMIN 短路 | **非配置**：`PermissionConstant`（契约层常量，发版变更） |

> [!IMPORTANT]
>
> `permit-paths` 只解决「是否需登录」边界；**数据权限**（`@DataScope` / SQL 行级过滤）不因路径或 `@PublicApi` 豁免。详见《[[数据权限设计]]》。
