---
formatter: markdown
formatter_version: 1
---
# 发布流水线

release workflow 先解析版本，再并行构建 linux/amd64 与 linux/arm64 镜像，按 digest 合并并推送 manifest，随后执行签名/发布资产/Helm 相关步骤。发布流程应与 [[12-release/04-artifact-verification]] 和 [[12-release/05-release-checklist]] 配套执行。
