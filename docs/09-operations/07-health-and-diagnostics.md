---
formatter: markdown
formatter_version: 1
---
# 健康检查与诊断

## 数据面

- readiness：确认 state manager 初始状态和 proxy task 已满足运行条件。
- metrics：Prometheus endpoint 记录请求、错误、资源和 xDS/运行时指标。
- admin/UI：提供配置、runtime、logs、costs 和诊断能力，必须限制绑定地址与访问权限。

## controller

controller Helm 默认分别暴露 health、metrics 和 agentgateway gRPC 端口；admin server 还提供版本、日志级别、KRT snapshot 和 pprof 诊断入口。pprof/snapshot 不应公开暴露或纳入普通业务 ingress。

## 排障顺序

先健康与版本，再配置/conditions，再 xDS，再路由/策略，最后下游 provider/MCP/A2A。每一步记录时间、resource identity、revision/generation、Pod、digest 和 trace id；恢复后验证同一业务场景而不只看进程存活。

关联：[[09-operations/06-troubleshooting]]、[[05-design/08-admin-and-ui-design]]。