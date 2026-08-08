## 工具与定位

MetaGPT 是一个多 Agent 框架，模拟完整软件公司的角色分工：产品经理编写 PRD、架构师设计技术架构、工程师编写代码、QA 编写测试用例。通过 SOP（标准作业程序）约束 Agent 输出质量，实现可预测的软件生成流程。

## 角色分工

| 角色 | 输出产物 | 说明 |
| --- | --- | --- |
| Product Manager | PRD 文档 | 需求分析与产品定义 |
| Architect | 系统设计 | 技术选型与架构设计 |
| Engineer | 代码实现 | 功能开发与单元测试 |
| QA Engineer | 测试用例 | 质量保证与测试 |
| Project Manager | 进度协调 | 多角色协同管理 |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| SOP 驱动 | 标准化流程确保输出质量一致 |
| 多角色协作 | 真实软件公司角色映射 |
| 完整交付物 | PRD → 设计 → 代码 → 测试全覆盖 |
| 可扩展 | 自定义角色、工具和 SOP |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [MetaGPT](https://docs.deepwisdom.ai/) | 官方文档 |
| [GitHub](https://github.com/geekan/MetaGPT) | 开源代码仓库 |
| [Demo](https://deepwisdom.ai/) | 在线演示 |

## 选型建议

全自动软件生成首选 MetaGPT；灵活 Agent 协作选 CrewAI；研究 SOP 驱动开发参考 MetaGPT 设计模式。