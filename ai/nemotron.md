## 模型与定位

NVIDIA Nemotron 3 是 2026 年 3-6 月发布的开放模型家族，采用 LatentMoE（Mamba-2 + MoE + Attention 混合）架构与 MTP 加速，开放权重、训练数据与配方。旗舰为 Nemotron 3 Ultra 550B-A55B（2026-06-04），另有 Super 120B-A12B（2026-03-11）与 Nano 30B-A3B。面向企业私有化推理与 Agent 工作流设计，支持英、法、西、意、德、日、韩、印地、葡、中十种语言。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本（多语言） |
| 输出能力 | 文本、工具调用、Agent 推理轨迹 |
| 推理模式 | 可配置 `enable_thinking`（思考开/关） |
| 典型模型名 | `nvidia/nemotron-3-ultra`、`nvidia/nemotron-3-super` |
| 上下文窗口 | 最高 1M tokens |
| 许可 | OpenMDW-1.1 类开放许可 |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `temperature` / `top_p` | 采样控制 |
| `max_tokens` | 最大输出长度 |
| `extra_body.chat_template_kwargs.enable_thinking` | 开启/关闭思考模式 |
| `force_nonempty_content` | coding agent 建议设为 true，避免空回复 |

## 调用与兼容性

OpenAI 兼容接口，支持 vLLM、SGLang、Ollama、llama.cpp、TensorRT-LLM 部署，也可通过 NVIDIA NIM 微服务一键启动：

```bash
# NIM 示例（OpenAI 兼容）
curl https://integrate.api.nvidia.com/v1/chat/completions \
  -H "Authorization: Bearer $NGC_API_KEY" \
  -d '{"model":"nvidia/nemotron-3-ultra","messages":[{"role":"user","content":"解释 MoE 架构"}],"extra_body":{"chat_template_kwargs":{"enable_thinking":false}}}'
```

## 版本与更新注意

Nemotron 3 系列为 2026 年最新家族；Ultra 550B-A55B 推理建议 4x GB200/B200 级 GPU。家族持续迭代，关注 NVIDIA 开发者博客获取新版本。

## 选型建议

GPU 资源充足的企业私有化 Agent 场景选 Ultra；高频长上下文业务选 Super；边缘与轻量场景用 Nano。模型自托管可避免按 token 计费。