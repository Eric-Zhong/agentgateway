---
formatter: markdown
formatter_version: 1
---
# 作者设计思想分析

本文不是项目官方 ADR，而是根据当前仓库代码、schema、CI 和控制器实现进行的架构逆向分析。结论描述“代码表现出的设计意图”，不代表作者明确写出的全部动机。

## 1. 统一数据面，而不是复制两套网关

项目把普通 HTTP/gRPC、LLM、MCP 和 A2A 流量放入同一 proxy runtime，通过协议适配器和策略链处理差异。这样做的核心思想是复用网络入口、TLS、认证、限流、重试、遥测和生命周期管理，避免为 AI 流量再维护一套与传统 API gateway 分裂的基础设施。

实现上，`crates/agentgateway/src/http` 提供通用 HTTP filter/policy，`llm`、`mcp`、`a2a` 分别承载 AI 协议语义；公共请求/响应模型由 `crates/http` 和 policy crate 支撑。该结构体现“稳定内核 + 可替换协议边缘”的架构原则。

## 2. 用户 API、控制面资源与运行时 IR 尽量一一对应

`architecture/configuration.md` 明确强调 user API -> xDS -> IR 的直接映射。controller 的 `api.Resource` 以 Bind、Listener、Backend、Route、ModelRoute、TCPRoute、Policy 等资源表达配置，而不是把所有配置预先扁平成一个巨大 listener 或 route list。

这种选择解决两个问题：

- 配置修改的影响面小，便于增量 xDS 更新。
- 控制面主要做机械转换，策略作用域和合并语义留给运行时处理，减少控制面复杂 join 与资源扇出。

代价是数据面需要承担 policy merge、引用解析后的有效状态组织和更多运行时判断；因此 stores、indexes、resource manager 和策略抽象成为核心基础设施。

## 3. 声明式控制面与命令式数据面分离

Kubernetes controller 观察声明式资源，通过 KRT/informer collections、索引、translator 和 plugin 计算期望状态，再以 protobuf/xDS 发布给 Rust proxy。Rust proxy 不直接理解全部 Kubernetes 对象，而消费稳定的 purpose-built resources。

这体现控制面/数据面分离：

- 控制面负责观察、关联、翻译、状态报告和资源生命周期。
- 数据面负责低延迟请求处理、策略执行、协议适配和下游访问。
- 两者通过版本化资源协议和稳定名称解耦。

因此 Kubernetes API 的复杂性不会直接污染请求处理路径，同时 Standalone、本地文件和外部 xDS 可以复用同一套运行时 stores。

## 4. 动态配置优先采用事件通知和有效快照

配置更新不是通过重启进程完成。`StateManager` 汇聚 xDS、本地配置和 config resource store；`ResourceManager` 对文件和 HTTP 资源提供 cache、watch、TTL refresh、失败重试和 active resource retention；stores 通过 watch channel 发布变化。

代码中的重要安全思路是“最后一次成功状态优先”：重新加载失败时，临时抓取的资源不会自动成为 active，manager 会回退保留上一次成功配置依赖。该思路降低了配置错误对在线流量的破坏性。

## 5. 读多写少场景使用不可变快照和原子替换

负载均衡的 `EndpointSet` 使用 ArcSwap/atomic buckets：读路径直接加载当前快照，不获取写锁；写路径通过 mutex 串行化 load-modify-store，避免并发更新丢失。model catalog 和其他运行时状态也采用 ArcSwap 等方式降低读路径成本。

这是一种面向 proxy 的典型取舍：

- 把复杂性放到配置更新和写路径。
- 把请求路径优化为无锁或低锁读。
- 通过 Arc、快照和显式生命周期保证读者看到一致状态。

## 6. 长生命周期协议被建模为状态机，而不是普通 HTTP handler

MCP 具有 session、JSON-RPC method、SSE/streamable HTTP、异步消息、fan-out 和按 session 的授权视图；A2A 具有 agent card、任务状态和 context id。代码分别拥有 session/router/mergestream/upstream 和 request/response classification，不把它们简单降级为 path proxy。

这说明作者把连接、session 和消息视为领域状态，并通过显式错误类型、协议版本判断、failure mode 和 session TTL 控制生命周期。

## 7. 策略是横切能力，但通过 trait 与阶段拆分避免侵入协议实现

`RequestPolicyTrait`、`ResponsePolicyTrait`、`BackendPolicyTrait` 把策略执行分成请求一次、响应一次和每个 backend call 三个时机；`HasExpressions`/`PolicyExpressions` 让 CEL 表达式注册成为策略契约的一部分。

该设计避免把认证、授权、限流、重试、guardrail、header mutation 等逻辑复制到 HTTP/LLM/MCP/A2A 各协议实现中，同时可以明确 retry 与 response processing 的调用次数。

## 8. 平台适配在边界，领域逻辑在核心

Go controller 处理 Kubernetes API、Gateway API、Secret、ReferenceGrant、conditions 和 chart；Rust core 处理 proxy、协议、策略、DNS、TLS、资源和 telemetry。provider-specific LLM 代码位于 `llm` adapter，MCP transport 位于 mcp 子系统。

这种边界让 Standalone 与 Kubernetes 共享数据面能力，也让未来引入其他控制面或 provider 时无需改动所有请求路径。

## 9. 可诊断性是控制面契约的一部分

translator 不只生成资源，还生成 accepted/attached/error conditions；controller 的状态报告、admin snapshot、logging、version 和 pprof 共同构成诊断面。资源翻译失败不是静默忽略，而是以用户可见 status 传播。

这体现 Kubernetes operator 的设计思想：期望状态、实际状态、观测 generation 和错误原因必须可审计。

## 10. 主要架构取舍

| 取舍 | 选择 | 收益 | 成本 |
|---|---|---|---|
| 统一 vs 专用 gateway | 统一 proxy + 协议 adapter | 能力复用、运维面统一 | 核心 runtime 更复杂 |
| 扁平配置 vs 独立资源 | 独立 resource + runtime merge | 增量更新、低 fan-out | runtime 解析和合并复杂 |
| 强一致配置 vs 可用旧状态 | 最后成功状态保留 | reload 失败不立即中断业务 | 需要 revision、watch 和诊断 |
| 共享锁 vs 原子快照 | ArcSwap/immutable snapshot | 高并发读性能 | 写路径和内存生命周期更复杂 |
| 控制面直接调用数据面 vs xDS | protobuf/xDS 边界 | 语言和部署解耦 | 协议版本与状态同步复杂 |
| 通用 policy vs 协议内置逻辑 | trait + policy phases | 横切能力复用 | 调用阶段和失败语义需严格定义 |

## 11. 风险与后续设计关注点

- resource source 多样化会增加配置语义漂移风险，需持续维护 source-to-IR contract。
- runtime merge 的优先级、Override、失败开放/关闭需要可视化和 conformance 测试。
- ArcSwap 快照会提高读性能，但必须限制缓存、连接和 provider 状态的内存增长。
- 长连接/流式请求会使 graceful drain、超时、连接上限和扩缩容比普通 HTTP 更重要。
- controller plugin 的资源贡献需要严格避免字段冲突和循环依赖。

关联：[[04-architecture/08-control-plane-translation]]、[[05-design/01-runtime-design]]、[[05-design/04-policy-design]]、[[07-testing/06-performance-resilience]]。