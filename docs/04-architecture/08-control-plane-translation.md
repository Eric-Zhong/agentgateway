---
formatter: markdown
formatter_version: 1
---
# 控制面翻译架构

## 输入集合

controller 的 `AgwCollections` 使用 KRT/informer 维护 Kubernetes 核心资源、Gateway API 资源、InferencePool、AgentgatewayBackend/Model/Policy，以及可选 Istio ambient 资源。索引按 namespace、parent reference、Service、Secret、HTTPRoute 等关系建立，减少每次变更的全量扫描。

## 翻译输出

translator 将对象转换为项目自有 protobuf resource：`Bind`、`Listener`、`Backend`、`Route`、`TCPRoute`、`ModelRoute`、`Policy`。`AgwResource` 同时保存资源和 Gateway namespaced identity；资源相等性比较包含 protobuf 内容和所属 Gateway，避免错误复用。

## 状态与错误

不支持的 Gateway API match/filter、无效引用、跨 namespace 缺少 ReferenceGrant、Secret/TLS 不可用等情况应转换为 Gateway API conditions 或 AgentgatewayPolicy status，而不是静默丢弃。translator tests、golden tests 和 E2E 共同验证 accepted/attached 结果。

## 设计约束

- resource name 需稳定、类型隔离、可用于 xDS 增量更新。
- parent/child 资源尽量独立，避免修改单个 route 时重新生成整个 gateway。
- policy 先保留作用域和 target，再由数据面按运行时规则合并。
- 生成的 API/protobuf 与 controller 类型必须通过 `make generate-apis` 和 clean-repo 检查。

关联：[[04-architecture/04-data-flow]]、[[06-api/03-kubernetes-crds]]、[[07-testing/05-e2e-conformance]]。