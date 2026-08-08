## 模型与定位

Nous Hermes 由 NousResearch 研发，基于 Llama 架构进行高质量指令微调，在指令遵循、推理能力和对话质量上超越原版 Llama。Hermes 系列采用大规模高质量数据集训练，支持工具调用和 Agent 工作流。

## 参数速览

| 模型 | 基座 | 上下文 | 开源权重 | 商业可用 | 适用场景 |
| --- | --- | --- | --- | --- | --- |
| Hermes 2 - Llama 2 7B | Llama 2 7B | 4K | ✅ | ✅ | 轻量对话、实验 |
| Hermes 2 - Llama 2 13B | Llama 2 13B | 4K | ✅ | ✅ | 通用对话、推理 |
| Hermes 3 - Llama 3.1 8B | Llama 3.1 8B | 128K | ✅ | ✅ | 长上下文、Agent |
| Hermes 3 - Llama 3.1 70B | Llama 3.1 70B | 128K | ✅ | ✅ | 高性能 Agent |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| 指令遵循 | 精准理解并执行复杂指令 |
| 工具调用 | 支持多步工具链执行 |
| 推理增强 | 数学、逻辑推理能力突出 |
| 长上下文 | Hermes 3 支持 128K 上下文 |
| 开源商用 | 完全开放权重，Apache 2.0 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [NousResearch](https://nousresearch.com/) | 官方主页 |
| [HuggingFace](https://huggingface.co/NousResearch) | 模型权重下载 |
| [Ollama](https://ollama.com/library/hermes3) | 本地一键运行 |

## 选型建议

基于 Llama 的微调首选 Hermes 3；工具调用/Agent 场景选 70B；轻量部署选 8B；需要 128K 上下文选 Hermes 3 系列。