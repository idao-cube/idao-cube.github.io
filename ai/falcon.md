## 模型与定位

Falcon 由阿联酋技术创新研究院（TII）研发，以 Apache 2.0 协议完全开放商用。Falcon3 系列在推理、代码、数学等能力上全面提升，支持 128K 长上下文，提供从 3B 到 40B 的完整规模矩阵。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| Falcon3-3B | 3B | 128K | ✅ | 端侧部署、轻量服务 |
| Falcon3-10B | 10B | 128K | ✅ | 通用对话、代码辅助 |
| Falcon3-40B | 40B | 128K | ✅ | 复杂推理、Agent |
| Falcon2-11B | 11B | 4K | ✅ | 稳定生产部署 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [HuggingFace](https://huggingface.co/tiiuae) | 模型权重下载 |
| [Falcon LLM](https://falconllm.tii.ae/) | 官方文档 |
| [Ollama](https://ollama.com/library/falcon3) | 本地一键运行 |

## 选型建议

端侧部署选 Falcon3-3B；通用场景选 Falcon3-10B；高性能需求选 Falcon3-40B。Apache 2.0 协议可放心商用。