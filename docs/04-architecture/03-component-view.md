---
formatter: markdown
formatter_version: 1
---
# 组件视图

入口层负责 listener/HTTP 连接；路由层负责匹配与 backend 选择；策略层负责认证授权和治理；协议层负责 HTTP/LLM/MCP/A2A 适配；配置层负责 source 到 IR；控制面层负责 xDS/Kubernetes；遥测层负责 logs/metrics/traces。Rust crate 边界见 [[11-development/02-repository-guide]]。
