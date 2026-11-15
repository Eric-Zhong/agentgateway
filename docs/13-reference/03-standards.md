---
formatter: markdown
formatter_version: 1
---
# 标准与外部规范

项目直接涉及或参考：Gateway API、Kubernetes controller/API conventions、xDS transport、MCP、A2A、OpenTelemetry、OAuth/JWT、CEL、OCI/Docker、OpenVEX。官方 Introduction 将 MCP/A2A 描述为 JSON-RPC 协议，并以 session、长连接、双向消息、工具联邦和任务状态作为 AI gateway 与传统 HTTP gateway 的重要差异。标准版本与实现支持范围应以仓库锁定依赖、CRD、schema 和测试为准，不能仅由名称推断完整兼容性。

产品介绍来源：[agentgateway Standalone Introduction](https://agentgateway.dev/docs/standalone/latest/documentation/about/introduction/)。
