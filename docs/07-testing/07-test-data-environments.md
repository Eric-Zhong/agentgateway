---
formatter: markdown
formatter_version: 1
---
# 测试数据与环境

测试数据集中在各 crate `tests/testdata`、examples 与 controller fixtures；外部服务优先使用可复现容器或 mock。Kind/MetalLB/Gateway API/Inference Extension 版本由 `controller/Makefile` 与 setup 脚本管理，避免依赖未声明的本地状态。
