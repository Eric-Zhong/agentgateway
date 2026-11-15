---
formatter: markdown
formatter_version: 1
---
# 逆向分析证据登记

| 编号 | 证据 | 结论 |
|---|---|---|
| E-001 | `README.md` | 产品定位为面向 Agentic AI 的开源 proxy，覆盖 LLM、MCP、A2A、安全与可观测性 |
| E-002 | `Cargo.toml` | 数据面为 Rust workspace，包含 agentgateway、LLM、HTTP、策略、xDS 等 crate |
| E-003 | `architecture/configuration.md` | 存在静态、本地文件、xDS 三类配置，并映射到共享 IR |
| E-004 | `controller/api/v1alpha1/agentgateway/*` | Kubernetes API 由 AgentGateway 相关 CRD 类型组成 |
| E-005 | `Makefile` | 构建包含 UI、Rust release、Docker、schema/API 生成、lint 与 test |
| E-006 | `.github/workflows/pull_request.yml` | PR 验证包含 Rust 多平台、UI、Go race、controller lint、E2E |
| E-007 | `.github/workflows/release.yml` | 发布构建 amd64/arm64 镜像、推送 manifest、签名/供应链产物与 Helm |
| E-008 | `DEVELOPMENT.md` | 本地开发需要 Rust、Node/pnpm；可用 Tilt + Kind 进行 Kubernetes 开发 |
| E-009 | `SECURITY.md` | 漏洞须走私密报告，行政接口是受信任运维边界 |
| E-010 | `schema/config.md`, `schema/config.json` | Standalone 配置 schema 覆盖路由、后端、LLM、MCP、策略和 UI |
| E-011 | [官方 Introduction](https://agentgateway.dev/docs/standalone/latest/documentation/about/introduction/) | 官方 1.7 文档对产品定位、MCP/A2A 问题、关键能力、Standalone/Kubernetes 模式和策略分类的说明 |

## 逆向范围

本轮以仓库当前快照为准，不推断未在仓库或公开项目资料中出现的 SLA、容量上限、租户模型和生产拓扑。
