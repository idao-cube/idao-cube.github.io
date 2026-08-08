## 模型与定位

Nano Banana 是 Google DeepMind 推出的新一代图像生成与编辑模型，最新版本为 Nano Banana 2（Gemini 3.1 Flash Image）。通过对话式界面创建和编辑图像，支持移除/替换对象、换背景、改风格等操作，输出分辨率覆盖 512px 至 4K。

## 参数速览

| 模型 | 分辨率 | 特性 | 访问方式 |
| --- | --- | --- | --- |
| Nano Banana（基础版） | 最高 2K | 对话式编辑、风格迁移 | Gemini 免费版 |
| Nano Banana Pro | 最高 4K | 高保真、精细编辑 | Google AI Pro/Plus/Ultra |
| Nano Banana 2 | 512px–4K | 更快速度、更锐利细节 | Gemini API / AI Studio |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Gemini App](https://gemini.google.com/) | 免费使用基础版 |
| [Google AI Studio](https://aistudio.google.com/) | 开发者 API 接入 |
| [Vertex AI](https://cloud.google.com/vertex-ai) | 企业级部署 |

## 选型建议

日常创作选 Gemini App 免费版；需要高精度 4K 输出选 Pro 版；开发者集成用 Gemini API + Nano Banana 2。