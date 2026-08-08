## 工具与定位

Superpowers 是 Jesse Vincent（obra）发起的开源 Agent 技能框架，2026-06 已达 226,000+ stars，超越 Vue 仓库成为最热门的 Agent 技能集合。它把一套完整的软件开发生命周期（SDLC）方法论拆解为**可组合技能（composable skills）**，Agent 在接到任务时自动检查并加载相关技能，把"正确做事"的方法论固化进编码流程，而非依赖一次性提示词。

## 核心能力

| 技能 | 作用 |
| --- | --- |
| brainstorming | 创作前探索用户意图、需求与设计 |
| using-git-worktrees | 隔离工作区的开发分支管理 |
| writing-plans | 将需求转化为可执行实现计划 |
| subagent-driven-development / executing-plans | 子代理并行执行实现计划 |
| test-driven-development | 先测试后实现的开发纪律 |
| requesting-code-review | 合入前请求代码审查 |
| finishing-a-development-branch | 合并/PR/清理的收尾决策 |

## 安装与使用

| 平台 | 方式 |
| --- | --- |
| Claude Code | `/plugin install superpowers@claude-plugins-official`（官方插件市场） |
| Codex App / CLI | OpenAI plugins 安装 |
| Cursor | `/add-plugin` |
| Gemini CLI | `gemini extensions install` |
| OpenCode / Copilot CLI / Kimi Code / Pi / Antigravity / Factory Droid | npx skills add 或原生支持 |

通用安装：`npx skills add obra/superpowers`。安装后 Agent 会在任务开始前自动评估并加载相关技能。

## 平台兼容

| 平台 | 说明 |
| --- | --- |
| Claude Code / Codex / Cursor / Gemini CLI | 官方支持，一命令安装 |
| Copilot CLI / Kimi Code / OpenCode | 兼容 npx skills 分发 |
| Pi / Antigravity / Factory Droid | 新兴平台逐步纳入 |

## 版本与更新注意

技能库持续演进，各平台安装方式略有差异；建议定期 `npx skills update` 获取新技能与修复。SDLC 流程为强方法论取向——若团队已有一套既定流程，可只选取其中部分技能组合使用。

## 选型建议

希望把完整工程方法论（规划→计划→TDD→审查→收尾）固化进 Agent 工作流的团队首选 Superpowers；其可组合设计允许按需裁剪。与 Matt Pocock 的小而可组合技能相比，Superpowers 更偏全流程框架，适合团队级统一实践。