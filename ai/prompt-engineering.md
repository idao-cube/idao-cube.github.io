## 工具与定位

提示词工程（Prompt Engineering）是释放大模型能力的关键技能。本指南系统性梳理主流提示技术，从基础指令到高级推理链，配套实战案例与评测框架，适合开发者、产品经理和 AI 研究者。

## 核心技术

| 技术 | 说明 | 适用场景 |
| --- | --- | --- |
| Zero-shot | 零样本直接提问 | 简单问答、快速测试 |
| Few-shot | 提供少量示例引导 | 格式控制、风格迁移 |
| Chain-of-Thought | 逐步推理过程 | 数学、逻辑推理 |
| Tree of Thoughts | 树状多路径探索 | 复杂规划、创意生成 |
| ReAct | 推理 + 行动循环 | Agent 工具调用 |
| Self-Consistency | 多路径投票提升准确性 | 高可靠场景 |

## 实战框架

| 框架 | 说明 |
| --- | --- |
| CRISPE | 角色、背景、任务、输出、约束、示例 |
| ROSES | 角色、客观、场景、预期、步骤 |
| BROKE | 背景、角色、目标、关键结果、示例 |

## 推荐资源

| 平台 | 说明 |
| --- | --- |
| [Prompting Guide](https://www.promptingguide.ai/) | 最全面的提示词技术文档 |
| [OpenAI Cookbook](https://github.com/openai/openai-cookbook) | 官方实战案例 |
| [LangChain Hub](https://smith.langchain.com/hub) | 社区提示词模板库 |

## 选型建议

入门先掌握 Zero-shot/Few-shot/CoT；复杂任务用 ReAct + ToT；生产环境结合评测框架持续优化提示词质量。