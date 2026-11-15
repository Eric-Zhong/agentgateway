---
formatter: markdown
formatter_version: 1
---
# Standalone 配置

## 配置层次

Standalone 配置由两类输入组成：

- 启动/静态输入：环境变量和顶层 `config` 中的 admin、stats、readiness、DNS、IPv6、xDS、CA、SPIFFE、数据库、session 等参数。
- 动态业务输入：本地 YAML/JSON 中的 gateways/binds/listeners、routes、backends、LLM、MCP、policies、model catalog、UI 等资源。

`crates/agentgateway/src/config.rs` 会先展开 shell 变量，再反序列化 `NestedRawConfig`，注册 custom CEL functions，解析敏感 header、IPv6/DNS、local config、xDS/CA auth token 和 trust domain。环境变量通常优先于文件字段；具体优先级必须以实现和 schema 为准。

## 资源存储模式

`config_store.rs` 定义可由 admin/UI 管理的资源类型，包括：

- `modelCatalog`
- `llm.provider`、`llm.model`、`llm.virtualModel`、`llm.apiKey`、`llm.policy`、`llm.settings`
- `mcp.target`、`mcp.policy`、`mcp.settings`
- `traffic.gateway`、`traffic.route`、`traffic.tcpRoute`
- `ui.policy`、`frontend.policy`

每个资源有 `kind`、`id`、JSON `value`、`revision`、创建/更新时间；批量 upsert 可能返回 created/updated/unchanged，并使用 generation conflict 保护“校验期间配置被其他写入修改”的情况。数据库模式支持 SQLite/Postgres；readonly 模式和 database overlay 的部署差异见 [[08-deployment/02-standalone-deployment]]。

## 变更规则

- 先用 `schema/config.json` 做机器校验，再用 `schema/config.md` 查字段语义。
- 静态监听、数据库和控制面连接参数可能要求重启；动态路由/策略/模型资源可通过本地 reload、xDS 或资源 store 更新。
- 修改策略、凭据、TLS 或下游地址前必须验证生效范围与失败语义。
- 不能将 `config` 文件中不存在的字段直接写入生产；生成 schema 与实现必须同步。

完整字段表请以仓库 `schema/config.md` 为准，机器校验使用 `schema/config.json`。配置源、reload 和 IR 语义见 [[05-design/02-configuration-design]]。