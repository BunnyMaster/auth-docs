## 中间件 Compose

本目录文件用途：

| 文件                       | 用途                                                        |
| -------------------------- | ----------------------------------------------------------- |
| `docker-compose.yaml`      | **本机 / 虚拟机开发**用中间件（MySQL、Redis、Nacos、MinIO）|
| `docker-compose-demo.yaml` | **演示服务器**用；带 `mem_limit`，适合小内存机              |

## 启动

```bash
# 本机 / 虚拟机开发
docker compose -f docker-compose.yaml up -d

# 演示服务器
# docker compose -f docker-compose-demo.yaml up -d
```
