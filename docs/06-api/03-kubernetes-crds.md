---
formatter: markdown
formatter_version: 1
---
# Kubernetes CRD 与控制器 API

controller API 位于 `controller/api/v1alpha1/agentgateway`，包含 Backend、Model、Parameters、Policy 及共享类型；同时集成 Gateway API。CRD 生成、验证、状态报告和 E2E 测试由 controller Makefile 与 `.github/workflows/pull_request.yml` 驱动。具体字段应直接查类型定义和生成清单。
