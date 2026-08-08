## 模型与定位

AI21 Jamba 系列采用混合 Mamba（SSM）+ Transformer 架构，在超长上下文下内存占用与推理速度显著优于纯 Transformer。2026-01-08 开源 Jamba2（3B dense 与 Mini 52B/12A MoE，Apache 2.0，256K 上下文）；2026-02-01 推出旗舰 API 模型 Jamba Large 1.7，另有 Jamba Mini 1.7 与 Jamba Reasoning 3B。面向长文档分析、企业 RAG 与私有化部署场景。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本 |
| 输出能力 | 文本、结构化输出、工具调用 |
| 推理模式 | 标准解码 + Reasoning 变体 |
| 典型模型名 | `jamba-large-1.7`、`jamba-mini-1.7`、Jamba2 3B / Mini |
| 上下文窗口 | 256K tokens |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `model` | 模型名，如 `jamba-large-1.7` |
| `messages` | 对话消息 |
| `max_tokens` | 最大输出长度 |
| `temperature` / `top_p` | 采样控制 |

## 调用与兼容性

支持 AI21 Studio、Amazon Bedrock、OpenRouter 三类平台，均为 OpenAI 兼容接口：

```bash
# AI21 Studio（OpenAI 兼容）
curl https://api.ai21.com/studio/v1/chat/completions \
  -H "Authorization: Bearer $AI21_API_KEY" \
  -d '{"model":"jamba-large-1.7","messages":[{"role":"user","content":"总结这段长文档"}]}'
```

Jamba2 权重以 Apache 2.0 开放，可通过 vLLM、SGLang、Ollama 本地部署。

## 版本与更新注意

Jamba2 开源版 2026-01-08 发布；Jamba Large 1.7 2026-02-01 发布。API 定价：Large 1.7 为 $2/$8 每百万 token，Mini 1.7 为 $0.20/$0.40。

## 选型建议

长文档理解、企业知识库 RAG 场景优先；需要私有化部署选开源 Jamba2，追求旗舰质量用 Jamba Large 1.7 API。