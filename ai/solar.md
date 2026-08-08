## 模型与定位

Solar 是韩国 AI 公司 Upstage 研发的开源模型系列，采用自研 Depth-Up Scaling（DUS）方法，在保持单 GPU 运行效率的同时，性能超越更大参数量的模型。Solar Pro 3 扩展至 102B 参数，在 Agent 工作流、代码生成、数学推理和指令遵循上实现大幅跃升。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 商业可用 | 适用场景 |
| --- | --- | --- | --- | --- | --- |
| Solar Mini | 10B | 4K | ✅ | ✅ | 轻量服务、边缘部署 |
| Solar Pro | 22B | 4K | ✅ | ✅ | 通用对话、工具调用 |
| Solar Pro 2 | 30B | 8K | ✅ | ✅ | 复杂推理、Agent |
| Solar Pro 3 | 102B | 8K | ✅ | ✅ | Agentic AI、多步任务 |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| Agent 优化 | 工具调用、多步任务执行、复杂指令遵循 |
| 推理增强 | 应用 SnapPO 强化学习，数学竞赛级表现 |
| 单 GPU 效率 | 22B 模型性能媲美 70B，仅需单卡 |
| 长上下文 | Solar Pro 2/3 支持 8K 上下文窗口 |
| 多语言 | 支持英语、韩语及多语言场景 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Upstage](https://upstage.ai/) | 官方 API 服务 |
| [OpenRouter](https://openrouter.ai/) | 全球模型服务接入 |
| [HuggingFace](https://huggingface.co/upstage) | 模型权重下载 |

## 选型建议

Agent 工作流首选 Solar Pro 3；需要单 GPU 高效运行选 Solar Pro（22B）；轻量场景选 Solar Mini；韩国市场项目优先选用。