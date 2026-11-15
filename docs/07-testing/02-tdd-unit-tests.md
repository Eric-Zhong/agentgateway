---
formatter: markdown
formatter_version: 1
---
# TDD 单元测试

Rust 使用 crate 内 tests、integration tests、snapshots/fixtures 与 benches；Go 使用 package tests 和 race detector；UI 使用 Playwright/helper tests。新增领域规则优先以失败测试定义边界，再实现最小行为，最后运行格式、lint 和目标测试。
