---
formatter: markdown
formatter_version: 1
---
# 质量属性需求

| 属性 | 当前可观测约束 | 验证入口 |
|---|---|---|
| 性能 | xDS 资源粒度贴近用户 API，避免大范围扇出；运行时策略合并 | [[07-testing/06-performance-resilience]] |
| 可用性 | 路由故障转移、Kubernetes rollout 与回滚路径 | [[09-operations/04-upgrade-rollback]] |
| 安全 | JWT/API key/OAuth、CEL/RBAC、TLS、私密漏洞处理 | [[10-security/01-security-model]] |
| 可观测性 | OpenTelemetry metrics/logs/tracing 与结构化日志 | [[05-design/06-observability-design]] |
| 可移植性 | Linux、Windows、macOS 测试；amd64/arm64 发布镜像 | [[08-deployment/05-container-images]] |
| 可维护性 | Rust/Go 分层、生成物校验、CI lint/test | [[11-development/04-code-conventions]] |

具体目标数值（P99、吞吐、RTO/RPO）当前仓库未定义，列入 [[13-reference/04-open-questions]]。
