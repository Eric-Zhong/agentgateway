---
formatter: markdown
formatter_version: 1
---
# 环境矩阵

| 环境 | 必需工具/依赖 | 验证 |
|---|---|---|
| 本地数据面 | Rust、Node 24.22.1、pnpm | `make build`, `make test` |
| 本地 Kubernetes | Docker/Podman、Kind、Tilt、ctlptl、cross、Go | `tilt up`, controller tests |
| CI PR | Rust、Go、Node、Playwright、Kind 依赖 | PR workflow |
| Release | Docker Buildx、GHCR、cosign、Helm | release workflow |

版本与前置条件以 `DEVELOPMENT.md`、Dockerfile 和 workflow 为准。
