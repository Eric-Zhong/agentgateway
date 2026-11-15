---
formatter: markdown
formatter_version: 1
---
# 数据流

## 配置流

1. 进程读取环境变量与静态配置，确定 admin/stats/readiness、DNS、xDS、CA/SPIFFE、数据库和运行身份。
2. 本地 YAML/JSON 经 shell expansion、serde 解析、CEL custom function 注册和字段校验。
3. Kubernetes controller 通过 informer/KRT collections 收集 Gateway API、Service/EndpointSlice/Secret、InferencePool、AgentgatewayBackend/Model/Policy 等对象。
4. translator/plugin 把对象转换为 `api.Resource`：Bind、Listener、Backend、Route、TCPRoute、ModelRoute、Policy。
5. resource manager/state manager 合并本地、xDS 和资源存储，更新 stores/IR，并通过 watch 通知 proxy。
6. proxy 等待初始配置状态，后续只消费有效的运行时状态；更新过程需要保留错误与 status 诊断。

## Kubernetes 控制面流

`Kubernetes API -> informer/KRT collection -> reference/index/plugin selection -> Gateway/route/backend/policy translator -> api.Resource protobuf -> xDS gRPC -> proxy state manager -> readiness/runtime stores`

controller 的 `AgwResource` 会保留资源及其 Gateway namespaced identity；xDS resource name 使用 `bind/`、`listener/`、`backend/`、`route/`、`route_group/`、`model_route/`、`tcp_route/`、`policy/` 前缀，避免不同资源类型重名。

## 请求流

1. Listener 接受连接并识别 HTTP、LLM、MCP、A2A 或其他 TCP/HTTP 语义。
2. 执行 TLS/网络入口过滤器、认证、CSRF/请求合法性和授权策略。
3. 根据 method/path/header/query/host 等匹配 route，并合并作用域策略。
4. 选择 backend；LLM 使用 provider/model 与健康/负载状态，MCP 处理 session/fanout，A2A 识别 agent card/JSON-RPC。
5. adapter 生成下游请求，执行 timeout/retry/rate-limit/callout/guardrail 等控制。
6. 响应可能以流式 body 返回；同时写 request log、metrics、trace 和必要的状态信息。

配置设计见 [[05-design/02-configuration-design]]，策略见 [[05-design/04-policy-design]]。