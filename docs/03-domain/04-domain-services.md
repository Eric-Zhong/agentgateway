---
formatter: markdown
formatter_version: 1
---
# 领域服务

- Configuration Translator：将 Standalone 或 xDS 资源转换为共享 IR。
- Route Resolver：根据 listener、route、backend 和策略作用域解析有效请求路径。
- Policy Evaluator：执行认证、授权、CEL、限流与 guardrail 规则。
- Provider Adapter：把统一 LLM/MCP/A2A 请求映射到下游协议。
- Control-plane Reconciler：观察 Kubernetes 资源并发布数据面资源/状态。
- Telemetry Recorder：记录跨协议请求的 logs、metrics、traces。

服务边界与实现映射见 [[05-design/01-runtime-design]]。
