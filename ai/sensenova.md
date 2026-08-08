## 模型与定位

日日新（SenseNova）是商汤科技的原生多模态智能体模型系列，基于 NEO-Unify 架构实现理解与生成一体。旗舰 SenseNova U1 支持图文混合输入与信息图/PPT 生成；U1 Pro（2026-07-18）面向长程任务交付级智能体；6.7 Flash-Lite 定位轻量多模态智能体，办公场景下比纯文本智能体节省约 60% token；U1 Lite 以 8B-MoT dense / A3B-MoT MoE 开源（GitHub OpenSenseNova）。配套 SenseNova-Skills / Cowork-Skills 技能库，可适配 OpenClaw、Hermes-Agent 等智能体框架。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像、网页、文档、图表（原生多模态） |
| 输出能力 | 文本、信息图、PPT、图像生成 |
| 推理模式 | 标准 + 长程智能体链路任务 |
| 典型模型名 | `SenseNova 6.7 Flash-Lite`、`SenseNova U1`、`SenseNova U1 Fast` |
| 上下文窗口 | 以官方平台文档为准 |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `model` | 模型名，如 6.7 Flash-Lite |
| `messages` | 支持图文混合内容 |
| `stream` | 是否流式返回 |
| 图片生成 | 输出信息图/PPT 的独立接口 |

## 调用与兼容性

通过商汤官方平台 API 接入；开源模型见 GitHub OpenSenseNova 仓库，可私有化部署。技能库适配 OpenClaw、Hermes-Agent 等智能体运行时：

```bash
# 官方平台 OpenAI 兼容接口示例
curl <platform-endpoint>/v1/chat/completions \
  -H "Authorization: Bearer $SENSENOVA_API_KEY" \
  -d '{"model":"SenseNova 6.7 Flash-Lite","messages":[{"role":"user","content":"分析这张图表并生成一页信息图"}]}'
```

## 版本与更新注意

U1 系列 2026 年发布；6.7 Flash-Lite 与 U1 Fast 面向真实办公工作流。计费采用 Token Plan：公测免费，Lite/Pro 付费档即将推出；国际版新用户首月免费，每模型每 5 小时 1500 次免费调用。

## 选型建议

办公智能体（数据分析、调研、PPT/信息图生成）场景首选；需要私有化的团队可直接采用 U1 Lite 开源版。