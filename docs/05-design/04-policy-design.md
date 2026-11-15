---
formatter: markdown
formatter_version: 1
---
# 策略设计

## 作用域与合并

Kubernetes `AgentgatewayPolicy` 明确区分 `frontend`、`traffic`、`backend` 三类设置，并要求 `targetRefs` 与 `targetSelectors` 二选一。字段校验限制了目标 Kind、sectionName、端口和策略组合，避免把 backend MCP/AI 认证错误地挂到不支持的 Service 或 provider section。

策略选择通常按照作用域从粗到细合并：

- traffic：Gateway < Listener < Route < Route Rule。
- backend：Gateway < Listener < Route < Route Rule < Backend/Service。
- frontend：面向 Gateway/端口入口，不使用 traffic/backend 的 inheritance 语义。

合并是字段级而非深层递归 merge：更具体策略设置某字段时覆盖同字段，未设置字段仍可由较粗策略提供。`strategy.inheritance=Default` 允许下级覆盖默认值，`Override` 可阻止更具体 traffic policy 参与。

## 执行阶段

1. 网络入口：TLS、proxy protocol、network authorization、listener-level HTTP/TCP 约束。
2. 请求身份：API key、JWT/JWS、basic auth、OAuth/OIDC、云 provider 身份。
3. 请求授权：CEL/RBAC、MCP tool/resource authorization、外部 ext-authz。
4. 流量治理：timeout、retry、buffer、header/URL transform、local/remote rate limit、session affinity、outlier detection。
5. backend 安全与治理：backend TLS、credential provider、LLM guardrail、MCP guardrail、外部 ext-proc。

## 失败语义

每个策略必须定义未配置、匹配失败、表达式错误、外部服务超时和下游拒绝的结果。对安全策略默认应偏向 fail-closed；MCP backend fanout 可显式选择 `FailClosed` 或 `FailOpen`，但全目标失败仍应返回错误。实现和配置文档必须同时记录状态码/JSON-RPC error、日志字段和 metrics。

拒绝策略必须明确失败语义；`SECURITY.md` 特别指出用户自写 CEL、外部 ext-auth/ext-proc/rate-limit 的错误不等于产品自身漏洞。安全模型见 [[10-security/03-authentication-authorization]]。