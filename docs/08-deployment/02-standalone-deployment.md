---
formatter: markdown
formatter_version: 1
---
# Standalone 部署

构建 UI 后使用 `make build` 生成 release binary；运行 binary 并提供静态/本地配置，默认 UI 入口可从 `DEVELOPMENT.md` 的本地流程验证。生产部署需显式配置监听地址、TLS、凭据、下游网络和遥测出口，不应把管理接口直接暴露到不受信网络。
