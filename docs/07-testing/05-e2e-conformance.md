---
formatter: markdown
formatter_version: 1
---
# E2E 与 Conformance

controller E2E 使用 Kind setup 脚本、编译镜像和 `go test -tags=e2e,agent,disable_pgv ./controller/test/e2e`；Gateway API conformance 位于 `controller/test/conformance`。测试失败应同时保留 pod、deployment、controller 日志和测试报告。
