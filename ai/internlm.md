## 模型与定位

书生·浦语（InternLM）由上海人工智能实验室研发，面向科研与产业应用，提供从轻量到超大规模的完整模型矩阵。**InternLM3-8B**（2025-01-15）为最新开源基座：仅用 4T 训练数据即超越同量级开源模型，训练成本节约 75%+，首次在通用模型上融合"深度思考 + 常规对话"双模式，通过系统提示词一键切换。InternLM2.5 系列（1.8B/7B/20B）仍为主力长上下文选项，7B-Chat-1M 支持百万 token。模型支持工具调用与 Agent 工作流。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| InternLM3-8B | 8B | 32K | ✅ | 深度思考+对话融合、轻量部署 |
| InternLM2.5-7B | 7B | 32K | ✅ | 长文档理解、RAG |
| InternLM2.5-20B | 20B | 200K | ✅ | 复杂推理、长上下文 |
| InternLM2.5-7B-Chat-1M | 7B | 1M | ✅ | 百万 token 超长上下文 |
| InternLM-XComposer2.5 | 7B | 8K | ✅ | 图文混合理解 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [书生·浦语 API](https://internlm.intern-ai.com.cn/) | 官方 API 服务 |
| [HuggingFace](https://huggingface.co/internlm) | 模型权重下载 |
| [ModelScope](https://modelscope.cn/organization/Shanghai_AI_Laboratory) | 国内镜像 |

## 选型建议

需要超长上下文（200K/1M）处理文档、RAG 场景首选 InternLM2.5-20B 或 7B-Chat-1M；轻量部署与思考/对话双模式选 InternLM3-8B；多模态场景选 XComposer 系列。体验入口：internlm-chat.intern-ai.org.cn。