---
formatter: markdown
formatter_version: 1
---
# 部署视图

Standalone 部署通常是一个 agentgateway 进程/容器加配置文件与下游服务；Kubernetes 部署包含 controller、数据面 Deployment/Service、Gateway API/CRD 资源和下游服务；外部控制面模式在二者之外增加 xDS server。生产拓扑需按网络、身份、凭据和遥测依赖具体设计，详见 [[08-deployment/01-deployment-models]]。
