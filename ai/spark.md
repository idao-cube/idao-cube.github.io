## 模型与定位

讯飞星火（Spark）是科大讯飞推出的国产通用大语言模型，以中文理解和语音能力见长，广泛用于智能客服、教育、办公自动化、车载语音等场景。当前提供 Lite、Pro、Pro-128K、Max、Max-32K、4.0 Ultra 六档语言模型，另有科技文献大模型（kjwx）与 X1 深度推理系列。API 同时提供 HTTP（OpenAI 兼容）与 WebSocket 两种接入方式。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本（Max/Ultra 支持联网搜索插件） |
| 输出能力 | 文本、结构化 JSON、Function Call（仅 Max/Ultra） |
| 推理模式 | 标准解码 + 4.0 Ultra 升级 X1.5 快思考 |
| 典型模型名 | `4.0Ultra`、`generalv3.5`(Max)、`max-32k`、`generalv3`(Pro)、`pro-128k`、`lite` |
| 上下文窗口 | Lite/Pro/Max 8K；Pro-128K 128K；Max-32K/Ultra 32K |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `model` | 模型名，见上表 |
| `messages` | 对话消息，system 角色仅 Max/Ultra 支持 |
| `stream` | 是否流式返回 |
| `chat_id` | 会话标识（非必填） |

## 调用与兼容性

HTTP（OpenAI 兼容）：

```bash
curl https://spark-api-open.xf-yun.com/v1/chat/completions \
  -H "Authorization: Bearer $SPARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"4.0Ultra","messages":[{"role":"user","content":"你好"}]}'
```

WebSocket 原生协议：`wss://spark-api.xf-yun.com/v4.0/chat`（需鉴权签名，适用流式语音交互场景）。

## 版本与下线注意

Max 版本套餐于 2026-03-10 下线，后端升级为 4.0 Ultra 并合并授权用量；4.0 Ultra 已升级 X1.5 快思考模式。新用户默认赠送 200 万 tokens 免费额度（限速 5 RPM）。

## 选型建议

中文场景、语音交互、需要 Function Call 的国内业务优先评估；预算敏感用 Lite/Pro，复杂任务直接上 4.0 Ultra。