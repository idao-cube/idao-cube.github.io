## 产品与定位

适合构建生产级 AI 应用和智能体，覆盖 200+ LLM 集成、向量数据库连接与 LangSmith 可观测性。LangGraph 作为状态管理层支持复杂的多步推理和循环工作流。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| LLM 集成 | 支持 OpenAI、Anthropic、Google、Cohere 等 200+ 模型 |
| 工具调用 | 原生 Function Calling 和自定义工具接口 |
| 记忆管理 | 对话缓冲、向量存储、实体记忆 |
| RAG 流水线 | 文档加载、分块、嵌入、检索全链路 |
| LangGraph | 状态机驱动的 Agent 编排（推荐方式） |
| LangSmith | 生产环境追踪、评估与调试 |

## 常用参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 生产环境固定版本避免漂移 |
| `temperature` | 采样温度 | 结构化任务建议 0.0-0.3 |
| `max_tokens` | 输出限制 | 避免超长响应 |
| `tools` | 工具列表 | Agent 场景显式声明可用工具 |
| `memory` | 记忆类型 | 长会话用 Vector Store，短会话用 Buffer |

## 调用与兼容性

```python
from langchain_openai import ChatOpenAI
from langchain.agents import create_react_agent, load_tools
from langchain import hub

llm = ChatOpenAI(model="gpt-4o", temperature=0)
tools = load_tools(["serpapi", "python_repl"])
agent = create_react_agent(llm, tools)
```

## 版本与更新注意

v0.3+ 版本以 LangGraph 为核心构建 Agent，建议迁移旧版 AgentExecutor 代码。模型和工具集成持续更新中。

## 选型建议

需要构建复杂 Agent 系统、多步推理工作流、RAG 检索应用时首选 LangChain；配合 LangSmith 可实现生产级监控和持续优化。