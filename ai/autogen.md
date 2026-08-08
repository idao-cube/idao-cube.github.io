## 工具与定位

AutoGen 是微软推出的多 Agent 对话框架，核心创新在于"对话即编程"——通过 Agent 之间的自然对话完成复杂任务。支持人机协作（Human-in-the-loop）、代码执行、工具调用和分布式 Agent 群，是构建生产级 Agent 系统的重要框架。

## 核心概念

| 概念 | 说明 |
| --- | --- |
| Assistant Agent | 执行任务的 AI Agent，可调用工具 |
| User Proxy Agent | 用户代理，可代表人类或执行代码 |
| Group Chat | 多 Agent 群聊，Manager 协调对话 |
| Tool | 函数调用、代码执行、API 访问 |
| Human-in-the-loop | 关键节点人工审批与干预 |

## 典型场景

| 场景 | 说明 |
| --- | --- |
| 代码生成与调试 | 多 Agent 协作编写、测试、修复代码 |
| 数据分析 | 查询、清洗、可视化全流程自动化 |
| 文档生成 | 研究 → 大纲 → 初稿 → 审校流水线 |
| 工具链编排 | 多工具协同完成复杂任务 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [AutoGen](https://microsoft.github.io/autogen/) | 官方文档 |
| [GitHub](https://github.com/microsoft/autogen) | 开源代码仓库 |
| [Azure AI](https://azure.microsoft.com/) | 企业级部署 |

## 选型建议

微软生态首选 AutoGen；需要角色化团队选 CrewAI；SOP 驱动软件生成选 MetaGPT；简单对话 Agent 用 LangGraph。