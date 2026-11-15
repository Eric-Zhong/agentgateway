---
formatter: markdown
formatter_version: 1
---
# 认证与授权

README 宣布支持 JWT、API key、OAuth、RBAC/CEL 和 MCP authorization。认证确认主体，授权按 Gateway/Listener/Route/Backend/MCP 作用域执行；规则的失败、拒绝与外部策略超时语义必须通过配置 schema、实现和验收测试共同确认。用户编写的错误 CEL 属于配置责任，但系统必须正确保护策略管理权限。
