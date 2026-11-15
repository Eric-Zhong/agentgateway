---
formatter: markdown
formatter_version: 1
---
# 产品范围

## 目标

agentgateway 是面向 AI 原生通信的网关/代理，统一承载 HTTP、LLM、MCP 和 A2A 流量，并提供认证、授权、限流、TLS、guardrail、路由、故障转移和 OpenTelemetry 能力。

## 用户角色

- 平台运维者：部署、升级、观测和故障处理。
- 网关配置者：定义 listener、route、backend 与 policy。
- 应用/Agent 开发者：通过统一协议访问模型、工具或其他 Agent。
- Kubernetes 平台管理员：通过 Gateway API 与 agentgateway CRD 管理控制面。
- 项目贡献者：维护 Rust 数据面、Go controller、UI、schema 和发布流程。

## 部署边界

- Standalone：由 YAML/JSON 文件驱动，适合独立进程或容器。
- Kubernetes：由 controller 将 Kubernetes/Gateway API 资源转换为数据面配置。
- 外部控制面：可通过 xDS 推送 purpose-built resource。

官方产品介绍进一步强调 agentgateway 的统一数据面定位：普通 HTTP/gRPC API、微服务、LLM、MCP 和 A2A 流量共享同一 proxy；同时强调工具联邦、长连接/会话处理、协议演进、开发者自助门户和多环境部署能力。整理后的官方说明见 [[02-requirements/05-official-introduction]]。

不在本项目文档中承诺具体云厂商 SLA、模型供应商可用性或用户自定义外部服务的正确性；官网宣传性表述也不自动等同于工程验收标准。
