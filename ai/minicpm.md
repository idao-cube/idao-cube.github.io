## 模型与定位

MiniCPM 由面壁智能（ModelBest）研发，主打"小模型大能力"——在 4B~9B 参数量级实现接近大模型的性能。**MiniCPM-o 4.5**（2026-02-04 开源旗舰）：9B 全双工全模态模型，"边看、边听、主动说"——原生全双工架构，基于 SigLip2 + Whisper-medium + CosyVoice2 + Qwen3-8B 构建；OpenCompass 77.6，超越 GPT-4o、接近 Gemini 2.5 Flash；Apache 2.0，端侧部署适配天数智芯、华为昇腾等 6 款国产芯片；2026-08-03 发布 GPTQ 量化版。**MiniCPM-V 4.6**（2026-05-17）：多模态图文理解，ViT 内提前压缩、视觉 token 压缩率 50%（12.5%× 加速），含 API 接口与免费公用密钥。

## 参数速览

| 模型 | 参数量 | 特点 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| MiniCPM-o 4.5 | 9B | 全双工全模态（看/听/说/文字） | ✅ Apache 2.0 | 实时语音对话、端侧部署 |
| MiniCPM-V 4.6 | 9B | 多模态图文，视觉 token 压缩 12.5%× | ✅ | 图文对话、OCR |
| MiniCPM3-4B | 4B | 128K 长上下文 | ✅ | 长上下文、RAG |
| MiniCPM-2.6 | 8B | 轻量通用 | ✅ | 端侧推理、边缘设备 |
| MiniCPM-SALA | 4B | 超低资源部署 | ✅ | 低功耗设备 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [面壁智能](https://www.modelbest.cn/) | 官方 API 服务 |
| [HuggingFace](https://huggingface.co/openbmb) | 模型权重下载（含 GPTQ 量化版） |
| [ModelScope](https://modelscope.cn/organization/OpenBMB) | 国内镜像 |
| [技术报告](https://arxiv.org/abs/2604.27393) | MiniCPM-o 4.5（arXiv 2604.27393） |

## 选型建议

实时语音/全模态交互首选 MiniCPM-o 4.5（支持国产芯片端侧部署）；多模态图文场景选 MiniCPM-V 4.6；长上下文 RAG 用 MiniCPM3-4B；低资源设备用 MiniCPM-SALA。