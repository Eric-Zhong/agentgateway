---
formatter: markdown
formatter_version: 1
---
# 运行时设计

## 启动阶段

`app::run_with_ui_assets` 的实际启动顺序如下：

1. 检查 TLS key logging，并安装策略 trace。
2. 创建 data-plane worker pool 和 shutdown/drain 通道。
3. 注册 readiness tasks、启动 readiness server。
4. 创建 metrics registry、xDS metrics、Tokio/allocator collectors。
5. 创建带 callout、DNS、H2/HBONE 和可选 CA/SPIFFE 的 upstream client。
6. 合并 model catalog sources，并创建 `ModelCatalog`。
7. 创建 `StateManager`、stores、resource manager 和 xDS watch channel。
8. 启动 state manager；收到初始 xDS state 后释放 state-manager readiness。
9. 创建 admin server、metrics server 和 `ProxyInputs`。
10. 创建 `proxy::Gateway`，等待初始 xDS state 后在 data-plane pool 中运行。

因此“进程已启动”不等于“业务已就绪”。readiness 必须同时反映 state manager 与 agentgateway 任务状态。

## 请求阶段

请求路径可抽象为：

`listener -> connection/protocol classification -> request filters -> authentication -> authorization/policy merge -> route/backend selection -> protocol adapter -> upstream -> response filters/telemetry`

HTTP 模块包含 JWT/API key/basic auth/OIDC/OAuth、ext-authz/ext-proc、CSRF、TLS、timeout、retry、local/remote rate limit、outlier detection、buffer/compression 等过滤器。LLM 模块负责请求格式转换、provider 选择、模型目录、tokenization、streaming guardrail 和响应日志；MCP 模块负责 JSON-RPC、session、SSE/streamable HTTP、工具授权、fanout 和 failure mode；A2A 模块识别 agent card 与 JSON-RPC call，并在响应中提取任务/消息状态。

## 配置更新

配置源经过解析、校验、转换后进入 state manager/store，再触发 proxy 使用新的有效状态。文件配置、xDS resources 和 database-backed config store 不是同一个 API：数据库资源具备 `kind/id/value/revision/generation`，并支持 SQLite/Postgres 与 watch notification；UI 的写入模式由 storage 配置决定。文档应把“reload 成功”和“单资源写入成功”分别验证，不能把二者混为一个事务。

## 生命周期与故障

- readiness：等待 state manager 初始状态和 proxy 启动条件。
- graceful drain：收到 shutdown 后通知组件，等待 drain receivers 释放，超时后强制结束。
- xDS/DNS/provider 失败：通过错误、状态和 telemetry 暴露，具体 fail-open/fail-closed 由协议/策略配置决定。
- 配置解析失败：不得把未经验证的数据标记为 active；当前生效版本和错误原因必须可诊断。

详细流转见 [[04-architecture/04-data-flow]]，配置见 [[05-design/02-configuration-design]]，测试见 [[07-testing/02-tdd-unit-tests]]。