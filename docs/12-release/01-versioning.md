---
formatter: markdown
formatter_version: 1
---
# 版本与兼容性

release workflow 触发于 `v*.*.*` tag，并将版本规范化为无 `v` 前缀的 canonical VERSION。版本影响 Docker tag、Helm/chart、CRD/schema/protobuf 和客户端兼容性；每次发布应关联 [[06-api/05-compatibility]]。
