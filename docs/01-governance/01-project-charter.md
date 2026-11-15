---
formatter: markdown
formatter_version: 1
---
# 项目章程

agentgateway 的公开使命是为 Agent-to-LLM、Agent-to-Tool、Agent-to-Agent 通信提供统一的安全、治理和可观测性 proxy。范围以 [[02-requirements/01-product-scope]] 为准。

## 原则

- API、xDS 与运行时 IR 尽量保持直接映射，降低配置扇出。
- 安全边界、配置来源和部署前提必须在文档中显式说明。
- 生成文件由生成命令维护，不手工改动。
- 行为变化必须同时更新需求、测试与发布说明。

治理依据：`CHARTER.md`、`CONTRIBUTION.md`、`CODEOWNERS`、`SECURITY.md`。
