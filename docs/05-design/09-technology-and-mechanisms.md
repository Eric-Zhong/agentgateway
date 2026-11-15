---
formatter: markdown
formatter_version: 1
---
# 技术栈与实现机制

## Rust 数据面

| 技术/机制 | 用途 | 设计价值 |
|---|---|---|
| Tokio async runtime | 并发连接、任务、watch、timer、graceful shutdown | 适合长连接、流式响应和高并发 I/O |
| Hyper/Axum/HTTP types | HTTP server/client、header、body、协议处理 | 标准化请求/响应与扩展点 |
| Tonic/Prost/gRPC | xDS、protobuf、控制面通信 | 类型安全、跨语言资源协议 |
| Arc/ArcSwap/RwLock | 共享状态、原子快照和低锁读 | 缩短请求路径同步成本 |
| watch/mpsc/JoinSet | 配置事件、调度、任务管理 | 显式生命周期和事件驱动 |
| DashMap/Mutex | 并发计数、写路径串行化和缓存 | 在读写模式不同的场景选择合适同步方式 |
| serde/schemars | YAML/JSON 和 schema 生成 | 人类配置与机器校验共用模型 |
| CEL | 动态条件、属性、授权和转换表达式 | 保持策略可配置而不编译新代码 |
| OpenTelemetry/Prometheus | logs、metrics、traces | 跨 gateway、policy、provider 关联诊断 |
| Rustls/AWS-LC/SymCrypt | TLS 与密码学 provider | 支持平台和合规模式差异 |

## Go Kubernetes controller

| 技术/机制 | 用途 | 设计价值 |
|---|---|---|
| Kubernetes client/informer | 监听资源和状态 | 声明式 reconciliation 基础 |
| Istio KRT collection/index | 增量集合、关联和缓存 | 避免每次变更全量扫描 |
| Gateway API types | GatewayClass、Gateway、Route、Policy 语义 | 使用标准 Kubernetes 网关模型 |
| plugin registry | 后端/策略/资源贡献 | 扩展 controller 而不修改主 pipeline |
| ReferenceGrant checker | 跨 namespace 引用安全 | 将授权边界纳入资源翻译 |
| conditions/status | Accepted、Attached、Invalid 等状态 | 让用户看到实际 reconcile 结果 |
| protobuf conversion | Go 对象到 `api.Resource` | 与 Rust 数据面稳定交互 |

## 配置与资源机制

### 多源配置

`ConfigSource`、xDS client、LocalClient 和 `ConfigResourceStore` 允许不同来源进入同一 StateManager。source 的解析、规范化和有效资源集合由 `ResourceFetcher` 统一处理，但 fetch mode 可以区分 managed cache、cached-or-direct、direct 和 files-only，体现“同一接口、多种一致性/缓存策略”。

### TTL 与文件监听

ResourceManager 对 JWKS、OpenAPI、OIDC discovery 和 generic HTTP resource 使用不同 TTL；失败刷新有最小重试间隔。文件 watch 通过 watch id 防止编辑器替换文件后旧任务删除新 watch，并在配置失败时回收临时依赖。

### 乐观并发控制

数据库 config store 使用 revision/generation、expected generation 和 Created/Updated/Unchanged 结果。多副本同时初始化 session key 时，失败的一方会重新读取获胜值，而不是覆盖已有值。这是典型的 optimistic concurrency + retry-on-conflict。

## 请求处理机制

请求过滤器按阶段组合：入口/连接限制、请求认证、授权、路由转换、backend policy、上游调用、响应处理。Policy trait 明确 request policy 不因 retry 重复执行，backend policy 按 backend call 执行，response policy 在最终响应阶段执行。

负载均衡支持 locality bucket、健康/驱逐、权重、affinity、power-of-two choices 和 fallback。LLM provider 选择使用 provider set 和健康评分；MCP 支持多 target fan-out 与 FailClosed/FailOpen；这些机制分别针对模型、服务端点和工具聚合的不同失败模型。

## 安全机制

- Secret/credential reference 与 provider-specific auth 分离。
- JWT/JWS、API key、basic auth、OAuth/OIDC、云 provider auth 由 HTTP auth 子模块承载。
- TLS 既用于 listener，也用于 backend/xDS/CA/SPIFFE 连接。
- CEL 作为表达式引擎，但策略实现必须显式注册所有表达式，防止表达式遗漏导致静默不执行。
- Kubernetes ReferenceGrant、RBAC 和 namespace selector 参与 controller 翻译，而不是部署后再补救。

## 设计模式清单

| 模式 | 代码表现 | 适用问题 |
|---|---|---|
| Ports and adapters | protocol adapters、client、xDS/local source 分层 | 隔离外部协议和核心模型 |
| Strategy | provider、policy、failure mode、fetch mode、crypto provider | 运行时选择算法/行为 |
| Chain of responsibility | HTTP filter/policy chain | 按阶段组合横切处理 |
| Composite | 多 policy、多个 provider、MCP targets | 聚合多个可独立对象 |
| Observer/reactive | watch channel、KRT collection、xDS subscription、DB notification | 配置和资源变化传播 |
| Repository/cache | ResourceManager、ConfigResourceStore、model catalog | 统一持久化/远程资源访问与缓存 |
| Adapter | LLM provider、MCP transport、A2A protocol、Go-to-protobuf | 统一上层接口适配差异协议 |
| Plugin/registry | Go `AgwPlugin`、policy/backend contributions | 扩展 controller 资源计算 |
| Snapshot/Copy-on-write | ArcSwap EndpointSet、model state | 高频读低锁并发 |
| Reconciler | Kubernetes controller translator/status | 声明式期望状态收敛 |
| Optimistic concurrency | config generation conflict、session key retry | 多写者并发更新 |
| Bulkhead/admission control | connection/request permits、per-core workers | 限制资源耗尽和隔离故障 |

## 不应过度推断的模式

代码中可以观察到上述结构，但不应把所有模块都称作完整 GoF 模式实现。例如 StateManager 不是纯 CQRS，controller 也不是完整 Event Sourcing；xDS resource 不是业务事件。文档应优先使用“机制/边界/职责”描述，避免只贴模式名而忽略实际调用时机和失败语义。

关联：[[04-architecture/09-design-philosophy]]、[[05-design/01-runtime-design]]、[[05-design/07-extension-design]]、[[13-reference/02-source-index]]。