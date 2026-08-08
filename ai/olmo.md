## 模型与定位

OLMo（Open Language Model）由 Allen Institute for AI（AI2）研发，是目前最透明的开源大模型之一。不仅开放权重，还开放完整训练数据、训练代码和评估流程，真正推动 AI 研究的可复现性。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 商业可用 | 适用场景 |
| --- | --- | --- | --- | --- | --- |
| OLMo 1.7-7B | 7B | 2K | ✅ | ✅ | 研究、实验 |
| OLMo 2-7B | 7B | 4K | ✅ | ✅ | 改进推理、研究 |
| OLMo 2-13B | 13B | 4K | ✅ | ✅ | 通用对话、研究 |
| OLMo 2-70B | 70B | 4K | ✅ | ✅ | 高性能研究、Agent |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| 完全透明 | 训练数据、代码、权重、评估全部开放 |
| 可复现 | 提供完整训练流程，可重复实验 |
| 研究友好 | 专为 AI 研究设计，便于学术使用 |
| Apache 2.0 | 完全开放商用 |
| 多规模 | 7B~70B 完整规模矩阵 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Allen AI](https://allenai.org/olmo) | 官方主页 |
| [HuggingFace](https://huggingface.co/allenai) | 模型权重下载 |
| [AI2 Playground](https://playground.allenai.org/) | 在线体验 |

## 选型建议

AI 研究首选 OLMo 2-70B；需要复现实验选完整 OLMo 栈；学术论文引用推荐 OLMo 系列。