---
formatter: markdown
formatter_version: 1
---
# 源码索引

| 主题 | 位置 | 阅读重点 |
|---|---|---|
| 产品介绍/能力 | `README.md` | LLM/MCP/A2A、security、observability、部署入口 |
| 配置类型与解析 | `crates/agentgateway/src/lib.rs`, `config.rs` | RawConfig、环境变量优先级、静态/xDS/本地配置 |
| 运行时启动 | `crates/agentgateway/src/app.rs` | readiness、metrics、state manager、admin、proxy pool |
| 数据面 proxy | `crates/agentgateway/src/proxy/`, `http/` | 请求链、过滤器、策略和 upstream |
| LLM | `crates/agentgateway/src/llm/`, `crates/llm/` | provider、model、stream、guardrail、catalog |
| MCP | `crates/agentgateway/src/mcp/` | session、transport、RBAC、fanout、guardrail |
| A2A | `crates/agentgateway/src/a2a/` | agent card、JSON-RPC、任务状态 |
| 配置资源存储 | `crates/agentgateway/src/config_store.rs` | SQLite/Postgres、revision、generation、resource kinds |
| xDS/协议 | `crates/xds/`, `crates/protos/`, `api/`, `buf.yaml` | gRPC discovery、purpose-built resources、代码生成 |
| Go controller | `controller/pkg/agentgateway/` | collections、translator、plugins、IR、status |
| CRD/API | `controller/api/v1alpha1/agentgateway/` | Backend/Model/Parameters/Policy 类型与 CEL validation |
| Helm | `controller/install/helm/` | controller、CRD、standalone、监控和安全默认值 |
| UI | `ui/src/` | config、logs、costs、CEL、MCP playground、API client |
| 配置与 schema | `architecture/`, `schema/` | 配置哲学、字段说明、机器校验 |
| 构建 | `Makefile`, `Dockerfile`, `Tiltfile` | UI/Rust、镜像、Kind/Tilt、本地开发 |
| CI/发布 | `.github/workflows/` | PR 分层验证、多平台镜像、manifest、签名 |
| 场景 | `examples/` | LLM/MCP/A2A、认证、限流、遥测和部署实例 |
| 设计思想与模式 | `docs/04-architecture/09-design-philosophy.md`, `docs/05-design/09-technology-and-mechanisms.md` | 架构取舍、技术机制、设计模式和风险 |
