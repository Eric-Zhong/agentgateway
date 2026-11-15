---
formatter: markdown
formatter_version: 1
---
# 兼容性

兼容性至少包含：Standalone schema 版本、CRD apiVersion、xDS/protobuf resource、客户端协议、provider adapter、镜像/Helm 版本和 Kubernetes 版本。生成 API、schema 与 CRD 变更必须通过 `make gen`/对应验证，并在发布说明中记录行为变化。当前仓库未提供完整的长期兼容矩阵，见 [[13-reference/04-open-questions]]。
