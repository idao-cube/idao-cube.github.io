## 模型与定位

IBM Granite 是面向企业场景的开源模型家族，全部以 Apache 2.0 许可发布并提供密码学签名校验。Granite 4.1（2026-04-21）为 3B/8B/30B dense decoder-only 模型，覆盖多语言、编码、RAG、工具调用与结构化 JSON 输出；Granite 4.0（2025-09）采用 hybrid Mamba-2/Transformer + MoE 架构，内存占用降低 70%、推理加速 2 倍，是首个通过 ISO 42001 认证的开放模型。配套 Granite Vision / Speech / Embedding / Guardian 系列，构成完整企业模型矩阵。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本（另有视觉、语音、嵌入版本） |
| 输出能力 | 文本、结构化 JSON、工具调用、FIM 代码补全 |
| 推理模式 | 标准解码 |
| 典型模型名 | `granite-4.1-3b/8b/30b`、`ibm/granite-4-h-small`（watsonx） |
| 上下文窗口 | 视版本而定（4.0 系列优化长上下文） |
| 许可 | Apache 2.0，ISO 42001 认证 |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `model_id` | watsonx 模型 ID，如 `ibm/granite-4-h-small` |
| `project_id` | watsonx 项目标识 |
| `max_completion_tokens` | 最大输出长度 |
| `temperature` / `top_p` | 采样控制 |

## 调用与兼容性

watsonx.ai API：

```bash
curl https://us-south.ml.cloud.ibm.com/ml/v1/text/chat \
  -H "Authorization: Bearer $IBM_API_KEY" \
  -d '{"project_id":"<your-project-id>","model_id":"ibm/granite-4-h-small","messages":[{"role":"user","content":"写一段 Rust 冒泡排序"}],"max_completion_tokens":500}'
```

权重同步发布在 Hugging Face（ibm-granite/granite-4.1-3b/8b/30b），可经 vLLM、SGLang、Ollama 本地部署。

## 版本与更新注意

4.0（2025-09）→ 4.1（2026-04）持续迭代；所有版本 Apache 2.0 并带密码学签名。新增模型关注 IBM Granite 官方发布页。

## 选型建议

对开源许可合规、供应链安全有严格要求的企业场景首选；30B 覆盖复杂任务，8B 平衡性能与成本，3B 用于边缘部署。