---
formatter: markdown
formatter_version: 1
---
# 协议适配器

适配器隔离统一入口模型与下游协议差异：HTTP 使用标准请求/响应；LLM 覆盖 OpenAI-compatible 与 provider-specific API；MCP 覆盖 stdio、HTTP、SSE、Streamable HTTP、OpenAPI；A2A 覆盖 agent discovery 与任务交互。每个适配器应定义认证、超时、重试、流式、错误映射和遥测字段。

官方 Introduction 强调 MCP/A2A 适配不能退化为普通短请求反向代理：MCP 需要 session/message awareness、异步 server-to-client message、按 session 的工具授权和多 backend response aggregation；协议版本变化还应支持 upgrade/fallback。
