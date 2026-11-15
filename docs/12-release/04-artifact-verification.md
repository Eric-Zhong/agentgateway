---
formatter: markdown
formatter_version: 1
---
# 制品验证

## 镜像与发布资产

验证 release workflow 的 canonical version、Docker tag、GHCR repository、linux/amd64 与 linux/arm64 digest、manifest list 和 Helm/chart 版本。不得只验证 tag 可拉取；应记录 digest、构建 commit、平台、镜像 config labels 和签名/attestation 结果。

## 功能 smoke

- 容器能启动并返回 `/version` 或等效版本信息。
- readiness、metrics 和 admin 端口绑定符合部署配置。
- UI 静态资产存在且能加载 API。
- 最小 HTTP route 成功；一个策略拒绝用例返回预期结果。
- LLM/MCP/A2A 至少按发布影响范围执行一条真实或可复现 mock 场景。
- Kubernetes chart render/install/upgrade/rollback 通过，CRD status 与 xDS resource 正常。

## 供应链

release workflow 使用 cosign installer、GHCR 和 `.github/vex/openvex.json`；验证应检查签名身份/issuer、digest 是否一致、OpenVEX 是否对应同一版本，以及依赖/镜像扫描结果。若 SBOM、provenance 或 CVE 门禁未在 workflow 中明确实现，应在发布记录中标记为待确认，而不是默认宣称已完成。

实际验证结果必须保留构建日志、测试 artifacts、制品引用、digest 和环境信息。
