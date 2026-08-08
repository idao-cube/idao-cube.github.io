## 工具与定位

Awesome AI Skill Hub 是社区驱动的 AI Skill 与提示词模板精选库，覆盖编程、写作、数据分析、创意生成等高频场景。2026 年技能生态已从"单一仓库精选"走向"开放标准 + 多平台分发"：以 Anthropic Agent Skills（SKILL.md）为事实标准，配合各平台原生格式（Cursor Rules/Skills、OpenCode .skill.md、Copilot Instruction）形成完整生态。

## 场景覆盖

| 场景 | 示例 Skill | 适用工具 |
| --- | --- | --- |
| 编程开发 | Code Review、TDD、Debug | OpenCode、Cursor、Copilot |
| 技术写作 | API 文档、README、技术博客 | OpenCode、Notion AI |
| 数据分析 | SQL 生成、可视化建议、报告 | OpenCode、Notebook LM |
| 创意生成 | 营销文案、产品命名、故事 | ChatGPT、Claude |
| 研究助手 | 论文摘要、文献对比 | Notebook LM、Elicit |

## 导出格式

| 格式 | 说明 |
| --- | --- |
| Agent Skill | SKILL.md 标准格式（跨平台兼容） |
| OpenCode Skill | `.skill.md` 格式，直接放入 `.opencode/skills/` |
| Cursor Rule/Skill | `.cursor/rules/*.mdc` 或 `.cursor/skills/` |
| Copilot Instruction | `copilot-instructions.md` 格式 |

## 生态资源导航

| 资源 | 说明 |
| --- | --- |
| [Anthropic Agent Skills](https://agentskills.io/) | 开放标准（SKILL.md），生态源头 |
| [anthropics/skills](https://github.com/anthropics/skills) | 官方技能仓库（document-skills 等） |
| [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 67K+ stars 的跨平台 Claude 技能精选库（1000+ 技能） |
| [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | 1000+ 精选 agent skills（官方团队+社区维护） |
| [Skills.sh](https://skills.sh/) | Vercel 维护的 Open Agent Skills 平台 |
| [SkillsLLM](https://skillsllm.com/) | 技能市场 |
| [awesomeskill.ai](https://awesomeskill.ai/) | 技能市场（API 目录） |
| SkillHub / SkillsMP | AI 五维评分 / 智能搜索技能站 |
| [PromptBase](https://promptbase.com/) | 提示词交易市场 |
| [FlowGPT](https://flowgpt.com/) | 社区提示词分享平台 |

## 选型建议

日常开发选编程类 Skill 直接导入；写作场景用 Markdown 格式自定义；跨平台复用优先 SKILL.md 标准格式；团队统一规范建议维护私有 Skill 库，并通过 plugin marketplace 或 repo 内 skills 目录分发。