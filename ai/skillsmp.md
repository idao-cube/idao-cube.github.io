## 工具与定位

SkillsMP（skillsmp.com）是独立社区维护的 Agent Skills 市场（与 Anthropic/OpenAI 无隶属），索引 GitHub 上公开的 SKILL.md 文件，帮助用户了解"人们在教 Claude、Codex 和其他 AI Agent 做什么"。平台已收集 **2,396,488 个 SKILL.md 文件**，提供按任务、创作者、职业分类检索，并可回源 GitHub 检查原始仓库后再决定是否安装。支持多语言（含中文 /zh）。适合技能发现、生态研究和 Agent 工作流集成。

## 核心能力

| 能力 | 说明 |
| --- | --- |
| 技能检索 | 搜索 239 万+ 公开 skills（如 'AI video'、'data analysis'、'code review'） |
| 创作者浏览 | 按 creator/repo 追踪团队维护的技能集与活跃度 |
| 职业地图 | 23 大职业组、867 个 SOC 职业（数据源美国劳工部 SOC 标准） |
| 分类浏览 | Tools 548K / Business 435K / Development 313K / Testing & Security 251K / Data & AI 201K / DevOps 183K / Documentation 161K / Content & Media 140K |
| REST API | 连接 239 万+ skills 到自有搜索、分析或 Agent 工作流 |
| 100% 分类 | 所有技能均带职业标签 |

## 热门 Skills 示例

| Skill | 来源仓库 |
| --- | --- |
| frontend-design（164.3k） | anthropics/skills |
| skill-creator（164.3k） | anthropics/skills |
| brainstorming（261.6k） | obra/superpowers |
| grill-me（189.5k） | mattpocock/skills |
| ui-ux-pro-max（110.4k） | nextlevelbuilder/ui-ux-pro-max-skill |
| vercel-react-best-practices（29.5k） | vercel-labs/agent-skills |
| ppt-generation（77.9k） | bytedance/deer-flow |
| browser-use（106.9k） | browser-use/browser-use |

## 使用方式

| 方式 | 说明 |
| --- | --- |
| 网页搜索 | Search / Creators / Occupations 三种入口 |
| 回源安装 | 打开技能 GitHub 源码，按仓库说明复制 skill 文件夹到技能目录 |
| API 接入 | REST API 将技能目录接入自有系统 |

## 版本与更新注意

SkillsMP 定期同步 GitHub 公开源，目录更新可能滞后于上游仓库；平台不认证技能安全性——社区技能应视作开源代码，安装前需审查源码、脚本与权限，并确认仓库活跃度。

## 选型建议

技能发现与研究生态首选 SkillsMP：职业分类和创作者视角是独有优势，适合摸清某个领域"人们在教 Agent 做什么"；需要中文加速与安全评级时配合 CocoLoop Hub、SkillHub 使用。