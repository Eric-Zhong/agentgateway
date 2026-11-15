---
formatter: markdown
formatter_version: 1
---
# 架构质量属性

## 性能

xDS resource cardinality 尽量镜像用户 API：单个 HTTPRoute rule、Pod 等映射为独立资源，减少修改造成的全量 fan-out。

## 可演进性

协议适配、策略、池和 xDS 被拆为独立 crate/context；用户配置与内部 IR 采用显式转换，避免把供应商细节扩散到入口层。

## 可靠性

路由具备重试/故障转移等能力，但实际语义以具体 backend/policy 配置和测试为准；变更通过 CI 生成物检查和多平台测试阻断回归。

## 安全性

认证、授权、凭据和 TLS 作为策略/部署边界独立建模，管理接口默认按受信任运维面处理。
