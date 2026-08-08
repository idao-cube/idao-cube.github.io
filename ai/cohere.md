## 模型与定位

Cohere 专注企业级生成式 AI，2026 年产品线以 **Command A 系列**为主：

- **Command A+**（2026-05-20 开源，Apache 2.0）：Cohere 首个 MoE 模型，218B 总参/25B 激活，128K 输入上下文（64K 最大生成），支持文本+图像输入，48 种语言（含全部欧盟官方语言），面向推理、Agent 工作流、RAG 与多模态文档处理；硬件极省——1×B200 或 2×H100（W4A4 量化），提供 BF16/FP8/W4A4 多格式；HF 可下载（CohereLabs/command-a-plus-05-2026）。
- **Command A**（企业旗舰 API）：111B dense，256K 上下文，$2.50/$10 per 1M，工具调用/JSON 模式/RAG 优化。
- **Command R2**：专注结构化数据分析，原生生成并执行 SQL/Python/R。

适合企业知识问答、RAG、客服自动化、文档理解与合规要求高的数据驻留场景。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像（Command A+） |
| 输出能力 | 文本、结构化输出、工具调用、推理 |
| 推理模式 | 可配置 thinking（Command A+ 支持） |
| 典型模型名 | command-a-plus-05-2026、command-a、command-r2 |
| 上下文窗口 | Command A+ 128K 输入/64K 生成；Command A 256K |
| 参数量 | Command A+ 218B 总/25B 激活（MoE）；Command A 111B dense |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 选择模型 | 按任务复杂度分层使用 |
| `thinking` | 推理开关 | 复杂任务开启，简单任务关闭提速 |
| `temperature` | 控制输出发散度 | 业务问答建议低温 |
| `p` / `top_p` | 采样控制 | 稳定输出时适当调低 |
| `max_tokens` | 输出长度限制 | 与产品字数规则对齐 |
| `tools` | 工具调用 | Agent 工作流建议声明 |

## 调用与兼容性

支持标准 API 与企业私有化部署（私有云/on-prem，AWS/Azure），支持 vLLM、Transformers 自部署；W4A4 量化在 2×H100 即可运行 A+，适合数据驻留与合规场景。

## 版本与下线注意

Command R+（08-2024）为旧代旗舰，已让位于 Command A 系列；Command R2 面向数据分析专门场景。模型策略会持续更新，建议定期校验关键业务用例与安全策略一致性。

## 选型建议

企业知识应用和可控输出优先 Cohere；私有化部署（两卡 H100 跑 A+）与 48 语言多语种场景是其差异化优势；结构化数据分析用 Command R2。