---
formatter: markdown
formatter_version: 1
---
# 配置设计

- Static config：启动时设置日志、端口等全局参数，不承载完整路由/策略。
- Local config：YAML/JSON 文件定义完整功能，file watch 触发 reload，映射到共享 IR。
- XDS config：使用 xDS transport 与项目自有 protobuf resource，不采用 Envoy Listener/Cluster 类型，强调高效机械转换。
- Kubernetes config：controller 观察 Gateway API 与 AgentGateway CRD，生成数据面资源。

设计约束是 source -> xDS -> IR 尽量一一对应，策略在运行时按作用域合并，避免控制面扇出。现有依据：`architecture/configuration.md`。
