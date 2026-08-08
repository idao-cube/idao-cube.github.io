## 模型与定位

Code Llama 由 Meta 研发，基于 Llama 架构针对代码任务深度优化。提供基础版、Python 专用版（Code Llama - Python）和指令微调版（Code Llama - Instruct），支持 16K 上下文，覆盖 80+ 编程语言。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| Code Llama 7B | 7B | 16K | ✅ | 轻量代码补全 |
| Code Llama 13B | 13B | 16K | ✅ | 代码生成、解释 |
| Code Llama 34B | 34B | 16K | ✅ | 复杂代码任务 |
| Code Llama 70B | 70B | 16K | ✅ | 高性能代码 Agent |
| Code Llama - Python 7B | 7B | 16K | ✅ | Python 专用场景 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Meta AI](https://ai.meta.com/blog/code-llama/) | 官方介绍 |
| [HuggingFace](https://huggingface.co/codellama) | 模型权重下载 |
| [Ollama](https://ollama.com/library/codellama) | 本地一键运行 |

## 选型建议

IDE 插件、代码补全选 7B/13B；代码审查、重构选 34B；全自动代码 Agent 选 70B；Python 项目优先选 Python 专用版。