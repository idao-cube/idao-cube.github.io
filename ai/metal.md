## 产品与定位

Mistral AI 推出的开源桌面应用，提供简单易用的界面运行本地大模型。支持 Llama、Mistral 等开源模型转换和运行，GPU 加速推理，隐私优先。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 模型市场 | 一键下载和切换模型 |
| 本地运行 | 完全离线，隐私安全 |
| GPU 加速 | CUDA/Metal 硬件加速 |
| 对话界面 | 简洁易用的聊天界面 |
| 多模型 | Llama、Mistral、Qwen 等开源模型 |
| API 服务 | 本地 OpenAI 兼容接口 |

## 常用参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 选择模型 | 根据硬件配置选择 |
| `temperature` | 采样温度 | 精确任务低温，创意任务高温 |
| `max_tokens` | 输出限制 | 控制响应长度 |
| `context_length` | 上下文长度 | 受显存限制 |

## 调用与兼容性

```bash
# 通过 Metal API 调用
curl http://localhost:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "mistral-small",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## 版本与更新注意

活跃开发中，支持最新开源模型。macOS/Linux/Windows 均支持。

## 选型建议

需要本地运行开源模型、隐私敏感场景首选 Metal；与 Ollama 功能重叠但界面更友好。