## 模型与定位

MiniMax 的 **M2 系列**专为 Agent 与代码而生。演进路径：**M2**（2025-10-27，229.9B 总参/9.8B 激活、192K 原生上下文、256 细粒度专家）→ M2.1 → **M2.5**（2026-02-12）→ **M2.7**（2026-03-18，含 M2.7/M2.7-highspeed）。M2.7 的一大特性是自我迭代——能自主调试训练运行、修改自身 scaffold。权重开源（HuggingFace），支持 SGLang/vLLM 部署。配套 MiniMax Agent（agent.minimaxi.com）与 Office Skills（Word/PPT/Excel）。适合对话助手、代码辅助、智能体、营销内容生成与多模态应用集成。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本为主（M2 系列），按模型支持语音/图像 |
| 输出能力 | 文本、结构化结果、工具调用 |
| 推理模式 | M2.7 / M2.7-highspeed（高速档） |
| 典型模型名 | MiniMax-M2.7、MiniMax-M2.5、MiniMax-M2.1、MiniMax-M2 |
| 上下文窗口 | 192K 原生上下文 |
| 参数量 | 229.9B 总参 / 9.8B 激活（MoE 256 专家） |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 模型选择 | Agent 任务建议 M2.7，交互场景可用 highspeed 档 |
| `temperature` | 创造性控制 | 创意文案可适当调高 |
| `top_p` | 采样控制 | 与温度协同调参 |
| `max_tokens` | 输出长度上限 | 长文任务需明确约束 |
| `stream` | 流式输出 | 实时交互建议开启 |
| `tools` | 工具调用 | Agent 编排建议启用 |

## 调用与兼容性

平台 API（platform.minimaxi.com，OpenAI 兼容）适用于 SaaS 产品、机器人与内容平台快速接入；开源权重（HuggingFace）可用 SGLang/vLLM 自部署。

## 版本与下线注意

M2 系列为当前主线，旧版 abab 系列、MiniMax-Text 系列已逐步下线或迁移；M2.7-highspeed 为高速推理档（更高吞吐、成本不同），生产环境建议按实测选档。M2.7 基准（厂商口径）：SWE-bench Pro 56.2、SWE-bench Multilingual 76.5、Multi-SWE-bench 52.7、Terminal-Bench 2.0 57.0、MM Claw 62.7、BrowseComp 77.8、AIME 2026 94.2、GPQA-Diamond 89.8。

## 选型建议

若目标是快速上线 Agent/代码类 AI 功能并兼顾成本，MiniMax M2 系列是高性价比候选：100 TPS 版 $0.3/$2.4 per 1M（50 TPS 输出半价）；官方称 1 万美金可让 4 个 Agent 工作一年。开源权重适合自部署长上下文 Agent 场景。