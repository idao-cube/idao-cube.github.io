## 模型与定位

百川智能（Baichuan）由搜狗前 CEO 王小川创立，公司战略已转向医疗方向。通用基座为 **Baichuan 4**（2024-05-22 发布）：较 Baichuan 3 通用能力 +10%、数学 +14%、代码 +9%，SuperCLUE 国内第一，多模态能力领先 Gemini Pro/Claude 3 Sonnet，采用 RLxF 强化学习对齐。**Baichuan-M4**（2026-05-26 发布）为新一代医疗专用大模型：HealthBench、HealthBench Hard、HealthBench Professional 三大医疗榜单世界第一，全面超越 GPT-5.5、Claude Opus 4.7、DeepSeek-V4-Pro；事实性幻觉率 3.3% 全球新低；内置 1000+ 条原子化临床路径，支撑"百小应"AI 家庭医生。适合企业知识问答、医疗健康、客服辅助、文档处理等业务场景。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像（多模态能力） |
| 输出能力 | 文本与结构化响应、工具调用 |
| 推理模式 | 通用推理（Baichuan 4）与医疗推理（M4）双线 |
| 典型模型名 | Baichuan4、Baichuan3-Turbo、Baichuan3-Turbo-128K、Baichuan-M4（医疗） |
| 上下文窗口 | Baichuan3-Turbo-128K 支持 128K |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 医疗场景选 Baichuan-M4，通用选 Baichuan4 |
| `temperature` | 控制发散度 | 客服和摘要建议低温 |
| `top_p` | 控制采样范围 | 需要稳定答案时调低 |
| `max_tokens` | 输出长度限制 | 避免冗长响应 |
| `response_format` | 响应格式 | 对接系统建议输出 JSON |
| `stream` | 流式返回 | 交互式场景建议开启 |

## 调用与兼容性

支持百川 MaaS 平台标准 API（platform.baichuan-ai.com）接入，含 Assistants API；新用户赠送 1000 万免费 token。适合与企业现有业务系统对接。

## 版本与下线注意

Baichuan 4 为当前通用基座，Baichuan 3 系列（Turbo/128K）仍提供服务但属旧版；Baichuan-M4 为医疗专用新线，与通用模型不可混用。建议关注平台公告中的版本切换和能力变更。

## 选型建议

医疗健康、医药问答场景首选 Baichuan-M4（幻觉率业界最低）；通用业务可用 Baichuan4；中文业务适配和企业化落地是百川的强项，新用户可先用免费额度评测。