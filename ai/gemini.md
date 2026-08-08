## 模型与定位

Gemini 强调多模态与长上下文能力，适用于复杂资料分析、图文问答、自动化任务与开发助手。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像、音频、视频（按模型能力） |
| 输出能力 | 文本、结构化内容、工具调用 |
| 推理模式 | 提供面向质量与速度的不同层级 |
| 典型模型名 | `gemini-3.6-flash`、`gemini-3.5-flash`、`gemini-3.5-flash-lite`、`gemini-3.1-pro-preview`（以官方列表为准） |
| 上下文窗口 | 支持超长上下文能力，具体上限按模型版本 |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型版本 | 质量优先选 Pro，成本优先选 Flash |
| `max_output_tokens` | 限制输出长度 | 长文生成需明确上限 |
| `response_mime_type` | 约束返回格式 | 结构化场景可指定 JSON |
| `tools` | 声明工具能力 | 搜索或函数调用时显式开启 |
| `safety_settings` | 安全阈值控制 | 企业场景建议统一策略 |

> 注：2026-07-21 起 `temperature`、`top_p`、`top_k` 采样参数已标记弃用，新项目建议使用 Interactions API 与模型默认采样。

## 调用与兼容性

可通过 Gemini API 与 Google Cloud 生态接入，适合与检索、存储、工作流系统联动。Interactions API（2026-06 GA）为推荐的新构建方式，支持模型与 Agent 混合编排。

## 版本与下线注意

2026-07-21 GA：`gemini-3.6-flash`（token 效率较 3.5 Flash 提升 17%、DeepSWE 最高 +65%、价格更低）、`gemini-3.5-flash`（agentic/coding 前沿，`gemini-flash-latest` 指向）、`gemini-3.5-flash-lite`（350 tokens/s 低成本子代理）；`gemini-3.1-pro-preview` 为 Pro 档预览；Gemini 3.5 Pro 正在测试、Gemini 4 预训练中。生产环境建议使用稳定版并设置灰度切换。

## 选型建议

多模态和长文档理解优先 Gemini；对响应速度敏感的聊天与工具编排优先 Flash 系列。