### 挂载 ip2region 后服务内存偏高 / OOM 重启

当前实现会把 xdb **整文件读进 JVM 堆**（`loadContentFromFile`），堆占用大致跟文件大小同量级。每个启用解析的进程各加载一份；auth / system / gateway 都开 IPv6 时，按「文件大小 × 实例数」估算。

开源库体量（以 [ip2region data](https://github.com/lionsoul2014/ip2region/tree/master/data) 实际下载为准，会随官网更新）：

- `ip2region_v4.xdb`：约 11MB
- `ip2region_v6.xdb`：约 35MB

换更大的商用/自建 xdb，内存按文件大小线性增加。旧版超大 IPv6 库或满载商用库仍可能把小容器打爆。

**处理**：

1. 默认推荐：Nacos 保持 `auth.common.ip-tool.resolve-ipv6-enabled=false`，只挂/只用 v4
2. 必须解析 IPv6：核对 xdb 体积，给容器留足堆；多服务都开就按文件大小乘实例数留余量
3. 长期若要用超大库：改成 mmap/文件搜索，而不是整库进堆
