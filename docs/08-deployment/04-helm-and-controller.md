---
formatter: markdown
formatter_version: 1
---
# Helm 与 Controller

## Chart 划分

- `agentgateway-crds`：安装 AgentgatewayBackend、Model、Parameters、Policy CRD。
- `agentgateway`：controller 与 data plane 的集群化部署，默认 controller Service 暴露 gRPC/health/metrics，支持 RBAC、xDS mode、HPA/VPA/PDB、proxy image 和监控。
- `agentgateway-standalone`：只部署 standalone proxy，支持 readonly/database 两种配置持久化模式、gateway Service、OIDC/session secret、PodMonitor、PDB 和 HPA。

## 重要 values

集群 chart：`rbac.gatewayNamespaces` 可缩小 namespaced write 范围；`controller.replicaCount`、`controller.xds.mode`、`controller.service.ports`、`proxy.image`、资源与调度字段影响控制面/数据面。controller 的 PDB/HPA/VPA 不直接扩展 data plane。

Standalone chart：`mode=readonly` 将静态 config 放入只读 ConfigMap；`mode=database` 以静态 config 为 baseline，将 Admin UI 变更保存到 PostgreSQL。chart 会管理 `config.storage` 和 `config.database`，values 中不应重复设置。gateway Service 的 targetPort 必须与 agentgateway listener 匹配；默认 proxy securityContext 为 non-root、禁止 privilege escalation、只读根文件系统和 drop all capabilities。

## 变更验证

1. `helm lint` 与 `helm template` 检查渲染结果。
2. 检查 CRD、RBAC、ServiceAccount、Service、Deployment、PDB/HPA/PodMonitor 差异。
3. 在 Kind/E2E 环境安装 chart，验证 controller reconcile、xDS、readiness 和真实请求。
4. 测试升级时的 CRD 保留、Deployment rollout、旧版本回滚和 database schema/数据兼容。

chart 变更需结合 CRD、RBAC、默认值、升级兼容和 controller E2E 验证。
