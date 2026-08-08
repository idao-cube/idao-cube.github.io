## 工具与定位

Agent Skills 是 Anthropic 于 2025-10-16 发布的编码 Agent 能力扩展机制，2025-12-18 正式开放为标准（agentskills.io），成为技能生态的事实源头。它把"一个能力"组织为一个文件夹：`SKILL.md`（YAML frontmatter 含 name/description + Markdown 指令）+ 可选 `scripts/`、`resources/`、`references/`。核心原理是**渐进披露（progressive disclosure）**——Agent 先读取轻量元数据判断相关性，需要时才加载全文，避免污染上下文。已被 Claude Code、OpenAI Codex CLI、Google Gemini CLI、Cursor、GitHub Copilot、Windsurf、Cline、Kimi Code 等 20+ 平台采用，skill 还可与 MCP server 组合使用。

## 核心能力

| 能力 | 说明 |
| --- | --- |
| SKILL.md 标准 | 文件夹式技能包，frontmatter + 指令 + scripts/resources/references |
| 渐进披露 | 先元数据后全文，按需加载控制上下文成本 |
| skill-creator | 2026-03-03 增强：自动写 evals、benchmark 模式、多 Agent 并行评估、A/B comparator、description 触发优化 |
| 三类 Skills | foundational（文档/表格/PPT）、partner（K-Dense/Browserbase/Notion）、enterprise（内部流程） |
| 遥测 | PreToolUse hook 可测量 skill 实际使用情况 |
| 与 MCP 组合 | skill 声明可搭配 MCP server 工具完成复杂任务 |

## 安装与使用

| 场景 | 方式 |
| --- | --- |
| Claude Code 项目 | `.claude/skills/` 目录（项目级）或 `~/.claude/skills/`（个人级） |
| 官方插件市场 | `/plugin marketplace add anthropics/skills` 后 `/plugin install document-skills@anthropic-agent-skills` |
| Claude API | `/v1/skills` endpoint + Code Execution Tool（beta） |
| claude.ai / Cowork | 会话中直接使用已启用 skills |
| AWS / Microsoft Foundry | Skills API 云端托管 |

## 平台兼容

| 平台 | 支持方式 |
| --- | --- |
| Claude Code / Codex CLI / Gemini CLI | 原生支持 SKILL.md |
| Cursor / GitHub Copilot / Windsurf / Cline | 兼容加载 .claude/skills 等目录 |
| Claude API / AWS / Foundry | Skills API 服务端调用 |

## 版本与更新注意

2025-10-16 首发 → 2025-12-18 开放标准。skill-creator 于 2026-03-03 大幅增强（evals/benchmark/并行评估）。2026-06-03 Anthropic 公开内部数百个 skills 的经验：按 9 类组织（library/API reference、product verification、data fetching、business process、code scaffolding、code quality、CI/CD 等），分发建议用 repo 内 `.claude/skills` 或 plugin marketplace。官方仓库 anthropics/skills 提供 document-skills 与 example-skills 两个插件。注意各家实现细节（如 Cursor 的 paths glob）略有差异。

## 选型建议

需要跨平台复用"技能包"、或想把自己的方法论打包给 Agent 使用时首选 Agent Skills 标准；配合 skill-creator 可把验证纳入技能开发流程；若只使用单平台，优先采用该平台原生格式（如 Cursor Skills、OpenCode .skill.md），再通过兼容加载复用本生态的技能。