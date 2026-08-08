## 产品与定位

适合在本地或服务器运行开源大模型（Llama、Qwen、Mistral 等），强调零依赖、高性能与跨平台部署。通过 GGUF 量化格式大幅降低显存占用，可在消费级硬件运行百亿参数模型。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 纯 C/C++ 实现 | 无外部依赖，适合嵌入式和精简环境 |
| GGUF 量化 | 支持 Q8_0 到 Q2_K 等多种量化级别，平衡体积与质量 |
| 多后端支持 | CUDA、Metal、ROCm、Vulkan、CPU |
| 推理模式 | 基础生成、流式输出、对话补全、Function Calling |
| OpenAI 兼容 | `llama-server` 提供 OpenAI Chat Completions 兼容接口 |

## 常用参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `-ngl` | GPU 层数 | 有独立显卡建议设高值加速 |
| `-c` / `--ctx-size` | 上下文窗口 | 受显存限制，建议 2048-8192 |
| `-t` / `--threads` | CPU 线程数 | 对齐物理核心数效果最佳 |
| `-mli` | 强制蒙特卡洛采样 | 提升推理质量但增加延迟 |
| `--temp` | 温度控制 | 精确任务 0.0-0.3，创作 0.5-0.8 |
| `--no-display` | 纯文本输出 | 用于脚本和管道处理 |
| `-fa` | Flash Attention | 大上下文加速并节省显存 |

## 调用与兼容性

通过 `llama-cli` 交互式对话或 `llama-server` 启动 API 服务。`llama-server` 默认端口 8080，提供与 OpenAI 完全兼容的 `/v1/chat/completions` 等接口。

```bash
# 直接运行对话
llama-cli -hf bartowski/Llama-3.2-3B-Instruct-GGUF:Q8_0

# 启动 API 服务器
llama-server -hf ggml-org/gemma-3-1b-it-GGUF -ngl 99

# 调用兼容接口
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "gemma-3-1b", "messages": [{"role": "user", "content": "Hello!"}]}'
```

## 版本与更新注意

项目更新频繁，GPU 支持和量化算法持续优化。建议定期拉取最新版本获取性能提升和 Bug 修复。

## 选型建议

需要完全离线、数据不上云、可控推理成本时首选 llama.cpp；配合 Ollama 可进一步简化模型管理和 API 封装。