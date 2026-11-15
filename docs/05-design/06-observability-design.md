---
formatter: markdown
formatter_version: 1
---
# 可观测性设计

日志用于请求、配置和控制面诊断；指标用于流量、延迟、错误、策略拒绝、provider 与资源使用；trace 用于跨 gateway、外部策略和下游 provider 的调用关联。Rust workspace 依赖 OpenTelemetry 相关 crate，controller 与 E2E 还需保留 Kubernetes 事件/状态证据。告警规则与容量阈值尚未在仓库统一定义，见 [[13-reference/04-open-questions]]。
