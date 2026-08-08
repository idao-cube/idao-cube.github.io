## 模型与定位

阶跃星辰（StepFun）是国产大模型公司，主打多模态与高效推理。当前旗舰开源模型为 **Step 3.7 Flash**（2026-05-29 发布，Apache 2.0）：MoE 架构，196B 总参 + 1.8B ViT 视觉编码 / 11B 激活，最高 400 tokens/s，256K 上下文，三档推理深度（reasoning: low/medium/high），面向 Agent 生产化——针对 Agent、Coding、Search、多模态工作流深度优化。适用于智能对话、代码生成、信息处理、自动化任务与 Agent 场景，适合希望快速接入高性价比新模型的团队。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像（1.8B ViT 视觉编码） |
| 输出能力 | 文本、结构化输出、工具调用 |
| 推理模式 | reasoning: low / medium / high 三档 |
| 典型模型名 | step-3.7-flash（开源）、Step3（2025-07，321B/38B 多模态推理 MFA+AFD） |
| 上下文窗口 | 256K |
| 参数量 | 196B 总参 / 11B 激活（MoE）+ 1.8B ViT |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 选择模型 | 按任务难度做分层路由 |
| `reasoning` | 思考深度 | 简单任务 low、复杂任务 high |
| `temperature` | 输出多样性 | 固定流程任务建议低温 |
| `top_p` | 采样范围 | 与温度联合微调 |
| `max_tokens` | 输出长度控制 | 线上建议设置硬上限 |
| `tools` | 工具调用 | Agent 任务建议开启 |

## 调用与兼容性

平台：platform.stepfun.com（国内）/ platform.stepfun.ai（海外），OpenAI/Anthropic 双兼容；OpenRouter、NVIDIA NIM 可用。开源权重 HuggingFace 提供 BF16/FP8/NVFP4/GGUF 多格式，支持 vLLM、SGLang、llama.cpp、Transformers 部署。

## 版本与下线注意

Step 3.7 Flash 为当前开源主线（2026-05-29）；早期 Step3（2025-07）、Step 2、Step 1 系列为旧版。模型名称和能力会随版本迭代调整，建议将核心参数配置化管理；注意区分国内/海外平台端点与模型 ID。

## 选型建议

如果团队需要兼顾推理速度与 Agent 能力、并希望开源可控，Step 3.7 Flash 适合作为多模型策略中的增量选项；400 tokens/s 的高速推理适合高并发交互产品，256K 上下文适合长文档与多轮 Agent 任务。