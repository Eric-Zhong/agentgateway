---
formatter: markdown
formatter_version: 1
---
# ATDD 验收场景

## 场景 A：Standalone 配置生效

Given 一个合法 YAML/JSON 配置，When 启动 agentgateway 并发送匹配请求，Then 请求按 route/backend 规则转发且策略生效。

## 场景 B：配置动态更新

Given 运行中的文件型配置，When 修改并保存合法配置，Then proxy reload 后新请求使用新 IR；非法配置不得破坏当前有效配置。

## 场景 C：Kubernetes 控制面

Given Gateway API/AgentGateway CRD 资源，When controller 观察到资源变化，Then 数据面收到对应 xDS/purpose-built resources，状态可被诊断。

## 场景 D：策略拒绝

Given 请求不满足认证/授权或 CEL 规则，When 请求进入策略链，Then 返回拒绝结果并产生日志/指标证据。

## 场景 E：发布制品

Given vX.Y.Z tag，When release workflow 完成，Then amd64/arm64 镜像 manifest、Helm/发布资产和验证信息可追踪。
