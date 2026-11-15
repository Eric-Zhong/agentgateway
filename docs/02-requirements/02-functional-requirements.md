---
formatter: markdown
formatter_version: 1
---
# 功能需求

| ID | 需求 | 证据/关联 |
|---|---|---|
| FR-001 | 接收并路由 HTTP/AI 原生协议流量 | [[03-domain/02-bounded-contexts]] |
| FR-002 | 支持 LLM provider/model 路由、负载均衡与故障转移 | `README.md`, `crates/llm` |
| FR-003 | 支持 MCP 多传输、工具聚合、OpenAPI 与 OAuth 场景 | `README.md`, `crates/agentgateway/src/mcp` |
| FR-004 | 支持 A2A agent-to-agent 通信 | `README.md`, examples |
| FR-005 | 在 Gateway、Listener、Route、Backend 等范围应用策略 | `architecture/configuration.md` |
| FR-006 | 支持本地文件动态 reload 与 xDS 配置 | `architecture/configuration.md` |
| FR-007 | 暴露日志、指标、追踪与管理/UI 能力 | `README.md`, `crates/agentgateway` |
| FR-008 | 在 Kubernetes 中由 controller 管理 Gateway API/CRD | `controller/`, CI E2E |
