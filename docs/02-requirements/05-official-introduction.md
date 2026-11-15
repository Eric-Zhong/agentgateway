---
formatter: markdown
formatter_version: 1
source: https://agentgateway.dev/docs/standalone/latest/documentation/about/introduction/
source_version: 1.7 (latest, accessed 2026-10-31)
---
# 官方产品介绍整理

> 本文整理自 agentgateway 官方 Standalone 1.7 文档的 Introduction 页面。官网定位用于产品语义和能力导航；仓库源码、schema、测试和 workflow 仍是当前实现细节的优先证据。
>
> 来源：[agentgateway Introduction](https://agentgateway.dev/docs/standalone/latest/documentation/about/introduction/)

## 产品定位

官方文档将 agentgateway 定义为 AI-first、开源、云原生的 gateway control plane 与 proxy data plane。它既是通用 HTTP/gRPC 数据面，也面向 LLM inference、MCP tool server 和 A2A agent traffic，目标是让普通 API、微服务和 AI 原生流量共享同一套网关能力，而不必分别维护“普通 API 网关”和“AI 网关”。

仓库实现与该定位相符的部分包括：Rust 数据面、Go/Kubernetes controller、HTTP/LLM/MCP/A2A 模块、TLS、负载均衡、超时、重试、限流、授权、traffic policy 和 OpenTelemetry 支持。实现证据见 [[04-architecture/02-container-view]] 与 [[13-reference/02-source-index]]。

## MCP 与 A2A 的问题空间

MCP 用于在 LLM 与工具/上下文之间交换信息，A2A 用于多 Agent 间的任务协作和状态管理。官方页面将二者描述为 JSON-RPC 协议，并指出它们解决了互操作性和消息结构问题，但本身不完整覆盖企业级的：

- 安全：认证、授权、审计和工具访问控制。
- 治理：数据边界、访问控制和自治工作流策略。
- 可观测性：知道 Agent 做了什么、何时做、为什么做。
- 可扩展性与性能：低延迟、重试、超时、失败恢复和资源控制。

这些问题对应仓库中的认证/授权、CEL/RBAC、MCP authorization、guardrail、rate limit、retry、timeout、telemetry 和 policy 合并设计，详见 [[05-design/04-policy-design]] 与 [[10-security/01-security-model]]。

## 为什么传统网关不足

传统 API gateway/reverse proxy 通常围绕短生命周期 HTTP 请求设计：接收请求、选择 backend、转发响应，不需要持续维护 session context 或双向连接状态。

MCP 的关键差异包括：

- JSON-RPC 方法和协议语义，不只是 HTTP path 转发。
- 客户端和服务器维护长生命周期 session。
- 请求和响应必须绑定相同 session context。
- MCP server 可能异步向 client 发消息。
- 一个客户端请求可能需要访问多个 MCP backend，聚合工具/资源结果。
- 每个 session 看到的工具集合可能不同，需要按 session 动态调整响应。

因此，MCP gateway 必须具备 session/message awareness、双向通信、连接资源控制和按客户端授权的工具虚拟化能力。仓库对应实现入口包括 `crates/agentgateway/src/mcp/session.rs`、`router.rs`、`mergestream.rs`、`rbac.rs` 和 `upstream.rs`；设计概要见 [[05-design/05-protocol-adapters]]。

## 官方列出的关键能力

| 能力 | 文档化含义 | 仓库对应入口 |
|---|---|---|
| Unified data plane | HTTP、gRPC、LLM、MCP、A2A 和普通 API 共用 proxy | `crates/agentgateway/src/http`, `llm`, `mcp`, `a2a` |
| High performance | Rust、高吞吐、低延迟、长连接和 fan-out 场景 | Rust workspace、pool、bench |
| Any agent framework | 兼容支持 MCP/A2A 的 Agent framework，并可把 REST API 暴露为工具 | `examples/`, protocol adapters |
| Platform agnostic | 可运行于 bare metal、VM、container、Kubernetes | [[08-deployment/01-deployment-models]] |
| Multiplexing/tool federation | 单一入口聚合多个 MCP server，并按 client 虚拟化工具 | MCP router/session/upstream |
| Protocol upgrades/fallbacks | 在协议演进时协商并优雅处理升级和回退 | MCP version handling、adapter tests |
| Authentication/authorization | JWT、RBAC 和工具/Agent 访问控制 | `http/auth`, `authorization`, `mcp/rbac` |
| Built-in observability | metrics 和 tracing 观察 client/backend tool 交互 | `telemetry/`, management metrics |
| Self-service portal | 内置 UI 便于发现、联邦、集成和保护 Agent/工具 | `ui/`, admin/runtime API |
| Open source | Apache 2.0 开源 | `LICENSE`, `README.md` |
| Gateway API conformance | 面向 Kubernetes Gateway API 的网关能力 | `controller/test/conformance` |
| Dynamic configuration | 通过 xDS 无停机更新 | `crates/xds`, controller translator |

表中“官方列出”是官网产品能力表述；实际支持范围仍需结合当前版本的 schema、实现和测试结果确认。

## Standalone 与 Kubernetes

官方文档将两种模式区分为：

| 维度 | Standalone | Kubernetes |
|---|---|---|
| 运行位置 | 任意环境 | Kubernetes 集群 |
| 配置方式 | agentgateway 配置文件，UI 可编辑 | Kubernetes CR、Gateway API |
| 运维风格 | ClickOps、个人/小团队/非 Kubernetes 基础设施 | GitOps、共享 Kubernetes 平台、大团队 |
| 架构 | binary proxy，可选数据库写入配置 | control plane + proxy gateway，可选数据库 |
| 安装 | binary、Docker、单 Deployment Helm | CRD Helm + control plane Helm |
| UI/CLI | 可编辑 UI、binary、`agctl` | 只读 UI、Kubernetes CR、`agctl` |

官方页面来源：[Standalone introduction](https://agentgateway.dev/docs/standalone/latest/documentation/about/introduction/)。本仓库对应的实际 chart 和权限差异见 [[08-deployment/04-helm-and-controller]]，配置存储差异见 [[06-api/02-standalone-configuration]]。

## 策略能力分类

官方介绍将策略分为三类：

### Traffic management

Header add/set/remove、redirect、URL/authority/path rewrite、direct response、CORS、request mirror、timeout、retry。

### Security

MCP authorization、外部 MCP authentication、A2A、AI-specific policy、backend TLS、backend auth、local/remote rate limit、JWT auth、external authorization。

### Resiliency

Request mirroring、timeout、retry/backoff 和按响应码触发重试。

这些能力在当前工程文档中分别展开于 [[05-design/03-routing-design]]、[[05-design/04-policy-design]]、[[10-security/03-authentication-authorization]] 和 [[07-testing/04-atdd-acceptance-tests]]。

## 版本与证据边界

官网链接使用 `standalone/latest`，页面当前显示 1.7；它可能随官网版本更新而变化。本文保留来源 URL 和采集日期。对“市场上最成熟/最可靠”等宣传性表述不转化为工程验收标准；工程质量应以可测量的性能、可靠性、安全和兼容性指标为准，当前未定义的数字见 [[13-reference/04-open-questions]]。
