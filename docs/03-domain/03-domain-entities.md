---
formatter: markdown
formatter_version: 1
---
# 领域实体与聚合

## 聚合候选

- Gateway/Bind 聚合：入口、listener 与其网络/TLS属性。
- Route 聚合：匹配规则、后端引用、转换和局部策略。
- Backend 聚合：服务地址、LLM/MCP/A2A 能力和连接参数。
- Policy 聚合：作用域、优先级、合并语义和外部依赖。
- Workload/Endpoint 聚合：Kubernetes 工作负载到可达端点的映射。

## 值对象

URI、主机/端口、协议类型、权重、超时、凭据引用、CEL 表达式、模型名称和 provider 参数。

这些是领域分析模型；最终字段以 `schema/config.*`、CRD 类型和 protobuf 定义为准。
