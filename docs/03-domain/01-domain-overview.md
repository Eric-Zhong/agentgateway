---
formatter: markdown
formatter_version: 1
---
# 领域总览

核心领域是“受治理的 AI 原生网络通信”。系统把入口连接映射到路由，再映射到后端能力，并在转发前后应用安全与治理策略。

## 核心概念

- Bind/Listener：网络入口和协议监听边界。
- Route：请求匹配、变换和后端选择规则。
- Backend：HTTP 服务、LLM provider/model、MCP server 或 A2A agent。
- Policy：认证、授权、TLS、限流、guardrail、凭据和可观测性规则。
- Configuration source：静态、本地文件、xDS 或 Kubernetes controller。
- Runtime IR：数据面统一消费的有效配置表示。

边界上下文详见 [[03-domain/02-bounded-contexts]]，组件实现详见 [[04-architecture/02-container-view]]。
