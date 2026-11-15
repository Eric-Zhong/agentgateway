---
formatter: markdown
formatter_version: 1
---
# 待确认问题

以下内容当前仓库没有足够证据，后续应由维护者确认：

- 生产支持的 Kubernetes、Gateway API、Rust/Go/Node 精确版本矩阵。
- 数据面吞吐、P99 延迟、并发连接、RTO/RPO 和容量基线。
- 多租户/跨 namespace 的正式授权模型与隔离承诺。
- 各 provider/MCP/A2A adapter 的长期兼容策略和错误语义。
- 遥测指标命名、保留期、告警阈值和敏感数据脱敏规范。
- release 是否要求 SBOM、provenance、CVE 门禁及其责任人。

这些问题不应在没有确认前写成硬性产品承诺。
