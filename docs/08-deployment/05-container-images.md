---
formatter: markdown
formatter_version: 1
---
# 容器镜像

根 `Dockerfile` 构建 UI、Rust binary 并生成运行镜像；Makefile 提供普通、CI、musl 和 s390x 目标。release workflow 构建 linux/amd64 与 linux/arm64，按 digest 合并 manifest，并使用 GHCR。s390x 在 Makefile 中明确为非官方支持架构。
