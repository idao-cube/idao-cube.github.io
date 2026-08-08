## AI Agent 是什么

AI Agent（智能代理）是指能够自主感知环境、制定计划并执行任务的 AI 系统。与普通的 LLM 调用不同，Agent 可以：

- 拆解复杂任务为子步骤
- 调用外部工具（搜索、计算、API）
- 记忆上下文和中间结果
- 自我修正和迭代

## 主流框架对比

### LangChain

最成熟的 Agent 框架，生态丰富。

```python
from langchain.agents import create_react_agent
from langchain.tools import tool

@tool
def search(query: str) -> str:
    """搜索互联网"""
    return search_api(query)

agent = create_react_agent(llm, tools=[search])
```

### CrewAI

专注于多 Agent 协作。

```python
from crewai import Agent, Task, Crew

researcher = Agent(role="研究员", goal="收集信息")
writer = Agent(role="写手", goal="撰写报告")

task = Task(description="研究AI趋势", agent=researcher)
crew = Crew(agents=[researcher, writer], tasks=[task])
```

### AutoGPT

最早出圈的自主 Agent，适合长期运行的自动化任务。

## 实战：搭建一个技术简报 Agent

这个 Agent 会每天早上自动抓取 Hacker News 热门，整理成简报发送到邮箱。

## 总结

AI Agent 是 2026 年最值得关注的技术方向之一。选择合适的框架，结合具体业务场景，可以大幅提升自动化水平。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)