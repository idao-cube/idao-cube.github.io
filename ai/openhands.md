## 产品与定位

OpenHands（前身 OpenDevin）是 All Hands AI 打造的开源自主软件工程 Agent 平台，MIT 许可。它不是给你提代码建议的助手，而是像一个初级工程师那样真正动手：制定计划、编辑文件、运行 shell 命令、浏览网页、执行测试，最后直接开出 Pull Request 供人审查。团队由卡内基梅隆大学教授 Graham Neubig 联合创立，已获 $18.8M Series A 融资，GitHub 超过 8.3 万 stars，SWE-bench Verified 官方成绩 77.6（V0 口径），是开源阵营中对标 Devin 的最强选手。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 自主执行 | 编辑文件、运行命令、浏览网页、执行测试、开 PR 全流程 |
| 三界面 | Web GUI（Agent Canvas）、终端 CLI、Python SDK 共用同一引擎 |
| Agent Canvas | 自托管开发者控制台，可运行 OpenHands/Claude Code/Codex/Gemini 等任意 ACP agent |
| 模型无关 | LiteLLM 统一接入：Claude/GPT/Gemini/Mistral/DeepSeek/Qwen/Minimax + 本地 Ollama/vLLM/LM Studio |
| 真实沙箱 | 每任务独立 Docker 容器，隔离文件系统与进程 |
| 自动化 | Slack @openhands、GitHub issue 触发、Jira/Linear 集成、定时任务 |
| 工程能力 | 上下文自动压缩、critic 模型质量评估、Skills 知识片段、MCP 工具 |
| 企业能力 | K8s 部署、Planning Mode、Agent Control Plane（权限/花费/审计） |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `docker run ... openhands` | 本地 GUI 启动（Docker 方式） |
| `openhands` (CLI) | 终端原生 agent 会话 |
| `openhands-sdk` | Python SDK 自定义 agent 工作流 |
| @openhands 评论 GitHub issue | 云版自动接单修 issue 并开 PR |

## 调用与兼容性

```bash
# Docker 方式运行本地 GUI（需 Docker）
docker run -it --rm -p 3000:3000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  ghcr.io/openhhands/openhhands

# 或使用 Cloud 版（GitHub/GitLab 登录）
# https://app.openhands.dev
```

## 版本与更新注意

v1.6.0（2026-03-30）加入 Kubernetes 一键部署与 Planning Mode（多步任务先出计划再动手）；v1.7.0（2026-05-01）支持 KVM 加速透传；2026-05-06 发布企业级 Agent Control Plane。自托管要求 Docker，CI/CD 场景需处理 socket 挂载与 Git 凭据注入，首次配置需预留一天调试时间。

## 选型建议

需要可审计、可自托管、模型自由的自主编码 Agent 的团队首选 OpenHands（MIT 核心在同类性能梯队中罕见）；监管行业（医疗/金融/政务）不能把代码交给闭源 SaaS 的场景尤其合适。Cloud Pro $20/月即可 BYOK 无限使用，成本比 Devin 的 ACU 超支更可预测。想要开箱即用的托管体验可选 Devin；想深入 IDE 协作可选 Cursor 的 agent 模式。