## 工具与定位

AutoGPT 是最早将 LLM 与自主 Agent 循环结合的开源框架之一，支持自动任务分解、子任务执行、互联网搜索与文件操作。虽然后续有更高效的替代方案，它仍是理解 Agentic AI 工作原理的经典参考实现。

## 核心能力

| 功能 | 说明 |
| --- | --- |
| 自主循环 | 自动推理 → 行动 → 观察 → 再推理的闭环 |
| 任务分解 | 将复杂目标拆解为可执行子任务 |
| 工具调用 | 搜索、文件读写、代码执行、API 调用 |
| 长期记忆 | 向量数据库存储历史上下文 |
| 多模型支持 | OpenAI、Anthropic、本地模型 |

## 架构组件

| 组件 | 说明 |
| --- | --- |
| Forge | 简化版 Agent 构建框架 |
| Agent Protocol | 标准化 Agent 通信协议 |
| Benchmark | Agent 性能评测套件 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [AutoGPT](https://agpt.co/) | 官方平台与文档 |
| [GitHub](https://github.com/Significant-Gravitas/AutoGPT) | 开源代码仓库 |
| [Docker Hub](https://hub.docker.com/r/autogpt/autogpt) | 容器化部署 |

## 选型建议

学习 Agent 原理首选 AutoGPT；生产环境建议评估 CrewAI、AutoGen 等更现代的框架；快速原型可用 OpenCode Agent 内置能力。