---
formatter: markdown
formatter_version: 1
---
# 限界上下文

| 上下文 | 责任 | 主要实现 |
|---|---|---|
| Proxy Runtime | 连接接入、协议处理、路由执行 | `crates/agentgateway`, `crates/http` |
| LLM Connectivity | provider、model、重试、流式响应和推理路由 | `crates/llm`, `crates/agentgateway/src/llm` |
| MCP Connectivity | tool/resource/prompt、传输和聚合 | `crates/agentgateway/src/mcp` |
| Policy & Security | CEL、认证、授权、TLS、限流、guardrail | `crates/policy-core`, `crates/celx` |
| Control Plane | Kubernetes watch、状态、xDS 资源编排 | `controller/`, `crates/xds`, `crates/protos` |
| Operations & UI | 管理接口、UI、日志、指标、追踪 | `ui/`, `crates/agentgateway` |

上下文之间通过配置模型、IR、xDS/protobuf 与 HTTP/MCP/A2A 协议协作。官方 Introduction 将这种组合概括为统一数据面：同一个 gateway 同时承载普通 API 和 Agentic AI 流量；MCP 上下文还需要额外的 session、message、fan-out、tool federation 和按 client 的授权视图。
