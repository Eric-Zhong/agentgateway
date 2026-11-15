---
formatter: markdown
formatter_version: 1
---
# 集成测试

集成测试覆盖 provider、MCP server、认证/策略、遥测与配置转换等跨模块行为；需要外部依赖时使用 `tools/manage-validation-deps.sh` 或示例提供的 Docker Compose。测试环境、凭据和清理策略必须写入对应测试 README。
