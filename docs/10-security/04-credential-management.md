---
formatter: markdown
formatter_version: 1
---
# 凭据管理

## 凭据类别

- 客户端到 gateway：API key、JWT/JWS、OAuth/OIDC session、basic auth。
- gateway 到 xDS/CA：xDS auth token、root CA、CA token、SPIFFE identity。
- gateway 到 provider/backend：API key、AWS/Azure/GCP 身份、credential provider、TLS client cert。
- 管理 UI：OIDC cookie secret、session encryption key、database connection string。

## 存储原则

凭据优先通过 Kubernetes Secret、Secret reference、环境注入或受支持 provider 提供；Standalone 配置只保存引用和必要元数据。`config.rs` 会根据 token 值是否为文件路径加载 xDS/CA token，chart 也支持 existing Secret；因此部署时必须确认挂载路径、权限和轮换后 reload/restart 方式。

日志、trace、error、admin snapshot、config dump 和 diff 不得泄露 Authorization、Proxy-Authorization、API key、cookie、数据库 URI 或私钥。敏感 header 应加入配置的 sensitive headers；管理/调试端点只绑定到受信网络。

## 轮换流程

1. 创建新凭据并授予最小权限。
2. 以双凭据/重叠有效期更新 Secret 或 provider。
3. 验证 xDS/CA/provider/UI 登录和真实业务请求。
4. 观察认证失败、provider error、TLS handshake 和审计日志。
5. 撤销旧凭据，记录版本和时间窗口。
6. 保留回滚所需的安全材料，但不恢复已泄露凭据。

轮换过程中不得把 token 直接写入 Git、公开 issue 或 CI 输出。
