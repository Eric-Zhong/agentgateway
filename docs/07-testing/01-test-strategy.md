---
formatter: markdown
formatter_version: 1
---
# 测试策略

## 测试金字塔

| 层级 | 目标 | 当前入口 |
|---|---|---|
| Rust unit | 解析、策略、认证、协议分类、错误映射和小型状态机 | crate 内 `#[cfg(test)]`、`*_tests.rs` |
| Rust integration | 多模块 proxy、provider、MCP、配置和遥测协作 | `crates/*/tests`、testdata、wiremock |
| Rust benches | CEL、tokenization、池、关键转换的性能趋势 | `benches/`、`divan`/criterion 相关依赖 |
| Go unit/race | translator、plugins、CRD 校验、状态报告 | `go test -race ./...` |
| UI | API client、编辑器合并/校验、浏览器用户流程 | `ui` 的 unit 与 Playwright E2E |
| E2E | controller + Kind + images + Gateway API + data plane | `controller/test/e2e` |
| Conformance | Gateway API 行为符合度 | `controller/test/conformance` |
| 生成/一致性 | schema、protobuf、CRD、默认成员不漂移 | `make gen`, `check-clean-repo`, `check-default-members` |

## CI 对应关系

PR workflow 当前包含：Rust 多平台 `make test`、Rust generation/lint、UI lint + Chromium E2E、controller race test/lint/gen、controller E2E 和 Kind 镜像验证。不能只运行 `cargo test` 作为完整回归，因为它不会覆盖 controller、UI、CRD 和 Kubernetes 行为。

## 变更到测试的映射

- Rust HTTP/LLM/MCP/A2A 或策略：单元 + 相关 integration/fixture；改变外部行为时补 ATDD/E2E。
- schema/config：schema 生成、配置 validate、合法/非法 fixture、reload/冲突场景。
- controller translator/plugin：translator golden/status test + controller E2E；跨资源关系变化需验证 ReferenceGrant/RBAC/namespace。
- Helm/deployment：template lint/render、Kind 安装、rollout、metrics/readiness 和回滚 smoke。
- UI/admin API：API contract、权限/CSRF/OIDC、Playwright 关键路径。

## 质量门禁

提交前至少执行目标测试、`make lint` 或对应 Go/UI lint；涉及生成物执行 `make gen` 后确认工作树无意外差异；涉及运行时或部署行为执行至少一条真实请求验收场景。

验收场景见 [[02-requirements/04-acceptance-scenarios]]，详细分层见 [[07-testing/02-tdd-unit-tests]]、[[07-testing/05-e2e-conformance]]。
