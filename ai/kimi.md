## 模型与定位

Kimi 由 Moonshot AI（月之暗面）研发，专注于超长上下文处理、多模态理解与智能体任务。2026 年 7 月发布的 Kimi K3 以 2.8 万亿总参数登顶全球开源模型规模之王座；K2.7 Code 专精长程编程 Agent 任务；K2.6 为通用旗舰。主线模型（K2 系列）全部开源，权重可从 Hugging Face 下载，支持本地/私有化部署。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像（多模态模型） |
| 输出能力 | 文本、结构化 JSON、工具调用 |
| 推理模式 | 支持思考与非思考双模式 |
| 典型模型名 | `kimi-k3`（旗舰）、`kimi-k2.7-code`（编程）、`kimi-k2.6`（通用）、`kimi-k2-thinking`（推理） |
| 上下文窗口 | 256K（K2 系列），1M（K2.7 Code） |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 编程场景推荐 `kimi-k2.7-code`，通用场景 `kimi-k2.6` |
| `temperature` | 控制随机性 | 事实任务 0.0-0.3，创作 0.5-0.8 |
| `top_p` | 核采样 | 与 `temperature` 二选一优先调 |
| `max_tokens` | 限制输出长度 | 避免超长响应造成成本波动 |
| `stream` | 流式返回 | 聊天 UI 建议开启 |

## 调用与兼容性

兼容 OpenAI ChatCompletions 接口，通过 Moonshot AI API 调用；K2.7 Code 同时兼容 OpenAI 与 Anthropic SDK 格式（仅需修改 base URL）。

```bash
# OpenAI 兼容调用示例
curl https://api.moonshot.cn/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${MOONSHOT_API_KEY}" \
  -d '{
    "model": "kimi-k2.6",
    "messages": [{"role": "user", "content": "你好，请介绍一下自己"}],
    "stream": false
  }'
```

## 版本与下线注意

> `kimi-k2` 系列模型已于 **2026 年 5 月 25 日下线**，`kimi-latest` 已于 2026 年 1 月 28 日停止新用户使用，请迁移至 `kimi-k2.6` 及以上版本。

> **Kimi K3**（2026-07）：2.8 万亿总参数开源模型，智能指数 57.1、Coding 76.2（Moonshot 全系最高），定价 $3/$15 per 1M。

> **Kimi K2.7 Code**（2026-06-12）：专为代码场景的开源模型（MoE 384 专家、8+1 激活），Kimi Code Bench v2 +21.8%、推理 token 消耗降约 30%；与 K2.6 同价（¥6.5 输入/¥27 输出、缓存命中 ¥1.3）；**必须开启思考模式**，手动关闭会报错；2026-06-15 上线高速版（5-6 倍速、6x 速度仅 2x 价格）。

## 选型建议

高精度通用任务选 Kimi K3 或 K2.6，编程与长程 Agent 任务选 K2.7 Code（需开思考模式），数学链式推理选 K2 Thinking；需要超长上下文选 256K/1M 版本。K2 系列权重开源，可本地部署控制成本。