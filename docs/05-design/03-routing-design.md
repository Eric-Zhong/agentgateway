---
formatter: markdown
formatter_version: 1
---
# 路由设计

路由按 listener/route/backend 关系组织。HTTP 路由负责常规匹配；LLM 路由补充 provider/model、成本/负载和流式语义；MCP 路由处理 server/tool/resource 与传输；A2A 路由处理 agent 能力协商与任务通信。权重、超时、重试、failover、缓存和语义路由等能力由 schema 与具体 backend/policy 决定，配置字段索引见 [[06-api/02-standalone-configuration]]。
