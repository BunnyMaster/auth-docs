## Ip2region

登录归属地、会话 IP 地域要用离线 xdb。Docker 把宿主机目录挂到容器 `/opt/ipdb`（`AUTH_IPDB_HOST_PATH`，默认 `/opt/ipdb`）。

xdb **不在**本知识库里。开箱用的文件在 [auth-server Releases](https://github.com/BunnyMaster/auth-server/releases) 的附件包中（常见路径：`ipdb/`，以当次 Release 为准）。

> [!IMPORTANT]
>
> - Release **只附带 IPv4**（`ip2region_v4.xdb`），方便本地先跑起来。
> - 包内文件**仅供上手**；上游会更新，**以官网最新为准**，生产建议自行核对后再覆盖。

### 官网与下载

| 来源                    | 地址                                                         |
| --------------------- | ---------------------------------------------------------- |
| **本项目 Release（推荐开箱）** | https://github.com/BunnyMaster/auth-server/releases        |
| 官网                    | https://ip2region.net                                      |
| 开源数据目录                | https://github.com/lionsoul2014/ip2region/tree/master/data |

### 需要哪些文件

| 文件 | Release 是否附带 | 说明 |
| :--- | :--- | :--- |
| `ip2region_v4.xdb` | 是 | 开箱可用；可用官网最新覆盖 |
| `ip2region_v6.xdb` | 否 | 需要时自行从官网/上游仓库下载；默认不启用 |

体量大约：v4 ~11MB，v6 ~35MB（以官网实际文件为准）。

### 放置

从 Release 解压出 `ip2region_v4.xdb` 后放到宿主机挂载目录：

```bash
sudo mkdir -p /opt/ipdb
sudo cp /path/to/Release/ipdb/ip2region_v4.xdb /opt/ipdb/
# 可选：需要 IPv6 归属地时再从官网下载后放入
# sudo cp /path/to/ip2region_v6.xdb /opt/ipdb/
```

![image-20260730171326582](Ip2region-20260730171106.png)

Nacos / 配置路径需与挂载一致，例如：

```yaml
auth:
  common:
    ip-tool:
      resolve-external-enabled: true
      resolve-ipv6-enabled: false   # 默认关；开之前先看风险
      ipv4-db-path: /opt/ipdb/ip2region_v4.xdb
      ipv6-db-path: /opt/ipdb/ip2region_v6.xdb
```
