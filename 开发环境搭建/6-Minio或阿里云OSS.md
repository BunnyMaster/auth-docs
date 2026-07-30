# MinIO 与阿里云 OSS 存储配置指南

本文档用于指导开发人员在开发与部署环境中选择并配置对象存储服务（MinIO / 阿里云 OSS），以及完成配置后的验证流程。

关于存储字段拆分与公开地址拼装规则，请参考 [[存储地址与配置]]。

## 1. 存储服务选型建议

根据不同的应用场景，请参考下表选择合适的存储实现：

|**使用场景**|**推荐方案**|**说明**|
|---|---|---|
|**本机 / 虚拟机开发环境**|**MinIO**|推荐方案。部署在本地 Docker 环境，方便调试与离线开发。|
|**外网演示 / 预生产 / 需真实外网直链**|**阿里云 OSS**|适用于需要真实外网直连访问或贴近生产环境的场景。生产落地细节以部署文档为准，本文侧重配置切换。|

## 2. 本地开发环境：MinIO 配置（推荐）

### 2.1 服务信息

若已根据 [[3-中间件-Compose]] 完成本地服务启动，MinIO 默认连接信息如下（以 [auth-server Releases](https://github.com/BunnyMaster/auth-server/releases) 中 `docker/docker-compose.yaml` 为准）：

- **API 服务地址**：`http://<宿主机IP>:9000`
- **控制台（Console）地址**：`http://<宿主机IP>:9090`
- **控制台登录账号**：`admin` / `Dev1234!`（Compose 中的 `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD`）

> **说明**：上述账号仅用于登录 MinIO **控制台**。写入 Nacos 的 `access-key` / `secret-key` 须为控制台中为服务创建的 **Access Key**，与 root 登录账号不是同一套凭证。

### 2.2 配置步骤

> [!WARNING]
> 若服务运行在 Docker 容器内部，需通过服务名进行通信，具体环境变量配置请参考 `auth-server/docker/env/service-system.env.example` 中的 `AUTH_FILE_MINIO_*` 配置项

1. 登录 MinIO 控制台，创建或确认分配给当前服务的 Access Key 与 Secret Key。
2. 登录 Nacos 控制台，修改 `service-system` 对应配置，确保 `minio` 节点信息准确：

```yaml
auth:
  file:
    default-platform: MINIO
    minio:
      endpoint: http://<你的宿主机IP>:9000   # 请替换为实际的宿主机 IP 地址
      access-key: <MinIO Access Key>        # 须与控制台中的 Access Key 一致（非 root 登录名）
      secret-key: <MinIO Secret Key>
      bucket: auth-files-dev
      # public-url: http://...              # 本机直连场景下通常无需配置
```

### 2.3 效果验证

1. 启动 `service-system` 服务。系统启动时会自动尝试创建指定的 Bucket（若已存在则自动跳过）。
2. 在应用中上传一张图片或头像，并在浏览器中直接访问返回的图片 URL。若能正常显示内容，则验证通过。

## 3. 阿里云 OSS 配置（演示 / 预生产 / 外网直链）

### 3.1 配置步骤

1. 修改 Nacos 配置中的存储平台类型为 `auth.file.default-platform: ALIYUN_OSS`。
2. 完善 `aliyun-oss` 配置节点，包括 `endpoint`、`bucket` 以及可选的 `public-url`。
3. **安全规范**：建议将 `AccessKey ID` 与 `AccessKey Secret` 注入为系统环境变量（`AUTH_FILE_ALIYUN_OSS_ACCESS_KEY_ID` / `AUTH_FILE_ALIYUN_OSS_ACCESS_KEY_SECRET`），切勿在 Nacos 配置中明文写入长期有效的密钥信息。

### 3.2 策略与权限说明

- 服务启动时会尝试**自动创建 Bucket**。
- 自动化工具**不会**自动配置 Bucket 的访问策略（Policy）、跨域规则（CORS）以及自定义域名映射。上述内容必须在阿里云 OSS 管理控制台手动配置完成。
- 遇到存储异常时，请优先排查阿里云 RAM 账号的权限策略以及 Endpoint 区域（Region）匹配情况。

相关字段骨架请参见 [[存储地址与配置]]。删除 Bucket 后立即重启报错的排查指引请参考 [[File自动创建桶失败]]。

## 4. 常见问题与注意事项

为避免开发与测试过程中遇到非预期错误，请特别注意以下问题：

- **避免配置无效 Endpoint**
  导入或复制 Nacos 配置后，务必检查并更新 `endpoint` 地址。指向无效或不可达的 IP 将导致文件上传直接失败。

- **确保密钥完全匹配**
  MinIO：应用中的 `access-key` / `secret-key` 必须与控制台创建的 Access Key 一致，**不要**误填控制台 root 登录名与密码。阿里云：须与 RAM 中生成的 AccessKey 严格一致，否则鉴权失败。

- **区分 Endpoint 与 CDN/公开域名**
  请勿将 CDN 节点或公开访问域名填入 `endpoint`。公开直链请使用 `public-url`。字段职责与推断规则详见 [[存储地址与配置]]。
