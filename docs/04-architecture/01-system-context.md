---
formatter: markdown
formatter_version: 1
---
# 系统上下文

agentgateway 位于 Agent、应用和外部 AI 能力之间：上游可以是 Agent/应用/客户端，下游可以是 LLM provider、MCP server、HTTP service、A2A agent；旁路依赖包括 Kubernetes API、外部身份/策略服务、OpenTelemetry collector 和凭据系统。

## 外部边界

- 数据面：处理真实业务请求，不应把管理接口默认暴露为公共安全边界。
- 控制面：生成/推送配置，不直接替代数据面的请求处理。
- 外部服务：ext-auth、ext-proc、rate limit、模型供应商和遥测后端按配置被信任，错误行为不等同于 agentgateway 自身漏洞（见 `SECURITY.md`）。

部署模式见 [[08-deployment/01-deployment-models]]，安全边界见 [[10-security/01-security-model]]。
