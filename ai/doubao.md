## 模型与定位

豆包（doubao）系列模型由字节跳动火山引擎提供，覆盖通用对话、创意生成、代码辅助等场景。通过火山方舟平台调用，支持多模态理解与高并发低延迟推理，性价比优势明显。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像 |
| 输出能力 | 文本、结构化 JSON、工具调用 |
| 推理模式 | 支持常规生成与推理型模型 |
| 典型模型名 | `doubao-seed-1-8-251228`、`doubao-pro`、`doubao-lite` |
| 上下文窗口 | 256K（以控制台实际可用模型为准） |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 生产环境固定版本或使用别名 |
| `temperature` | 控制随机性 | 事实任务 0.0-0.4，创作 0.6-1.0 |
| `top_p` | 核采样 | 与 `temperature` 二选一优先调 |
| `max_tokens` | 限制输出长度 | 避免超长响应造成成本波动 |
| `stream` | 流式返回 | 聊天 UI 建议开启 |

## 调用与兼容性

兼容 OpenAI ChatCompletions 接口，通过火山方舟 API 调用。支持 SDK 与 REST API 两种方式接入。

```bash
# OpenAI 兼容调用示例
curl https://ark.cn-beijing.volces.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${VOLCANO_ENGINE_API_KEY}" \
  -d '{
    "model": "doubao-seed-1-8-251228",
    "messages": [{"role": "user", "content": "Hello!"}],
    "stream": false
  }'
```

## 版本与下线注意

模型版本标注日期为版本冻结日期，请定期检查火山方舟控制台获取最新可用模型列表。

## 选型建议

高精度任务选最新大版本，日常对话选轻量模型；火山方舟同时提供第三方模型（如 Kimi K2.5、GLM-4.7 等），可通过统一 API 调用。