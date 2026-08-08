## 工具与定位

LangGraph 是 LangChain 官方 Agent 编排框架，通过状态图（StateGraph）精准控制 Agent 执行流程。支持条件分支、循环、子图嵌套等复杂逻辑，是人机协作（Human-in-the-loop）和生产级 Agent 系统的首选框架。

## 核心概念

| 概念 | 说明 |
| --- | --- |
| StateGraph | 状态图，定义 Agent 的状态与转换逻辑 |
| Node | 图中的节点，代表一个 Agent 或一个工具 |
| Edge | 边，定义节点间的流转条件 |
| Conditional Edge | 条件边，根据状态动态决定下一步 |
| Checkpoint | 持久化状态，支持中断与恢复 |

## 典型模式

| 模式 | 说明 | 适用场景 |
| --- | --- | --- |
| ReAct Agent | 推理 + 行动循环 | 工具调用、搜索 |
| 多 Agent 协作 | 多个子图协同工作 | 复杂任务分解 |
| 人机协作 | 关键节点人工审批 | 高风险决策 |
| 循环优化 | 迭代改进直到满足条件 | 代码生成、文案优化 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [LangGraph](https://langchain-ai.github.io/langgraph/) | 官方文档 |
| [LangSmith](https://smith.langchain.com/) | 可视化调试与评测 |
| [LangGraph Cloud](https://langchain-ai.github.io/langgraph/cloud/) | 托管部署服务 |

## 选型建议

需要精细控制 Agent 流程首选 LangGraph；简单 Agent 用 LangChain/LangChain Expression；多角色协作选 CrewAI；软件生成选 MetaGPT。