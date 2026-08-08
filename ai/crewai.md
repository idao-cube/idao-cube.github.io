## 工具与定位

CrewAI 是一个以"角色化 Agent 团队"为核心的协作框架，每个 Agent 拥有专属角色、工具和目标，通过顺序、层级或异步模式协同完成复杂任务。特别适合模拟真实团队工作流（如内容团队、研究小组、数据分析组）。

## 核心概念

| 概念 | 说明 |
| --- | --- |
| Agent | 角色化智能体，拥有 name、role、goal、backstory |
| Task | 具体任务，分配给特定 Agent 执行 |
| Crew | Agent 与 Task 的集合，代表一个完整工作流 |
| Process | 执行模式：sequential、hierarchical、async |
| Tool | Agent 可调用的工具（搜索、代码、API） |

## 协作模式

| 模式 | 说明 | 适用场景 |
| --- | --- | --- |
| Sequential | 顺序执行，前一任务输出作为后一输入 | 内容生产流水线 |
| Hierarchical | 层级管理，Manager Agent 分配任务 | 复杂项目管理 |
| Async | 异步并行，多 Agent 同时工作 | 大规模数据采集 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [CrewAI](https://www.crewai.com/) | 官方网站与文档 |
| [GitHub](https://github.com/joaomdmoura/crewai) | 开源代码仓库 |
| [CrewAI Studio](https://studio.crewai.com/) | 可视化拖拽构建 Agent 团队 |

## 选型建议

模拟团队工作流首选 CrewAI；需要精细控制 Agent 通信选 AutoGen；简单任务自动化用 LangChain/LangGraph。