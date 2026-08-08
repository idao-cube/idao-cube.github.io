## 模型与定位

适合实时问答、开发辅助、通用对话与 Agent 任务。当前旗舰为 **Grok 4.5**（2026-07-08 发布），在编码、反幻觉与 agentic 工具调用上领先，支持 reasoning + non-reasoning 双模式，并已集成进 GitHub Copilot；另有 Grok 4.3（1M 上下文）与专用编码模型 grok-build-0.1。Grok 5 尚未发布，仍在 Colossus 2 集群训练中。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本为主，部分模型支持多模态 |
| 输出能力 | 文本、结构化结果、工具调用 |
| 推理模式 | Grok 4.5 支持 reasoning + non-reasoning 双模式 |
| 典型模型名 | `grok-4.5`、`grok-4.3`、`grok-build-0.1`（以官方控制台实时列表为准） |
| 上下文窗口 | Grok 4.5 500K；Grok 4.3 1M |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 选择目标模型 | 生产环境避免使用临时别名 |
| `temperature` | 控制随机性 | 事实问答建议低温 |
| `top_p` | 采样裁剪 | 与温度搭配调参 |
| `max_tokens` | 输出长度控制 | 对成本与时延做硬限制 |
| `stream` | 流式输出 | 前端聊天建议开启 |
| `tools` | 工具调用声明 | 自动化任务建议开启 |

## 调用与兼容性

xAI API（docs.x.ai）同时兼容 OpenAI SDK 与 Anthropic SDK 协议：

```bash
curl https://api.x.ai/v1/chat/completions \
  -H "Authorization: Bearer $XAI_API_KEY" \
  -d '{"model":"grok-4.5","messages":[{"role":"user","content":"写一段 Python 快速排序"}],"tools":[]}'
```

## 版本与下线注意

Grok 5 未发布（2026-07 仍在训练，预计 Q3 2026+）；Grok 4.20-0309 等旧系列逐步下线。API 定价：Grok 4.5 $2.00/$6.00 每百万 token，Grok 4.3 $1.25/$2.50，grok-build-0.1 $1.00/$2.00。订阅：Free / SuperGrok $30/月 / SuperGrok Heavy $300/月。

## 选型建议

对实时性和 Agent 能力均有要求时优先评估 Grok 4.5；编码专项任务用 grok-build-0.1 控制成本；关键任务先做离线评测再放量。