---
formatter: markdown
formatter_version: 1
---
# 领域与实现映射

## 领域对象到运行时对象

| 领域概念 | 运行时/控制面对象 | 说明 |
|---|---|---|
| Gateway/Bind | `api.Bind`, `BindKey`, `AgwBind` | 网络入口与 listener 容器 |
| Listener | `api.Listener`, `GatewayListener`, `ParentInfo` | 协议、端口、hostname、TLS 和 route parent |
| Route | `api.Route`, `AgwRoute` | 匹配、转换、backend destination |
| Backend | `api.Backend`, endpoint/service/provider | 下游 HTTP、LLM、MCP 或 A2A 能力 |
| Policy | `api.Policy`, `AgwPolicy`, policy traits | 作用域、条件、执行阶段和合并 |
| Workload/Endpoint | `WorkloadStore`, `EndpointSet` | locality、health、capacity、affinity 和负载选择 |
| External resource | `ResourceRef`, `ResourceManager` | JWKS、OpenAPI、OIDC、文件和 HTTP 依赖 |
| Config resource | `ConfigResource` | UI/database overlay 的持久化资源 |

## 聚合边界

Gateway/Listener 负责入口关系；Route 负责请求匹配；Backend 负责下游连接策略；Policy 负责横切规则；Workload/Endpoint 负责实际可达性。controller 翻译阶段保存这些边界，数据面运行时再根据 request context、健康状态和策略条件组合有效执行对象。

## 一致性边界

- Kubernetes objects：最终一致，通过 informer/KRT 和 status 收敛。
- xDS resources：按资源名称和版本传播，proxy 等待初始状态后提供业务 readiness。
- local config：文件/HTTP 变化触发重新规范化，失败保留上一成功状态。
- database resources：事务和 generation 提供写入一致性，通知驱动其他 runtime reload。

## 领域服务

`translator` 是控制面领域服务，`ResourceManager` 是外部资源生命周期服务，`StateManager` 是运行时配置协调服务，`Policy` traits 是请求执行服务，`EndpointSet` 是下游选择服务。它们共同构成“声明式配置 -> 有效运行时行为”的领域链。

关联：[[03-domain/01-domain-overview]]、[[04-architecture/04-data-flow]]、[[05-design/09-technology-and-mechanisms]]。