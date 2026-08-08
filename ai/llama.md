## 模型与定位

Llama 是 Meta 发布的开源权重模型家族，适合私有化部署、定制微调与多框架推理。2026-04-08 发布 **Llama 5**：600B+ 参数 MoE（约 52B 激活参数），5M token 超长上下文，原生多模态（文本/图像/视频/音频），原生 Agent 工具使用能力，SWE-bench Verified 约 74%、MMLU-Pro 约 87%，提供 8B/70B/405B 等尺寸，采用 Llama Community License 3.0（月活 ≤1B 的用户可免费商用）。另有闭源旗舰 Muse Spark 作为 Meta AI 助手引擎，经 Meta Model API 提供。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像、视频、音频（Llama 5 原生多模态） |
| 输出能力 | 文本、结构化输出、原生 Agent 工具调用 |
| 推理模式 | 标准解码 + 工具使用（agentic） |
| 典型模型名 | `Llama-5`（开源权重）、`muse-spark-1.1`（API） |
| 上下文窗口 | Llama 5 最高 5M tokens；Muse Spark 1M |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `temperature` | 随机性控制 | 问答任务 0.2-0.6 |
| `top_p` / `top_k` | 候选采样 | 先调 `temperature` 再调采样 |
| `max_tokens` / `max_new_tokens` | 输出长度上限 | 避免长尾超时 |
| `repetition_penalty` | 抑制重复 | 长文生成可适度提高 |
| `stop` | 停止词 | 协议化输出必须配置 |
| `seed` | 复现实验 | 评测与回归时固定 |
| `num_ctx` | 上下文窗口（部署参数） | 结合显存和延迟平衡 |

## 调用与兼容性

开源权重可经 vLLM、TGI、SGLang、Ollama 等部署（Groq、Together、Fireworks、Ollama 均已支持 Llama 5）。云端 API 走 Meta Model API（base `https://api.meta.ai/v1`，模型 `muse-spark-1.1`），同时兼容 OpenAI SDK 与 Anthropic Messages 协议，新账户赠送 $20 免费额度，OpenCode 内置 Meta provider：

```bash
curl https://api.meta.ai/v1/chat/completions \
  -H "Authorization: Bearer $META_API_KEY" \
  -d '{"model":"muse-spark-1.1","messages":[{"role":"user","content":"你好"}]}'
```

## 版本与下线注意

Llama 4 → Llama 5 的 tokenizer、chat template、微调均不兼容，升级需重新微调；开源 Llama 5 与闭源 Muse Spark 并行提供，按部署方式选择。开源模型迭代快，建议锁定模型哈希或镜像标签。

## 选型建议

需要可离线、可审计、可定制能力的场景选 Llama 5 开源权重；希望免运维、接受闭源时选 Meta Model API（Muse Spark）。复杂任务可结合 MoE 或更大参数模型做路由。