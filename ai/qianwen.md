## 模型与定位

千问（Qwen）系列是阿里云通义千问提供的通用大语言模型，覆盖从轻量到超大规模的模型矩阵，支持文本生成、代码编写、数学推理、多模态理解。Qwen3.6（2026-04-01 发布）引入混合思考模式并默认开启；2026 年中阿里云百炼已上线 qwen3.8-max、qwen3.7-plus、qwen3.7-flash 等最新型号，开源侧提供 qwen3.6-35b-a3b（Apache 2.0）与 qwen3.6-27B 等权重。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像（多模态模型） |
| 输出能力 | 文本、结构化 JSON、工具调用、Function Calling |
| 推理模式 | 混合思考模式（默认开启，可用 /think /no_think 切换） |
| 典型模型名 | `qwen3.8-max`、`qwen3.7-plus`、`qwen3.7-flash`、`qwen3.6-plus`、`qwen3.6-max-preview`、`qwen3.6-35b-a3b` |
| 上下文窗口 | 按模型不同 32K-256K 不等（以官方模型页为准） |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 生产环境固定版本或使用别名 |
| `temperature` | 控制随机性 | 事实任务 0.0-0.3，创作 0.5-0.9 |
| `thinking_budget` | 思考 Token 上限 | 控制思考型模型成本 |
| `max_tokens` | 限制输出长度 | 避免超长响应造成成本波动 |
| `response_format` | 结构化输出 | 需要稳定解析时使用 JSON |
| `tools` | 工具定义 | Agent 场景显式声明可用工具 |
| `stream` | 流式返回 | 聊天 UI 建议开启 |

## 调用与兼容性

支持 OpenAI 兼容接口和原生 DashScope 接口两种接入方式。注意区分区域端点：北京（`dashscope.aliyuncs.com`）和新加坡（`dashscope-intl.aliyuncs.com`），API Key 与区域绑定。

```bash
# OpenAI 兼容调用示例
curl https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${DASHSCOPE_API_KEY}" \
  -d '{
    "model": "qwen3.7-plus",
    "messages": [{"role": "user", "content": "你好，请介绍一下自己"}],
    "stream": false
  }'
```

## 版本与下线注意

模型持续迭代，qwen3.6 起旧版 qwen-plus/qwen-max 等别名逐步迁移至新系列；生产环境建议锁定可回滚的模型版本，并定期检查阿里云百炼控制台获取最新模型列表。

## 选型建议

日常助手选 `qwen3.7-flash`（高速低延迟），高质量任务选 `qwen3.7-plus` 或 `qwen3.8-max`，代码任务选 Qwen Coder 系列；需要私有化部署时使用开源 `qwen3.6-35b-a3b`。