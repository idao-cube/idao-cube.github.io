## 工具与定位

Karpathy Guidelines 源自 Andrej Karpathy 2025-01 关于 LLM coding agent 失败模式的著名帖子，由社区（multica-ai）整理实现为可装载的 Agent 规则（约 174K stars）。核心思想：LLM 编码 Agent 最常见的失败源于"想得太少、改得太多"——因此用四条极简规则约束 Agent 行为。它门槛极低：既可以作为单文件放进 CLAUDE.md/AGENTS.md，也可以作为正式 Skill 安装。注意：这是社区实现，并非 Karpathy 本人维护。

## 核心能力

| 规则 | 说明 |
| --- | --- |
| Think before coding | 动手前先思考方案，不盲目开写 |
| Keep it simple | 保持简单，避免过度设计 |
| Surgical changes | 外科手术式的最小改动，不顺手重构 |
| Stay focused on goal | 始终聚焦目标，不偏离需求 |

## 安装与使用

| 方式 | 说明 |
| --- | --- |
| 放入 CLAUDE.md / AGENTS.md | 作为常驻行为约束（最简单） |
| 作为 Skill 安装 | 通过 skills CLI 或手动放入 skills 目录 |
| 自定义扩展 | 在四条规则基础上补充团队特定约束 |

## 平台兼容

| 平台 | 说明 |
| --- | --- |
| Claude Code | CLAUDE.md 原生支持 |
| Cursor / Codex / Gemini CLI | AGENTS.md 或兼容规则文件 |
| 任意 Agent | 文本规则即可注入 |

## 版本与更新注意

实现版本较多（multica-ai 为主流之一），选择时注意来源；规则极简，适合作为基线再叠加其他技能（如 TDD、代码审查）。不解决"如何做对"的流程问题，只约束"别做错"的行为边界。

## 选型建议

零成本起步、想给 Agent 立行为底线（尤其防过度改动/偏离目标）的团队首选 Karpathy Guidelines；可作为任何 Agent 工作流的基础层，再按需叠加 Superpowers（全流程）、Matt Pocock（需求澄清）等技能。