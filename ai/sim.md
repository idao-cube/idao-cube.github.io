## 产品与定位

Sim（Sim Studio AI，旧金山）是开源 AI Agent 工作区，自称"AI 劳动力的中央智能层"。它的核心理念是同一套工作流支持三种构建海拔：拖拽可视化画布、自然语言描述（Mothership 控制平面）、纯代码，随时在三种方式间切换。内置 1000+ 集成、全主流 LLM、表格数据库、文件存储、知识库（RAG）与定时任务，把原来需要三四个服务拼装的能力打包进一个工作区。Apache 2.0 开源，GitHub 2.93 万 stars，2026-07-10 登上 PH 日榜 #2。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Mothership | 自然语言控制平面，描述需求即自动搭建工作流/表格/知识库 |
| 可视化构建 | ReactFlow 画布连接触发器、模型、工具与控制流 |
| 代码逃生舱 | Function 块自定义 JavaScript，完整 API/SDK 程序化调用 |
| 1000+ 集成 | Slack、Notion、HubSpot、Salesforce、数据库等 |
| 内置存储 | Tables（数据库）、Files、Knowledge（RAG 记忆）、scheduled tasks |
| 可观测 | 每次运行逐块 trace、完整日志与真实成本 |
| 部署形态 | 导出为 API 端点、Slack bot、定时任务 |
| 自托管 | Bun + Docker Compose / Kubernetes (Helm)，支持 Ollama/vLLM 本地模型 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `bun run setup` | 交互式向导：建库、生成密钥、配置环境 |
| `docker compose up` | Docker Compose 一键自托管 |
| `helm install sim` | Kubernetes 集群部署 |
| API/SDK | 程序化构建与触发 agent |

## 调用与兼容性

```bash
# 自托管要求：Bun + Docker + PostgreSQL
git clone https://github.com/simstudioai/sim
cd sim
bun run setup   # 交互式配置后启动

# 技术栈：Next.js + Bun + PostgreSQL + Drizzle + Better Auth
```

## 版本与更新注意

活跃迭代中（v0.7.35，2026-07-14），版本更新频繁。开源核心 Apache 2.0 免费自托管；托管云 sim.ai 与部分企业功能（SSO、访问控制）走商业许可，生产使用前确认官网当前价格与限额。本地模型可通过 Ollama 实现完全离线。

## 选型建议

想要"本周就交付一个能跑的 agent"的创始人与小团队首选 Sim——对话式构建降低了技术门槛，代码逃生舱又保住了天花板，自托管把数据留在自己的 Postgres 里。对比：n8n/Flowise/Langflow 偏纯可视化，LangGraph/CrewAI 偏代码优先；Sim 恰好介于两者。若你的价值在于精细调优推理循环，代码优先框架仍更透明可控。