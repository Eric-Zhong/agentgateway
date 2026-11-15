---
formatter: markdown
formatter_version: 1
---
# 仓库结构

- `crates/agentgateway`：主要 proxy crate
- `crates/agentgateway-app`：应用入口
- `crates/llm`, `http`, `pool`, `policy-core`, `celx`, `xds`, `protos`：领域/基础能力
- `controller/`：Go controller、CRD、Helm、E2E
- `ui/`：Node/pnpm UI
- `schema/`：配置 schema
- `examples/`：可运行场景
- `tools/`, `common/`, `.github/`：构建、生成、CI
