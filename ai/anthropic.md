## 公司与产品定位

Anthropic 是一家专注于 AI 安全研究的公司，旗下的 Claude 系列模型强调诚实性、可靠性和帮助性。提供从 Haiku 到 Opus（以及限量顶级档 Fable/Mythos）的多档位选择，满足从快速响应到复杂推理的各类需求。Claude 生态同时延伸至 Claude Code（终端编程助手）、Voice Mode、auto-fix 与 Computer Use CLI 等 Agent 能力。

## 参数速览（2026-08 当前模型）

| 模型 | 模型 ID | 上下文 | 输入/输出（$ per 1M） | 定位 |
| --- | --- | --- | --- | --- |
| Claude Fable 5 | claude-fable-5 | 1M | $10 / $50 | 顶级档（limited availability） |
| Claude Mythos 5 | claude-mythos-5 | 1M | $10 / $50 | 顶级档（限量，见 glasswing） |
| Claude Opus 5 | claude-opus-5 | 1M | $5 / $25 | 当前 Opus 旗舰 |
| Claude Opus 4.8 | claude-opus-4-8 | 1M | $5 / $25 | Opus 主力档 |
| Claude Opus 4.7 | claude-opus-4-7 | 1M | $5 / $25 | 前代 Opus，长程 Agent |
| Claude Sonnet 5 | claude-sonnet-5 | 1M | $2 / $10（2026-08-31 前推广价，之后 $3/$15） | 生产工作负载、平衡档 |
| Claude Sonnet 4.6 | claude-sonnet-4-6 | 1M | $3 / $15 | 前代 Sonnet |
| Claude Haiku 4.5 | claude-haiku-4-5 | 200K | $1 / $5 | 高频、低延迟 |

## 核心能力

| 能力 | 说明 |
| --- | --- |
| Tool Use | 可靠的函数调用与工具使用 |
| Extended Thinking | 长思考模式处理复杂推理 |
| Adaptive Thinking | Opus 系列专属，智能调节思考深度 |
| 上下文窗口 | Opus/Sonnet 1M tokens，Haiku 200K |
| 多模态 | 图像理解与处理（最高 2576 像素边长） |
| 新 tokenizer | Claude 4.7+ 使用新 tokenizer，同文本约多产生 30% token（成本评估需注意） |
| 生态能力 | Claude Code、Voice Mode、auto-fix（云端修 PR/CI）、Computer Use CLI |

## API 与接入方式

| 接入方式 | 说明 |
| --- | --- |
| 官方 Claude API | 原生接口，功能最完整（platform.claude.com） |
| Claude Platform on AWS | 企业托管，含区域端点（+10% 溢价） |
| Amazon Bedrock | 企业级托管部署 |
| Google Vertex AI | GCP 集成 |
| Microsoft Foundry | Azure 集成 |

## 版本与迁移注意

> Claude 3/4 旧系列（`claude-3-opus`、`claude-3-sonnet`、`claude-3-5-haiku`、`claude-sonnet-4-20250514`、`claude-opus-4-20250514`、`claude-sonnet-4` 等）已陆续停用/退役，请升级至 Opus 5/4.8、Sonnet 5 或 Haiku 4.5 系列。Sonnet 5 推广价 $2/$10 至 2026-08-31 截止，之后恢复 $3/$15；Fable 5 / Mythos 5 为限量顶级档。

## 选型建议

日常助手选 Haiku 4.5（快速便宜）；平衡场景选 Sonnet 5（8-31 前推广价更划算）；高精度长任务选 Opus 5 / 4.8；需要极致能力可评估 Fable 5 / Mythos 5 限量档；Agent 编码场景搭配 Claude Code 使用效果最佳。