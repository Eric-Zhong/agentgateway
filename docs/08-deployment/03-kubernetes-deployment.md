---
formatter: markdown
formatter_version: 1
---
# Kubernetes 部署

## 组件与职责

Kubernetes 模式由 controller、agentgateway data plane、Gateway API/AgentGateway CRD、Service/Secret/ConfigMap、可选 InferencePool/ListenerSet/XBackend、以及 xDS gRPC 通道组成。controller 需要 Kubernetes API 读取权限和按配置范围的写权限；standalone proxy 的 ServiceAccount 默认只作为 pod identity，不需要 Kubernetes API 权限。

## 安装前

1. 确认 Kubernetes、Gateway API、Inference Extension 和 chart 版本兼容。
2. 确认 controller 的 `gatewayNamespaces`、system namespace、controller name 和 RBAC 范围。
3. 准备 proxy/controller 镜像、私有仓库 pull secret、TLS Secret、OIDC/session secret 和下游凭据。
4. 规划 xDS transport：chart controller values 支持 `plaintext`、`tls`、`either`，生产应确认证书、信任根和轮换流程。
5. 为 data plane 配置资源 requests/limits、PDB、HPA/拓扑分布和 gateway Service 端口；不要把 controller 的 PDB/HPA 当作 data plane 可用性保证。

## 安装后验证

- `kubectl get gatewayclass,gateway,httproute,agentgatewaybackend,agentgatewaypolicy -A` 检查对象和 conditions。
- 检查 controller Deployment、Service、health/metrics/xDS 端口和日志。
- 检查 data plane rollout、readiness、监听 Service 与实际端口映射。
- 创建最小 HTTP/LLM/MCP 路由，验证请求成功、策略拒绝和 metrics/trace。
- 修改 route/backend，确认 translator status、xDS resource 更新和 data plane 生效。
- 记录镜像 digest、chart version、CRD version 和回滚命令。

## 关键限制

Gateway API 资源状态和 AgentgatewayPolicy 的 target/merge 约束由 CRD CEL validation 和 translator 共同决定；“资源已创建”不等于“route 已 accepted/attached”。详细翻译链见 [[04-architecture/04-data-flow]]，chart 参数见 [[08-deployment/04-helm-and-controller]]。
