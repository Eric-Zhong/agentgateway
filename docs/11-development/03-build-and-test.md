---
formatter: markdown
formatter_version: 1
---
# 构建与测试

## 常用命令

```bash
# Rust + embedded UI
make build
make test
make lint

# 不需要 Node 时跳过 UI 构建（仍会使用已有 ui/dist）
UI=0 make build

# schema/protobuf/metrics 生成与工作树校验
make gen
make generate-schema
make generate-apis
make validate
make check-clean-repo
make check-default-members

# controller
cd controller && go test -race ./...
make -C controller analyze
make -C controller verify

# UI
cd ui
corepack pnpm install --frozen-lockfile
pnpm lint
pnpm test:e2e
```

## 构建依赖与输出

根 Makefile 的 `make build` 会先执行 UI 安装/构建，再执行 `cargo build --release --features ui`；UI 产物在 binary 构建时从 `ui/dist` 嵌入。Dockerfile 复用类似的 Node + Rust 多阶段构建。`make docker`、`docker-ci`、`docker-musl` 和 `docker-s390x` 对应不同镜像变体，其中 s390x 明确不是官方支持架构。

## 生成物纪律

`make gen` 会刷新 API/schema/metrics 等生成物；CI 随后运行 `check-clean-repo`，因此提交生成代码前必须确认变更是预期的。`Cargo.toml` 的 workspace/default-members 也由 `check-default-members` 防止新增 crate 遗漏。

## 测试选择

局部 Rust 变更先运行目标 crate test，再运行 `make test`；controller 变更至少运行受影响 package 的 race test；UI 变更运行 lint 和 Playwright；Helm/CRD 变更进入 Kind/E2E。测试失败时保留完整命令、环境版本和 artifacts，不用格式化或清理命令掩盖失败。
