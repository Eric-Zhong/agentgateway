---
formatter: markdown
formatter_version: 1
---
# Admin 与 UI 设计

## 管理面

proxy 管理面提供 admin、metrics、readiness 等独立 server；controller 也提供 admin server，当前明确包含 `/version`、`/logging`、`/snapshots/krt` 和 `/debug/pprof/` 等诊断入口。管理面必须与业务 listener、metrics 和 readiness 分离绑定，并遵守 `SECURITY.md` 中“受信任运维接口”的边界。

## UI 功能域

`ui/src/api/` 与页面组件表明 UI 覆盖 config/config resources、CEL、logs、runtime、costs/budgets、keys、guardrails、MCP playground、client setup 和 model/provider 选择。UI 写入的配置可能落入文件或 database overlay，必须向用户显示当前 storage mode、revision 和保存结果。

## 交互契约

- 配置编辑器应先 schema 校验，再显示 diff，最后调用 upsert/更新 API。
- 并发编辑遇到 generation/revision conflict 时，保留用户草稿并要求重新加载/合并。
- 凭据页面只显示脱敏值；导出、日志和错误消息不可返回 secret。
- 管理 endpoint 与 UI session/OIDC/CSRF 配置必须按部署模式验证。

关联：[[05-design/02-configuration-design]]、[[09-operations/06-troubleshooting]]、[[10-security/04-credential-management]]。