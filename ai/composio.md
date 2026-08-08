## 产品与定位

Composio 是 AI Agent 的"工具层"基础设施：给 agent 接上 1000+ 预认证的工具（GitHub、Slack、Gmail、Notion、Salesforce 等），并替开发者解决最头疼的认证与执行问题。核心卖点是 Managed Auth——OAuth 全流程托管（授权、token 刷新、生命周期管理），agent 按需在对话中发起连接；配合远程沙箱环境与并行执行，让 Claude Code、Cursor 等任何 MCP 客户端从"能问问题"升级为"能干活"。MIT 开源，GitHub 2.95 万 stars，SOC2 与 ISO 27001 认证。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 1000+ 集成 | GitHub/Slack/Gmail/Notion/Salesforce 等预认证工具包 |
| Managed Auth | OAuth 端到端托管，用户意图触发内联授权，无需预配置 |
| Meta Tools | 运行时发现/认证/执行工具，不把数百个工具定义塞进上下文 |
| 沙箱工作台 | 远程沙箱中工具即代码运行，结果落盘为可导航文件系统 |
| MCP 端点 | 每个 session 暴露托管 MCP URL，Claude/Cursor/ChatGPT 直接接入 |
| 框架适配 | OpenAI Agents、Claude Agent SDK、Vercel AI SDK、LangChain、LangGraph、CrewAI、AutoGen、Gemini 等 |
| MCP Gateway | 企业每团队一个 MCP 端点，SSO（SAML/OIDC/SCIM）、工具白黑名单、审计日志 |
| 模型无关 | 换模型不换工具与认证，零重构 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `curl -fsSL https://composio.dev/install \| bash` | 安装 CLI |
| `composio search` | 搜索可用工具 |
| `composio execute` | 直接执行工具 |
| `composio link` | 连接账号 |
| `composio run` | 脚本化工作流 |

## 调用与兼容性

```python
from composio import Composio

composio = Composio(api_key="COMPOSIO_API_KEY")
session = composio.create(user_id="user_123")  # 创建会话

# 方式一：原生工具
tools = session.tools()  # 交给你的 agent 框架

# 方式二：MCP 接入（Claude/Cursor 等）
mcp_url = session.mcp.url  # 指向 session.mcp.url 即可
```

## 版本与更新注意

活跃维护中（monorepo，Python/TypeScript 双 SDK）。定价为使用量制：Free 20K calls/月、$29/月 200K calls、$229/月 2M calls、Enterprise 定制；官方预告 2026-08-15 调整价格，选型前关注新定价。工具调用是硬成本，高频使用需预留预算。

## 选型建议

给已有 agent（Claude Code、自建 agent、CrewAI/LangGraph 项目）补真实工具执行能力时首选 Composio——免去逐个写 OAuth 的体力活。企业大规模推广 agent 可用 MCP Gateway 做统一治理。若只需单一平台内的低代码集成，可对比 n8n/Dify 的内置连接器；需要深度自定义认证再考虑自建。