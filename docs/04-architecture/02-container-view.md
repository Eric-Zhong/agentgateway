---
formatter: markdown
formatter_version: 1
---
# 容器视图

## 运行容器

| 容器/模块 | 技术 | 责任 | 主要证据 |
|---|---|---|---|
| agentgateway proxy | Rust | 接收连接、执行策略、匹配路由、调用下游、输出遥测 | `crates/agentgateway/src/app.rs`, `proxy/` |
| state/config manager | Rust | 汇聚本地配置、xDS 和资源存储，维护运行时 stores/IR | `state_manager/`, `config_store.rs` |
| admin/metrics/readiness | Rust | 管理 API/UI、Prometheus metrics、健康与 readiness | `management/`, `app.rs` |
| protocol adapters | Rust | HTTP、LLM、MCP、A2A、TLS、认证和策略实现 | `http/`, `llm/`, `mcp/`, `a2a/` |
| controller | Go | 监听 Kubernetes 资源、翻译为 agentgateway protobuf resources、报告状态 | `controller/pkg/agentgateway/` |
| UI | TypeScript/React/pnpm | 配置、日志、成本、CEL、MCP playground 等管理体验，构建后嵌入 proxy | `ui/src/`, `Makefile` |
| Helm charts | Helm/YAML | controller、CRD、standalone proxy 和监控资源 | `controller/install/helm/` |
| external services | Kubernetes API、xDS、数据库、身份/策略、provider | 提供控制面、持久化、认证与下游能力 | 配置和 chart values |

## 运行时边界

`agentgateway` 进程内部不是单一 handler：`app::run_with_ui_assets` 初始化 worker pool、OpenTelemetry resource defaults、readiness、metrics、上游 client、state manager、admin server 和 proxy gateway；proxy 与 state manager 在初始 xDS 状态准备好后才进入就绪/处理阶段。优雅退出通过 drain channel 和 shutdown signal 协调。

controller 与 proxy 通过 gRPC/xDS 及项目自有 `api.Resource` protobuf 协作，而不是直接共享 Go 内存对象。UI 通过 admin/runtime API 读写管理功能，具体 endpoint 以 UI `src/api/` 和 Rust management 模块为准。

代码映射和依赖见 [[11-development/02-repository-guide]]，请求生命周期见 [[05-design/01-runtime-design]]。