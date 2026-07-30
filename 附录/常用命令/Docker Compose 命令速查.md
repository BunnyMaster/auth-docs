# Docker Compose 命令速查

优先用 Compose V2：`docker compose`。

中间件见 [[3-中间件-Compose]]；演示机见 [[附录/部署/演示机部署]]；本机源码启动见 [[8-本地启动前后端]]。

## Auth 项目

| 场景 | 命令 |
| ------ | ------ |
| 中间件 | `cd Auth/assets/Release/docker && docker compose -f docker-compose.yaml up -d` |
| 创建网络 | `docker network create auth-network` |
| 后端本地构建 | `cd auth-server/docker && ./dockerctl.sh dev build-up` |
| 后端拉 ACR | `export AUTH_IMAGE_PREFIX=... AUTH_IMAGE_TAG=prod-latest && ./dockerctl.sh prod pull && ./dockerctl.sh prod up` |
| 前端本地构建 | `cd auth-web/docker && export GATEWAY_HOST=auth-gateway && ./dockerctl.sh build-up` |
| 前端拉 ACR | 同上 PREFIX/TAG + `GATEWAY_HOST` 后 `./dockerctl.sh pull && ./dockerctl.sh up` |

镜像已更新但容器未变：先 `pull` 再 `up`。见 [[dockerctl速查]]。

## 通用

| 命令 | 作用 |
| ------ | ------ |
| `docker compose up -d` | 后台启动 |
| `docker compose down` | 停止并删容器（`-v` 会删命名卷）|
| `docker compose pull` | 只拉镜像 |
| `docker compose ps` | 状态 |
| `docker compose logs -f` | 跟踪日志 |
| `docker compose exec <service> sh` | 进容器 |
| `docker compose config` | 打印合并配置 |
