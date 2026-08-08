## 模型与定位

BLOOM 由 BigScience 跨国研究合作项目研发，176B 参数规模，支持 46 种自然语言及 13 种编程语言。以 BigScience RAIL License 开放权重，致力于推动 AI 技术的开放与民主化。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| BLOOM-560M | 560M | 2K | ✅ | 轻量实验、教学 |
| BLOOM-1.7B | 1.7B | 2K | ✅ | 边缘设备、低资源 |
| BLOOM-7.1B | 7.1B | 2K | ✅ | 通用对话、研究 |
| BLOOM-176B | 176B | 2K | ✅ | 研究、多语言任务 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [HuggingFace](https://huggingface.co/bigscience) | 模型权重下载 |
| [BigScience](https://bigscience.huggingface.co/) | 项目主页 |
| [Ollama](https://ollama.com/library/bloom) | 本地运行（小规模） |

## 选型建议

多语言研究首选 BLOOM-176B；资源受限场景选 BLOOM-7.1B；教学与实验可用小规模版本。