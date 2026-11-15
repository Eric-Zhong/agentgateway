---
formatter: markdown
formatter_version: 1
---
# 扩展设计

扩展点包括 provider adapter、policy provider、外部 auth/proc/rate-limit、MCP OpenAPI/transport、Kubernetes API 与 schema/code generation。新增扩展应保持现有 source -> IR -> runtime 边界，避免在入口层直接引入供应商特定逻辑，并补充单元、集成和验收测试。
