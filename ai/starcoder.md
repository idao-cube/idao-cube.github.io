## 模型与定位

StarCoder 由 BigCode（Hugging Face + ServiceNow）联合研发，专为代码任务优化。StarCoder2 系列覆盖 80+ 编程语言，支持 16K 上下文，在代码生成、补全、解释和调试方面表现优异。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| StarCoder2-3B | 3B | 16K | ✅ | 轻量代码补全 |
| StarCoder2-7B | 7B | 16K | ✅ | 代码生成、解释 |
| StarCoder2-15B | 15B | 16K | ✅ | 复杂代码任务 |
| StarCoderBase-15B | 15B | 8K | ✅ | 多语言代码支持 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [HuggingFace](https://huggingface.co/bigcode) | 模型权重下载 |
| [BigCode](https://www.bigcode-project.org/) | 项目主页 |
| [Ollama](https://ollama.com/library/starcoder2) | 本地一键运行 |

## 选型建议

IDE 代码补全选 StarCoder2-3B；代码生成与重构选 StarCoder2-7B；复杂工程任务选 StarCoder2-15B。