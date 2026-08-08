## 模型与定位

适用于通用对话、复杂推理、代码生成、数学解题与结构化输出。基于自研 MoE 架构，具备高性价比与低推理成本优势。主力模型同时支持思考与非思考模式，可按任务类型灵活切换。2026 年 7 月 31 日发布的 V4-Flash 正式版大幅强化了 Agent 与代码能力，多项基准反超自家更大规模的 V4-Pro 预览版。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本 |
| 输出能力 | 文本、结构化 JSON、工具调用、Function Calling |
| 推理模式 | 支持思考（reasoning）与非思考双模式 |
| 典型模型名 | `deepseek-v4-flash`（正式版）、`deepseek-v4-pro`（正式版即将发布） |
| 上下文窗口 | 100 万 token（V4 系列），最大输出 38.4 万 token |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 生产环境推荐 `deepseek-v4-flash` 或 `deepseek-v4-pro` |
| `thinking` | 启用思考模式 | 复杂推理任务启用，简单问答可关闭 |
| `reasoning_effort` | 推理深度 | 可选 `low`、`medium`、`high`，高强度问题用 `high` |
| `temperature` | 控制随机性 | 事实任务 0.0-0.3，创作 0.5-0.8 |
| `max_tokens` | 限制输出长度 | 避免超长响应造成成本波动 |
| `stream` | 流式返回 | 聊天 UI 建议开启 |

## 调用与兼容性

兼容 OpenAI ChatCompletions 接口，base_url 保持不变。V4-Flash 正式版原生支持 Responses API 格式并针对性适配 Codex。另有 Anthropic 兼容接口（base_url: `https://api.deepseek.com/anthropic`）供 Anthropic SDK 用户直接迁移。

```bash
# OpenAI 兼容调用示例
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${DEEPSEEK_API_KEY}" \
  -d '{
    "model": "deepseek-v4-flash",
    "messages": [{"role": "user", "content": "Hello!"}],
    "thinking": {"type": "enabled"},
    "stream": false
  }'
```

## 版本与下线注意

> `deepseek-chat` 与 `deepseek-reasoner` 已于 **2026-07-24** 永久停用，请使用新模型名 `deepseek-v4-flash` / `deepseek-v4-pro`。

> **DeepSeek-V4-Flash 正式版（0731）** 于 2026-07-31 上线公测：总参数 284B、每 token 激活 13B，架构与预览版一致、仅重新后训练，Agent 与代码能力大幅跃升——DeepSWE 从 7.3 升至 54.4，Terminal Bench 2.1 达 82.7（反超 V4-Pro 预览版的 72.1）。权重当天以 MIT 协议开源（Hugging Face）。V4-Pro API 与 APP/WEB 端模型未变，正式版将尽快发布。

## 选型建议

日常对话与快速响应优先 `deepseek-v4-flash`，高精度任务优先 `deepseek-v4-pro`；Agent 编程与长程自动化任务可直接使用 V4-Flash 正式版（其智能体能力已超 Pro 预览版）；需要多步推理时启用思考模式并设置合理的 `reasoning_effort`。