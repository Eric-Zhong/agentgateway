---
formatter: markdown
formatter_version: 1
---
# 故障排查

## 第一轮收集

记录时间、namespace/gateway/route/backend、镜像 digest、chart/CRD 版本、配置 revision/generation、request/trace id；同时保存 proxy/controller 日志、Pod events、Gateway/Policy conditions、metrics 和 xDS 连接状态。

## 按层定位

1. **进程与 readiness**：检查 binary/UI assets、启动配置、监听地址；确认 readiness 是否仍在等待初始 xDS/state manager，而非误判为端口故障。
2. **配置与资源**：用 `schema/config.json`/validate 检查文件；检查 config store 的 kind/id/revision/generation 和 generation conflict；确认最新有效版本没有被非法更新替换。
3. **Kubernetes translator**：检查 GatewayClass/Gateway/HTTPRoute/Backend/Policy conditions、引用 namespace/sectionName、ReferenceGrant、Secret 和 Service/EndpointSlice；controller 的 admin `/version`、`/logging`、`/snapshots/krt`、`/debug/pprof/` 可辅助诊断。
4. **xDS/control plane**：确认 controller Service 端口、xDS mode、TLS trust root、token/header、DNS 和 namespace/gateway identity；区分 gRPC auth failure、DNS resolution failure 和 resource rejected。
5. **route/policy**：验证 listener/host/path/method/header/query match；检查 policy target、merge precedence、CEL evaluation、JWT/API key/OAuth、MCP authorization 和 ext-authz 结果。
6. **upstream**：区分 DNS/TLS/connect/timeout、provider quota、LLM format conversion、MCP session/transport、A2A JSON-RPC 和遥测出口错误。
7. **Kubernetes rollout**：检查 Deployment/Service selector、targetPort、readiness、PDB/HPA、image pull 和 resource pressure。

## 保护证据

不要直接删除 Pod、ConfigMap、数据库或旧镜像来“重试”。先导出资源 YAML、日志、事件、metrics snapshot 和当前配置；修复后用同一请求重放并比较 trace/status。安全疑似事件按 [[10-security/06-security-operations]] 处理。
