---
formatter: markdown
formatter_version: 1
---
# 安全模型

信任边界包括客户端到 gateway、gateway 到下游、gateway 到外部策略/身份服务、controller 到 Kubernetes API、运维者到管理接口和构建发布供应链。身份、授权、TLS、凭据、CEL、网络和发布签名分别承担边界控制；管理接口默认属于受信任运维面，不是匿名互联网安全边界。
