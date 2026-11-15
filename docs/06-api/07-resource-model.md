---
formatter: markdown
formatter_version: 1
---
# Resource 模型

## xDS resource 类型

controller IR 当前映射的资源类型包括：`bind`、`listener`、`backend`、`route`、`route_group`、`model_route`、`tcp_route`、`policy`。资源名称由类型前缀加稳定 key 构成；Gateway 作用域由额外的 namespaced identity 表达。

## Standalone resource 类型

数据库-backed config store 使用 `modelCatalog`、`llm.provider`、`llm.model`、`llm.virtualModel`、`llm.apiKey`、`llm.policy`、`mcp.target`、`mcp.policy`、`traffic.gateway`、`traffic.route`、`traffic.tcpRoute`、`ui.policy`、`frontend.policy` 等 kind，并通过 `revision` 与全局 `generation` 实现乐观并发控制。

## 兼容要求

新增资源必须同时考虑：serde/schema 表示、protobuf/xDS 表示、controller translator、state manager/store、admin/UI API、status/telemetry、生成代码和测试 fixture。任何资源名称、字段语义或 merge 规则变化都应进入 [[06-api/05-compatibility]] 和 [[12-release/02-change-management]]。