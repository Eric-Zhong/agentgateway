---
formatter: markdown
formatter_version: 1
---
# 升级与回滚

升级前备份配置/CRD、确认 schema/CRD/protobuf 兼容性、准备旧镜像和 chart；分批 rollout 后验证请求、策略、遥测和 controller 状态。异常时停止 rollout，恢复旧镜像/chart/配置，并保留日志、事件、版本和资源 diff 供复盘。
