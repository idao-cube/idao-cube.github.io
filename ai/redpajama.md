## 模型与定位

RedPajama 由 Together AI 主导，基于完全透明、可复现的数据集训练，旨在提供真正开放的大语言模型。RedPajama-INCITE 系列涵盖对话、指令遵循和代码生成版本，Apache 2.0 协议完全开放商用。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 商业可用 | 适用场景 |
| --- | --- | --- | --- | --- | --- |
| RedPajama-3B | 3B | 2K | ✅ | ✅ | 轻量部署、边缘设备 |
| RedPajama-7B | 7B | 2K | ✅ | ✅ | 通用对话、实验 |
| RedPajama-7B-Chat | 7B | 2K | ✅ | ❌ | 对话应用 |
| RedPajama-INCITE-7B | 7B | 2K | ✅ | ✅ | 指令遵循、Agent |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| 完全透明 | 训练数据、代码、权重全部开放 |
| 可复现 | 提供完整训练流程，可重复实验 |
| 多版本 | Chat、Instruct、Code 多个专用版本 |
| Apache 2.0 | 完全开放商用，无限制 |
| Together 生态 | 与 Together AI 平台深度集成 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Together AI](https://www.together.ai/) | 官方 API 与训练平台 |
| [HuggingFace](https://huggingface.co/togethercomputer) | 模型权重下载 |
| [GitHub](https://github.com/togethercomputer) | 训练代码与数据集 |

## 选型建议

开放研究首选 RedPajama；需要透明可复现的训练流程选此系列；Together AI 用户可直接使用 API。