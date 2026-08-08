## 模型与定位

腾讯混元是腾讯自研大模型体系。**混元 Hy3 正式版**（2026-07-06 发布）：MoE 架构，295B 总参数/21B 激活参数，256K 上下文，快慢思考融合（no_think/think_low/think_high 三档），Apache 2.0 开源；发布当日（day-0）即上架 HuggingFace、ModelScope、OpenRouter，并适配 Hermes、Kilo、Cline、OpenClaw、OpenCode、CherryStudio 等工具。Agent 任务解决率从 72% 提升至 90%、耗时降低 34%，性能比肩 2-5 倍参数的旗舰模型。适合中文问答、办公协同、客服助手、营销内容生成及行业化 Agent 场景。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本（多模态配套模型） |
| 输出能力 | 文本、结构化内容、工具调用 |
| 推理模式 | 快慢思考融合：no_think / think_low / think_high |
| 典型模型名 | hunyuan-hy3（正式版）、hunyuan-hy3-preview（2026-04-23 重建首版） |
| 上下文窗口 | 256K |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 选择模型 | 生产建议固定 hunyuan-hy3 |
| `thinking` | 思考深度控制 | 简单任务 no_think、复杂任务 think_high |
| `temperature` | 控制发散程度 | 严谨业务建议低温 |
| `top_p` | 核采样参数 | 与温度联合微调 |
| `max_tokens` | 限制输出长度 | 防止响应过长影响延迟 |
| `stream` | 流式输出 | 对话产品建议开启 |

## 调用与兼容性

支持腾讯云 API 接入（cloud.tencent.com/product/tclm），与腾讯云安全、存储、监控等服务集成；开源权重可在 HuggingFace/ModelScope 下载自部署。

## 版本与下线注意

旧版 hunyuan-turbo、hunyuan-turbos 等模型自 2026-05-22 起逐步下线，迁移至 TokenHub；Hy3 preview 为过渡版本，正式版已替代。配套模型：Hy-MT2-Pro（翻译）、Hy-Role-Latest（角色扮演）、Hy3D 3.1、HY Image 3.0。

## 选型建议

中文业务系统和云上集成需求强时可优先评估混元 Hy3；开源权重适合私有化部署，Agent 工作流建议显式声明 thinking 档位以平衡质量与延迟；定价：输入 ¥1/百万 token、输出 ¥4、缓存命中 ¥0.25，性价比突出。