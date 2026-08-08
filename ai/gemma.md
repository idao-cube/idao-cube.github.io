## 模型与定位

Gemma 是 Google 基于 Gemini 同源技术打造的开放权重模型系列。**Gemma 4**（2026-07-21 发布）为最新一代，首发 `gemma-4-31b-it` 与 `gemma-4-26b-a4b-it`（MoE），AI Studio 与 Gemini API 可直接使用。Gemma 3 系列支持多模态（图像+文本），上下文窗口 128K。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| Gemma 4-31B | 31B | 长上下文 | ✅ | 通用对话、复杂推理、Agent |
| Gemma 4-26B-A4B | 26B（MoE 4B 激活） | 长上下文 | ✅ | 高效推理、低成本部署 |
| Gemma 3-12B | 12B | 128K | ✅ | 通用对话、代码辅助 |
| Gemma 3-27B | 27B | 128K | ✅ | 复杂推理、Agent |
| Gemma 3-4B | 4B | 128K | ✅ | 端侧推理、轻量服务 |
| Gemma 3-1B | 1B | 32K | ✅ | 超低资源、移动端 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Google AI Studio](https://ai.google.dev/) | 官方 API 服务 |
| [HuggingFace](https://huggingface.co/google) | 模型权重下载 |
| [Kaggle](https://www.kaggle.com/models/google/gemma) | 免费推理环境 |

## 选型建议

追求最新能力选 Gemma 4-31B，追求推理效率选 Gemma 4-26B-A4B（MoE）；本地/端侧部署可选 Gemma 3-4B；需要多模态选 Gemma 3 全系列。