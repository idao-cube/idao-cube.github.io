## 模型与定位

DBRX 是 Databricks 研发的开源 MoE（混合专家）模型，总参数 132B，每个 token 激活 36B 参数。在代码生成、数学推理和逻辑推理上超越同规模密集模型，甚至媲美 70B 密集模型，Apache 2.0 协议完全开放商用。

## 参数速览

| 模型 | 总参数 | 活跃参数 | 上下文 | 开源权重 | 商业可用 |
| --- | --- | --- | --- | --- | --- |
| DBRX Base | 132B | 36B | 32K | ✅ | ✅ |
| DBRX Instruct | 132B | 36B | 32K | ✅ | ✅ |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| MoE 架构 | 16 个专家，每次激活 4 个，效率更高 |
| 代码生成 | 在 HumanEval、MBPP 等基准上表现优异 |
| 长上下文 | 32K 上下文窗口，适合长文档处理 |
| 推理能力 | 数学与逻辑推理能力突出 |
| 高效推理 | 36B 活跃参数，推理成本低于密集 70B 模型 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Databricks](https://www.databricks.com/) | 企业级训练与部署 |
| [HuggingFace](https://huggingface.co/databricks) | 模型权重下载 |
| [Together AI](https://www.together.ai/) | 云端推理服务 |

## 选型建议

需要 MoE 架构优势选 DBRX；代码生成场景表现优异；企业用户可结合 Databricks 平台使用。