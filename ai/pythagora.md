## 产品与定位

Pythagora（YC W24，2024-05 获 $4M seed）是 GPT Pilot 的官方商业化平台。GPT Pilot 是其开源项目（GitHub 33.7K+ stars），核心思路与"让 AI 从零写代码"的生成器不同——它让 **14 个专业 agents 扮演一个完整开发团队**：Product Owner、Spec Writer、Architect、Tech Lead、Developer、Code Monkey、Reviewer、Troubleshooter、Debugger、Technical Writer、DevOps 等，从需求理解、架构规划、编写代码、调试到部署，全流程自主推进。

Pythagora 以 VS Code / Cursor 扩展形式提供这套多 agent 流程，生成全栈应用（React 前端 + Node.js 后端 + 数据库），可一键部署到 AWS 或完全本地运行。厂商口径称已有 80K+ 用户、5K+ 企业采用，适合快速把自然语言需求变成可运行的 MVP。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 14 个专业 agents | 模拟 PM/架构师/开发者/审查者/调试者等完整团队角色 |
| 全流程自动化 | 需求→规划→编码→调试→部署，Agentic 式端到端推进 |
| 全栈生成 | React 前端 + Node.js 后端 + 数据库（MongoDB/Postgres/MySQL/Google Sheets） |
| 一键部署 | 部署到 AWS，或完全在本地运行 |
| 持续迭代 | 运行后继续对话修改功能，agent 团队自动跟进 |

## 计费方式

| 方案 | 价格 | 说明 |
| --- | --- | --- |
| Starter | 免费 | 600K tokens/月，仅前端，带水印 |
| Startup | $20/月 | 4M tokens，全栈、无水印 |
| Premium | $45–200+/月 | 10M–100M tokens，按用量分层 |
| Business | 定制 | SSO/RBAC/审计等企业功能 |

注意：Pythagora 定价变动较频繁（2026-02 曾有 Pro $180/40M tokens 的档位），以官网实时价格为准。

## 调用与兼容性

```bash
# VS Code / Cursor 安装 Pythagora 扩展
# 在扩展中创建新项目，用自然语言描述需求即可启动 agent 团队
```

技术栈限定 React + Node.js；Python 支持长期标注 "coming soon"，实际一直未落地。

## 版本与更新注意

- **安全警告（重要）**：其开源项目 GPT Pilot 的仓库于 2026-06-11 被发现隐藏供应链蠕虫——一个凭据窃取器，藏于 `core/telemetry/` 目录，从 2025-08 起混入代码。凡在 2025-08 至 2026-06-11 期间克隆并运行过该仓库的用户，应立即轮换可能泄露的凭据。
- GPT Pilot 仓库 README 已标注 "not being maintained anymore"，开发重心已完全转向 Pythagora 商业平台，请勿再按旧教程使用 GPT Pilot。
- 仅支持 React/Node.js 全栈；企业实际落地案例较少，多为中小团队或原型验证。

## 选型建议

- 想体验"AI 团队"分工协作、而非单个 agent 对话的开发者。
- 快速从自然语言需求生成可运行全栈 MVP 的个人与初创团队。
- 有安全合规要求的组织：先自查是否用过受污染的 GPT Pilot 版本，再评估商业平台。
- 需要 Python/多语言栈、或对提示词有精细控制需求的团队：Pythagora 技术栈与黑盒流程可能不适用。