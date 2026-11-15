---
formatter: markdown
formatter_version: 1
---
# 领域事件与状态

当前仓库主要通过 watch channel、xDS discovery、Kubernetes status、database generation 和请求 telemetry 表达状态变化，而不是独立业务事件总线。

## 配置/资源状态

- 文件配置：`unloaded -> expanded -> parsed -> validated -> translated -> active`。
- ConfigResource：`not-found -> upsert validation -> created/updated/unchanged`；并可能因 generation conflict 被拒绝。
- xDS：`connected -> resource received -> accepted/rejected -> applied`。
- Kubernetes：`observed -> indexed -> translated -> reported`；资源状态包含 accepted/attached 等 Gateway API conditions。
- Runtime：state manager 通过 watch 更新 stores，proxy 在初始状态到达前保持未就绪。

## 请求状态

`received -> classified -> authenticated -> authorized -> routed -> upstream started -> streaming/completed/failed`

MCP 还包含 session 建立、tool/resource/prompt 方法校验、fanout failure mode 和 session idle TTL；A2A 记录 success/error/unknown、result kind、task state、context id 等响应信息；LLM 记录 provider/model、token/cost/guardrail 和流式响应相关信息。

## 运维含义

事件应能关联 resource identity、gateway/namespace、revision/generation、request/trace id 和版本。具体错误与 status 字段以 controller reports、protobuf、runtime logs 和 metrics 实现为准。
