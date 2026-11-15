---
formatter: markdown
formatter_version: 1
---
# 协议

- HTTP/HTTPS：通用代理与管理接口。
- LLM：统一 OpenAI-compatible 入口及 provider-specific adapter。
- MCP：工具/资源/提示交互，多种 transport。
- A2A：Agent 能力发现与任务通信。
- xDS：控制面传输，但 resource 类型为项目自有 protobuf。
- Kubernetes/Gateway API：声明式控制面输入。

协议实现与测试见 [[05-design/05-protocol-adapters]] 和 [[07-testing/05-e2e-conformance]]。
