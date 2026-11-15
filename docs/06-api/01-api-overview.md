---
formatter: markdown
formatter_version: 1
---
# API 总览

用户接口分为四层：Standalone YAML/JSON schema、Kubernetes Gateway API/AgentGateway CRD、xDS/purpose-built protobuf、运行时 HTTP/LLM/MCP/A2A 协议。配置字段权威索引是 `schema/config.md` 与 `schema/config.json`；生成协议来源是 `crates/protos/proto` 与 `api/`。
