---
formatter: markdown
formatter_version: 1
---
# 代码规范

Rust 提交前运行 `cargo fmt --check` 与 clippy；Go 保持 `go mod tidy` 干净并运行 lint/race；生成文件通过生成命令更新；依赖版本变更需同步 lockfile。实现应保持 crate/context 边界，避免把协议或供应商逻辑复制到入口层。
