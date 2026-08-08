## 模型与定位

Apple Foundation Models 是 Apple 为开发者提供的 Swift 原生模型框架，于 2026-06-08 WWDC26 宣布开源核心与 utilities 包。提供四种模型接入方式：端侧 System Language Model（2026 重建，支持直接图像输入与更好的推理/工具调用）、PrivateCloudComputeLanguageModel（PCC，32K 上下文、无账号无 API key、watchOS 27 支持）、CoreAILanguageModel（Apple Neural Engine 加速）、MLXLanguageModel（Hugging Face MLX-Community 开源模型）。LanguageModel 协议可接入任意模型，Anthropic、Google 已宣布将发布官方 Swift 包。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像（端侧模型直接图像输入） |
| 输出能力 | 文本、工具调用、Spotlight RAG、OCR/条形码识别 |
| 推理模式 | `reasoningLevel` 可控（PCC） |
| 典型模型名 | `SystemLanguageModel`、`PrivateCloudComputeLanguageModel`、MLX 模型 ID |
| 上下文窗口 | PCC 32K；端侧取决于设备内存 |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `reasoningLevel` | PCC 推理深度控制 |
| `contextOptions` | 上下文注入配置 |
| `GenerationOptions` | 采样与生成长度 |
| Dynamic Profiles | 按场景切换模型配置 |

## 调用与兼容性

Swift（iOS 26.4+ / macOS / iPadOS / watchOS 27 / visionOS）+ Python SDK + fm CLI。iOS 26.4 增加上下文检查与 Token 计数 API。示例（Swift）：

```swift
import FoundationModels

let model = PrivateCloudComputeLanguageModel()
let response = try await model.generate("用一句话介绍你自己")
print(response.text)
```

## 版本与更新注意

2026-06 WWDC26 发布框架开源与 WWDC27 预览；端侧系统模型 2026 年重建，推理与工具调用能力显著提升；PCC 新增 watchOS 27 支持。

## 选型建议

Apple 生态应用（App、快捷指令、Siri 集成）首选；PCC 适合注重隐私且需大模型能力的场景；App Store 小企业计划（年下载 <200 万）PCC 免云端 API 成本。