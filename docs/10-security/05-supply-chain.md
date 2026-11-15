---
formatter: markdown
formatter_version: 1
---
# 供应链安全

依赖通过 Cargo/Go/pnpm lockfile 管理；生成 API/schema 必须可复现并由 CI `check-clean-repo` 验证；release 构建 multi-arch 镜像并使用 cosign/OpenVEX 相关产物。发布前还应核对 digest、SBOM/漏洞扫描和来源证明，具体自动化以 release workflow 为准。
