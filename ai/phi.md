## 模型与定位

Phi 系列由微软研发，专注于"小模型大智慧"——在 3B~15B 参数量级实现超越同规模的推理能力。Phi-4 系列在数学推理、代码生成和指令遵循方面表现突出；**Phi-4-reasoning**（2025-04-30，14B，MIT）为推理版，输出分"推理链 + 总结"两段；**Phi-4-reasoning-vision-15B**（2026-03-04，MIT）为多模态推理版，基于 SigLIP-2 视觉编码器，擅长数学/科学推理与 GUI 界面理解，支持 Azure Foundry 托管。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| Phi-4-reasoning-vision-15B | 15B | 16K | ✅ MIT | 多模态推理、GUI 理解、科学推理 |
| Phi-4-reasoning | 14B | 32K | ✅ MIT | 数学、科学、代码推理 |
| Phi-4-mini | 3.8B | 128K | ✅ | 端侧部署、低延迟 |
| Phi-4 | 14B | 128K | ✅ | 数学推理、代码生成 |
| Phi-3-medium | 14B | 128K | ✅ | 通用对话、RAG |
| Phi-3-mini | 3.8B | 128K | ✅ | 移动端、边缘设备 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Azure Foundry](https://ai.azure.com/) | 官方 API 服务（含 Phi-4-reasoning-vision 托管） |
| [HuggingFace](https://huggingface.co/microsoft) | 模型权重下载 |
| [Ollama](https://ollama.com/library/phi4) | 本地一键运行 |

## 选型建议

本地/移动端部署首选 Phi-4-mini；数学和代码推理场景选 Phi-4-reasoning；多模态推理与 GUI/文档理解选 Phi-4-reasoning-vision-15B（Azure Foundry 托管免 GPU）；需要 Azure 生态集成的首选 Phi 系列。