# JWT

网关、鉴权、各业务服务都要签验 JWT。算法和密钥加载散着写，迟早各搞一套。这个 starter 收成一个 `JwtTokenProvider`：你组好 `JwtBuilder`，它负责签名；验签/解析走同一套密钥和 `issuer` 约束。

基于 JJWT 0.12；按 `auth.common.jwt.algorithm` 装 HS256 或 RS256。
**不做**：登录会话、多 Header 选 Token、`token_type` 路由、黑名单——那些在 `module-security-*` / 业务层。

## 怎么用

注入 `JwtTokenProvider`：

```java
// 签发：Claims / exp 自己定，starter 只 sign + compact
String token = jwtTokenProvider.generatorJwtToken(
    Jwts.builder()
        .subject(String.valueOf(userId))
        .issuer(jwtProperties.getIssuer())
        .id(jti)
        .expiration(exp)
        // ...
);

boolean ok = jwtTokenProvider.validateToken(token);           // 无效只返回 false，不抛
JwtUserToken user = jwtTokenProvider.parseToken(token);       // 验签失败抛 JwtException 子类
Claims claims = jwtTokenProvider.getClaims(token);            // 要自定义 claim 用这个
long left = jwtTokenProvider.getRemainingSeconds(token);      // 按 exp 与当前时间；不算 clock-skew
```

输入会 trim，并去掉 `Bearer ` 前缀（大小写不敏感）。这只是字符串规范化，**不是**网关级「从哪个 Header 取 Token」。

`parseToken` 要求 `sub` 是可解析成 `Long` 的 userId，否则 `InvalidTokenException`。映射字段：`userId` / `iss` / `sub` / `jti` / `audience`（`aud` 取第一个）。别的 claim 用 `getClaims`。

`access-expired` / `refresh-expired` **不会**自动写进 JWT；只给业务算 `exp` 用。两者必须 refresh > access，启动时校验。

## 怎么配

前缀 `auth.common.jwt`。YAML 里算法写大写 `HS256` / `RS256`。

### HS256（默认）

```yaml
auth:
  common:
    jwt:
      algorithm: HS256
      issuer: auth-service
      secret: "至少32字节的UTF-8密钥……………………"
      access-expired: 3600
      refresh-expired: 604800
      clock-skew-seconds: 30
```

### RS256

```yaml
auth:
  common:
    jwt:
      algorithm: RS256
      issuer: auth-service
      access-expired: 3600
      refresh-expired: 604800
      keystore-path: classpath:keystore/auth.p12   # 或 file:/path/auth.p12
      password: "change-me"
      alias: jwtkey
      auto-generate: false
```

| 配置 | 默认 | 说明 |
|------|------|------|
| `algorithm` | `HS256` | 未配也走 HS256 |
| `issuer` | 必填 | 解析器 `requireIssuer` |
| `secret` |—| HS256 必填，UTF-8 ≥ 32 字节 |
| `access-expired` / `refresh-expired` | 3600 / 604800 | 业务用；refresh 必须更大 |
| `clock-skew-seconds` | 30 | 只给 JJWT 验 `exp`；不影响 `getRemainingSeconds` |
| `keystore-path` / `password` / `alias` | alias=`jwtkey` | RS256 必填 path+password |
| `auto-generate` | `false` | 仅文件系统路径且文件不存在时生成 PKCS12；`classpath:` **不会**生成 |

联调要自动生成 keystore：用可写文件路径 + `auto-generate: true`。生产关掉。

## 边界与风险

- 调用方负责 `sub`/`exp`/`jti`/`aud` 等约定；starter 不保证配置里的过期秒数等于 token 里的 `exp`。
- `validateToken` 适合「探活」；需要区分过期/伪造时用 `parseToken` / `getClaims`，看 `JwtExpiredException`、`InvalidTokenException`、`JwtParseException`、`JwtKeyLoadException`。
- HTTP/`Result` 映射不在本模块，由网关或 MVC Advice 做。
- 多 Token 源、与 SecurityContext 集成：见上层 `module-security-contract` / `module-security-core`，本文不展开。
