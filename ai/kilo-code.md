## 产品与定位

Kilo Code 是目前最流行的开源 AI 编码 agent 之一，前身 fork 自 Cline 与 Roo Code，目标是成为"all-in-one agentic engineering platform"。它在 VS Code、JetBrains、CLI、Slack 和云端 Cloud Agents 中提供同一套 agent 体验，内置 500+ 模型选择（GPT-5.5、Claude Opus 4.7、Claude Sonnet 4.6、Gemini 3.1 Pro 等），并承诺零 AI 推理加价——按模型提供商的公开价格计费。

2026 年 4 月的重构让扩展基于可移植的开源核心，在 VS Code、CLI 与 Cloud Agents 之间共享，带来并行工具调用、Agent Manager（并排运行多个 agent）、行级 inline 代码审查与 500+ 模型访问。VS Code Marketplace 安装量已超 137 万，是 Cline 家族中最激进的派生版本。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 五种内置 agents | Code（写码）、Plan（架构设计）、Ask（问答不动文件）、Debug（排障）、Review（审查改动） |
| 自动委派子代理 | 原 Orchestrator Mode 已弃用，Code/Plan/Debug agent 可自动协调子代理分工 |
| 500+ 模型 | 任意主流/开源权重/本地模型，任务中途可切换；Auto Model 按任务类型与预算智能路由 |
| Memory Bank | 将架构决策与项目约定写入仓库内结构化 Markdown，跨会话保留上下文 |
| Cloud Agents | 云端运行长任务，不占用本地资源；可跨设备接续会话（手机→IDE→CLI） |
| Code Review | /review 本地审查或云端 agent 审查 PR，支持 REVIEWS.md 适配项目规范 |
| MCP Marketplace | 浏览并接入 MCP 服务器扩展 agent 能力 |
| KiloClaw | 一键部署个人 AI agent（OpenClaw），支持 Telegram/Discord/Slack 触发 |
| 其他 | 内联自动补全、语音命令、浏览器控制、安全漏洞分析、PDF 上传、团队分析仪表盘 |

## 计费方式

| 方案 | 价格 | 说明 |
| --- | --- | --- |
| 免费扩展 | $0 | 自带 API key（BYOK）或本地模型，Kilo 不额外收费 |
| Kilo Pass Starter | $19/月 | 信用订阅，50% 奖励积分，积分永不过期 |
| Kilo Pass Pro | $49/月 | 更高配额，日常开发主力 |
| Kilo Pass Expert | $199/月 | 大用量与团队 |
| Teams | $15/用户/月 | 共享工作区、管理员控制、统一计费 |

新用户注册即送 $20 免费额度；信用卡充值有 5% 手续费，AI 推理本身零加价。

## 调用与兼容性

```bash
# CLI 安装
npm install -g @kilocode/cli

# VS Code：从 Marketplace 安装 kilocode.Kilo-Code 扩展
# JetBrains：安装原生插件（IntelliJ/WebStorm/PyCharm 等）
```

也可通过 OpenRouter、AWS Bedrock、Azure OpenAI、Google AI Studio 等网关，或本地 Ollama / LM Studio 运行。

## 版本与更新注意

- 2026-04 扩展完全重构：快照（原 checkpoints）改基于 Git、per-tool 权限系统取代 auto-confirm 命令、Modes 更名 Agents、移除 profile 层改为收藏模型。
- 2026-06 域名从 kilocode.ai 迁移至 kilo.ai；新增 Auto Model 路由、Qwen3/GLM/MiniMax 模型与 KiloClaw 多渠道连接。
- 开源协议：扩展 Apache-2.0，CLI MIT。全提示词与上下文窗口可见，无静默压缩、无静默换模型。
- 功能面较广（语音、云端、部署、审查），部分高级功能仍在快速迭代中。

## 选型建议

- 追求模型自由度与零加价的开发者：500+ 模型 + BYOK，成本完全透明。
- 需要跨 IDE/终端/移动端接续工作的团队：会话与上下文全平台同步。
- 想审查每一条 prompt 与上下文、自托管或 fork 的团队：MIT/Apache-2.0 全源码开放。
- 相对 Cline（仅 VS Code）与 Roo Code：Kilo 覆盖面更广但功能更年轻；相对 Cursor：保留现有编辑器而非更换 IDE。