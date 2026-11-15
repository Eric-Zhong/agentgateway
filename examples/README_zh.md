## 示例

此目录包含如何使用 agentgateway 的示例。网关示例命名为 `<type>-<name>`，其中
`type` 可以是 `mcp`、`llm` 或 `traffic`。

集成示例可能会使用上游项目的名称，因为它们涵盖多种 agentgateway 流量类型。

### 集成

* [netbird-agent-network](netbird-agent-network/README.md)：在通过私有 agentgateway listener 路由 OpenAI 和 Anthropic 流量之前，使用 NetBird Agent Network 对其进行认证和授权。包含 Kubernetes 和独立 Docker Compose 部署方式。
* [openshell](openshell/README.md)：在专用的、由 agentgateway 管理的 Kubernetes Gateway 后运行 OpenShell 沙箱工作负载。
* [render-deploy](render-deploy/README.md)：在 Render 上部署独立版 agentgateway，使用公开 HTTPS URL、持久化的 `/config` 磁盘和 Blueprint（`render.yaml`）。

### MCP

* [mcp-basic](mcp-basic/README.md)：开始使用 agentgateway 的最简单方式，通过 Stdio 暴露单个 MCP server。
* [mcp-multiplex](mcp-multiplex/README.md)：在单个 listener 上复用多个 MCP target。
* [mcp-keyed-rate-limit](mcp-keyed-rate-limit/README.md)：为每个客户端在每个 server 的每个 tool 上分别设置独立配额。
* [mcp-guardrails/expression](mcp-guardrails/expression/README.md)：使用 CEL guardrail 拒绝 MCP 调用，并对请求/响应文本进行遮蔽。
* [mcp-authorization](mcp-authorization/README.md)：对传入请求应用 JWT 认证和 MCP 授权策略。
* [mcp-authzen](mcp-authzen/README.md)：基于 OpenID [AuthZEN](https://openid.net/specs/openid-authzen-authorization-api-1_0-ID1.html) 标准对 MCP tool 调用进行授权。
* [mcp-authentication](mcp-authentication/README.md)：认证 MCP 客户端并保护 MCP 流量。
* [mcp-tls](mcp-tls/README.md)：终止 TLS 连接。
* [mcp-openapi](mcp-openapi/README.md)：将 OpenAPI 规范作为 MCP tools 提供服务。
* [mcp-apps](mcp-apps/README.md)：在复用的 target 之间代理 MCP Apps（交互式 tool UI）。
* [mcp-telemetry](mcp-telemetry/README.md)：可视化 MCP 调用的 trace 和 metrics。

### LLM

* [llm-basic](llm-basic/README.md)：使用特定于 provider 的模型前缀，将 LLM 请求代理到 OpenAI 和 Anthropic。
* [llm-context-compression](llm-context-compression/README.md)：通过外部授权使用 Headroom 缩减 LLM 请求上下文。
* [llm-cost-routing](llm-cost-routing/README.md)：使用 CEL 对 LLM 请求进行分类，并将同一个公开模型名路由到不同的上游模型。
* [llm-ollama-postgres](llm-ollama-postgres/README.md)：代理本地 Ollama 模型，并将请求日志存储到 Postgres。
* [llm-keyed-rate-limit](llm-keyed-rate-limit/README.md)：根据 JWT claims 和请求的模型，为每个调用方提供独立的本地限流桶。
* [llm-prompt-enrichment](llm-prompt-enrichment/README.md)：向 agentgateway AI 请求追加或前置 prompt。
* [llm-prompt-guard](llm-prompt-guard/README.md)：为 LLM 请求和响应配置 prompt guard。
* [llm-semantic-routing](llm-semantic-routing/README.md)：使用 vLLM Semantic Router，按照成本或层级进行语义模型选择。
* [llm-standalone-epp](llm-standalone-epp/README.md)：在 Kubernetes 上的独立 EPP 部署旁，将 agentgateway 作为 sidecar proxy 运行。
* [llm-telemetry](llm-telemetry/README.md)：导出 LLM backend 调用的 trace。

### 流量

* [traffic-http](traffic-http/README.md)：将 agentgateway 作为标准 HTTP proxy 使用。
* [traffic-egress-proxy](traffic-egress-proxy/README.md)：将 agentgateway 作为带 allowlist 的 CONNECT egress proxy 使用，支持动态公共 backend 和固定的集群内 route。
* [traffic-a2a](traffic-a2a/README.md)：代理 [A2A](https://a2aproject.github.io/A2A/) 流量。
* [traffic-aws-agentcore](traffic-aws-agentcore/README.md)：使用 JWT 认证和用户 ID header 转发来代理 AWS AgentCore 流量。
* [traffic-token-exchange](traffic-token-exchange/README.md)：通过 `extAuthz` + CEL、`backendAuth.oauth` RFC 8693 token-exchange grant 或 RFC 7523 JWT bearer grant，将传入的用户凭据交换为每个上游专用的 token。
* [traffic-cross-app-access](traffic-cross-app-access/README.md)：使用 Cross App Access（OAuth Identity Assertion Authorization Grant / ID-JAG）将已认证用户的身份交换为 backend 作用域的 access token，包含本地 Keycloak、xaa.dev 或 Okta+Auth0 演示。
* [traffic-jwt-sign](traffic-jwt-sign/README.md)：在每次 backend 请求中使用私钥签署短期 JWT，适用于 Snowflake SQL API 等要求使用密钥对 JWT 而非静态凭据的上游服务。
* [traffic-oidc](traffic-oidc/README.md)：使用内置的 `oidc` 浏览器认证流程和本地 Keycloak issuer。
* [traffic-oauth2-proxy](traffic-oauth2-proxy/README.md)：与外部 `oauth2-proxy` 部署集成。
* [fault-injection](fault-injection/README.md)：向部分流量注入模拟延迟和中止，用于故障注入测试。
* [traffic-ratelimiting-local](traffic-ratelimiting-local/README.md)：对 HTTP 流量应用本地限流。
* [traffic-ratelimiting-global](traffic-ratelimiting-global/README.md)：使用 Envoy ratelimit service 对流量应用全局限流。
* [traffic-tailscale-auth](traffic-tailscale-auth/README.md)：使用 Tailscale identity header 对 HTTP 请求进行认证。
* [traffic-spiffe](traffic-spiffe/README.md)：对于服务端和上游连接，从本地 SPIFFE Workload API 获取网关的 mTLS 身份。
* [traffic-unified-gateway](traffic-unified-gateway/README.md)：在一个共享的 gateway listener 上暴露 LLM、MCP 和 UI。

### 客户端管理

* [microsoft-intune](microsoft-intune/README.md)：验证由 Intune 管理的 Codex
  和 Claude Desktop 配置，并在 macOS 和 Windows 上为这两个客户端
  报告自定义合规性。