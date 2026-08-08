## 产品与定位

LangWatch 是开源 LLMOps 平台，解决"agent 上线前如何系统化测试、上线后如何观测"的问题。核心创新是仿真测试（Scenarios）：用 Agent + User Simulator + Judge 三个角色跑完整多轮对话，产出"能不能完成某件事"的二元业务结果，而不是模糊的评分——比只看最终输出的 evals 更接近生产真相。OpenTelemetry 原生，支持 LangChain、LangGraph、CrewAI、Pydantic AI、Vercel AI SDK、Google ADK、Mastra 等框架，还能追踪 Claude Code/Codex/opencode 的 token 与成本。Apache 2.0（核心）/SDK MIT，GitHub 3.4K stars，获 €1M 融资，2026-05 全平台开源，2026-07-30 PH 日榜 #4。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 仿真测试 | Agent + 用户模拟器 + 裁判的端到端场景，二元业务结论 |
| 测试金字塔 | 单元评估 → 集成评估 → 端到端仿真三层 |
| 安全仿真 | prompt 注入、PII 外泄、工具滥用等对抗场景 + PDF 风险报告 |
| 可观测 | OTel/OTLP 原生，全框架兼容，token/成本/缓存逐 span 统计 |
| 线上评估 | 生产流量实时评估，任何信号可捕获 |
| Optimization Studio | Trace → 数据集 → 评估 → 优化 → 复测闭环，prompt 与 Git commit 关联 |
| Scenario MCP | 编辑器内自动生成 agent 测试 |
| Langy | AI 把 PM 目标转成测试计划，失败自动转成 PR |
| 企业安全 | RBAC、SCIM/SSO、审计日志→SIEM、ISO 27001、EU 数据驻留 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `docker compose up` | 自托管（Docker Compose） |
| `helm install langwatch` | Kubernetes 集群部署 |
| Scenario MCP | 在 Claude Desktop 内直接测试与评估 |
| Python/TypeScript SDK | 发送 traces 与运行评估 |

## 调用与兼容性

```python
import pytest
from langwatch import scenario

@pytest.mark.agent_test
async def test_recipe_agent():
    result = await scenario.run(
        name="dinner recipe request",
        description="用户在周六晚上又饿又累且没钱点外卖，想要一个食谱",
        agents=[RecipeAgent()],              # 被测 agent，任意框架
        user_simulator=scenario.UserSimulatorAgent(),
        judge=scenario.JudgeAgent(criteria=[
            "Agent should ask at most one follow-up, then give a recipe",
        ]),
    )
    assert result.success
```

## 版本与更新注意

活跃迭代，2026-05 起整个平台开源（此前部分模块闭源）。核心 Apache 2.0 免费自托管；企业模块（SCIM、审计日志、计费等，位于 langwatch/ee/）生产环境需商业许可；云端托管价格以官网为准。OpenClaw 生态用户可通过 OTel 接入观测。

## 选型建议

当 agent 从 demo 走向生产、靠手工测试已无法覆盖多轮失败路径时，LangWatch 是当前最完整的开源选择——仿真测试把"概率指标"变成团队能向非技术方汇报的"行/不行"。LangChain/LangGraph/CrewAI 用户零重写接入。对可观测性要求极高且已有 OTel 栈的团队，可对比 Langfuse、LangSmith（闭源）后按数据驻留要求选型。