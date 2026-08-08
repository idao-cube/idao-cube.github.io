## 工具与定位

OpenCode Skills 是 OpenCode 的扩展能力系统，通过 `.skill.md` 文件注入领域专属的工作流、最佳实践和代码模板。官方提供 50+ Skills，社区持续贡献，支持一键安装与自定义开发。

## 官方精选 Skills

| Skill | 说明 | 适用场景 |
| --- | --- | --- |
| brainstorming | 创意工作前的需求探索与方案设计 | 新功能开发、架构设计 |
| systematic-debugging | 系统化调试工作流 | Bug 修复、性能优化 |
| test-driven-development | TDD 全流程 | 功能开发、重构 |
| frontend-design | 生产级前端界面生成 | Web 开发、UI 实现 |
| ui-ux-pro-max | 50+ 风格、161 色板、UX 指南 | 产品设计、视觉优化 |
| seo | 搜索引擎优化自动化 | 内容发布、站点优化 |
| vue / nuxt / react | 框架专属最佳实践 | 前端开发 |
| bun-development | Bun 运行时优化 | 后端开发、脚本 |

## Skill 结构

| 组件 | 说明 |
| --- | --- |
| 触发条件 | 何时激活该 Skill |
| 工作流 | 分步骤的执行指令 |
| 资源引用 | 脚本、模板、配置文件的相对路径 |
| 检查清单 | 完成后的验证项 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [OpenCode Skills](https://opencode.ai/docs/skills) | 官方文档与安装 |
| [Skill 开发指南](https://opencode.ai/docs/skills/developing) | 自定义 Skill 开发 |
| [Community Skills](https://github.com/opencode-ai/skills) | 社区贡献仓库 |

## 选型建议

日常开发装 brainstorming + debugging + TDD；前端项目加 frontend-design + ui-ux-pro-max；框架专属选对应 Skill；团队统一规范维护私有 Skill 库。