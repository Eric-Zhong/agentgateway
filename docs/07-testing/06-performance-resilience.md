---
formatter: markdown
formatter_version: 1
---
# 性能与韧性

性能测试应覆盖吞吐、延迟、并发流式请求、配置更新扇出、provider failover、MCP 长连接和资源占用；韧性测试应覆盖下游超时、限流、策略依赖失败与 controller 重启。当前仓库有 benches/pprof 相关能力，但没有统一目标数字，须在部署场景中补充。
