---
formatter: markdown
formatter_version: 1
---
# 调试指南

本地数据面可运行 release/debug binary 并访问 UI；配置问题先用 schema/validate，再检查 reload 日志；Kubernetes 使用 `kubectl describe`、事件、controller/data-plane 日志和 E2E artifacts；性能问题使用仓库已有 bench/pprof 能力。避免在没有记录版本和配置的情况下判断回归。
