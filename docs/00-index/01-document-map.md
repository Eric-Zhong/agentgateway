---
formatter: markdown
formatter_version: 1
---
# 文档地图

## 生命周期

需求从 [[02-requirements/01-product-scope]] 开始，经由 [[03-domain/01-domain-overview]]、[[04-architecture/01-system-context]] 和 [[05-design/01-runtime-design]] 形成实现约束，再由 [[07-testing/01-test-strategy]] 验证，最终通过 [[08-deployment/01-deployment-models]]、[[09-operations/01-operations-handbook]] 和 [[12-release/05-release-checklist]] 交付运行。

## 快速定位

| 问题 | 文档 |
|---|---|
| 项目做什么 | [[02-requirements/01-product-scope]] |
| 请求如何流动 | [[04-architecture/04-data-flow]] |
| 配置如何生效 | [[05-design/02-configuration-design]] |
| API/CRD 在哪里 | [[06-api/01-api-overview]] |
| 如何测试 | [[07-testing/01-test-strategy]] |
| 如何部署 | [[08-deployment/01-deployment-models]] |
| 出问题怎么办 | [[09-operations/06-troubleshooting]] |
| 安全边界是什么 | [[10-security/01-security-model]] |

## 证据原则

结论优先引用仓库内的源码、配置、测试、工作流和现有文档；无法由仓库确认的内容标注为“待确认”或“建议”。证据登记见 [[00-index/02-evidence-register]]。
