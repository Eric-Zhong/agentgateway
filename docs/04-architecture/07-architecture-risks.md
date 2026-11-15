---
formatter: markdown
formatter_version: 1
---
# 架构风险与演进

- 配置 schema 与 CRD/xDS 多源并存，若映射规则不一致会产生行为差异；需持续维护 [[06-api/05-compatibility]]。
- LLM/MCP/A2A 下游能力和错误语义各异，统一 API 可能隐藏供应商差异；需在 adapter 文档与验收场景中显式化。
- 外部策略服务与遥测系统是运行时依赖，需定义超时、失败开放/关闭和容量边界。
- UI 构建产物嵌入 binary，Node/pnpm 与 Rust 构建链耦合；应保持可跳过 UI 的快速开发路径。
- 当前仓库没有统一的性能目标数字；见 [[13-reference/04-open-questions]]。
