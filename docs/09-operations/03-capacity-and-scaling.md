---
formatter: markdown
formatter_version: 1
---
# 容量与扩缩容

容量评估至少考虑并发连接、流式响应、请求体/token、provider 限额、MCP 长连接、内存缓存、TLS/加密成本和遥测出口。Kubernetes 扩缩容应同时观察数据面与 controller，避免只扩大入口而把压力转移到下游或策略服务。
