## 模型与定位

Mixtral 是 Mistral AI 的 MoE（混合专家）模型系列，采用 8 个专家子网络，每个 token 仅激活 2 个专家。Mixtral 8x7B 性能超越 Llama 2 70B，但推理成本仅为其 1/5。Mixtral 8x22B 进一步提升性能，支持 64K 上下文。

## 参数速览

| 模型 | 总参数 | 活跃参数 | 上下文 | 开源权重 | 商业可用 |
| --- | --- | --- | --- | --- | --- |
| Mixtral 8x7B | 47B | 13B | 32K | ✅ | ✅ |
| Mixtral 8x7B Instruct | 47B | 13B | 32K | ✅ | ✅ |
| Mixtral 8x22B | 141B | 39B | 64K | ✅ | ✅ |
| Mixtral 8x22B Instruct | 141B | 39B | 64K | ✅ | ✅ |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| MoE 架构 | 8 专家激活 2 个，高效推理 |
| 长上下文 | 8x22B 支持 64K 上下文窗口 |
| 多语言 | 支持英语、法语、德语、西班牙语等 |
| 代码生成 | 在 HumanEval、MBPP 上表现优异 |
| 推理效率 | 性能/成本比优于同规模密集模型 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Mistral AI](https://mistral.ai/) | 官方 API 服务 |
| [HuggingFace](https://huggingface.co/mistralai) | 模型权重下载 |
| [Ollama](https://ollama.com/library/mixtral) | 本地一键运行 |

## 选型建议

高效推理首选 Mixtral 8x7B；需要 64K 长上下文选 8x22B；本地部署用 Ollama；企业用户选 Mistral API。