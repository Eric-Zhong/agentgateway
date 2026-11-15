---
formatter: markdown
formatter_version: 1
---
# 部署模式

| 模式 | 控制方式 | 适用 |
|---|---|---|
| Standalone | 文件/YAML/JSON + file watch | 独立进程、容器、快速验证 |
| Kubernetes | controller + Gateway API/CRD + Helm | 集群化声明式管理 |
| External xDS | 外部控制面推送 purpose-built resources | 自建控制面或集成平台 |

官方文档补充了运维风格差异：Standalone 更适合 binary/Docker/单 Deployment、ClickOps、个人或小团队和非 Kubernetes 基础设施；Kubernetes 模式更适合共享集群、CRD/Gateway API、GitOps 和平台团队。Standalone UI 可以编辑配置；Kubernetes UI 主要是只读观察入口，声明式变更应通过 Kubernetes CR/GitOps 完成。

选型取决于配置来源、网络边界、扩缩容和运维能力。快速开始入口参考根 README 与 [[08-deployment/02-standalone-deployment]]。官方来源见 [[02-requirements/05-official-introduction]]。
