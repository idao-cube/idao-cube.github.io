## 工具与定位

Cursor Rules 是 Cursor IDE 的上下文注入系统。2026 年已演进为**双机制**：Rules（规则，常驻约束）与 Skills（技能，按需多步流程）——"rules nudge, skills execute"。旧的 `.cursorrules` 单文件格式已弃用，统一迁移到 `.cursor/rules/*.mdc` 多文件目录格式，每个文件带 YAML frontmatter（description/globs/alwaysApply）。正确配置后，AI 补全和建议会更贴合项目实际，减少无效输出。

## 规则类型（Rules）

| 类型 | 说明 | 示例 |
| --- | --- | --- |
| 项目概览 | 项目定位、技术栈、核心模块 | "这是 Vue 3 + TypeScript 项目，使用 Pinia 状态管理" |
| 编码规范 | 命名、格式、注释约定 | "组件使用 PascalCase，工具函数使用 camelCase" |
| 架构约束 | 目录结构、依赖规则、禁止项 | "禁止在组件层直接调用 API，必须通过 Store" |
| 测试要求 | 测试框架、覆盖率、命名 | "使用 Vitest，测试文件与源码同目录，*.test.ts" |
| 技术栈细节 | 版本、配置、特有模式 | "UnoCSS 替代 Tailwind，自定义 shortcuts 在 uno.config.ts" |

## 四种激活模式

| 模式 | 触发方式 | 适用场景 |
| --- | --- | --- |
| Always | frontmatter `alwaysApply: true` | 全局铁律（慎用，常驻消耗上下文） |
| File-glob | `globs` 匹配路径自动挂载 | 大多数场景推荐（如 *.tsx 挂 React 规范） |
| Agent Requested | `description` 语义触发 | 按需加载、节省上下文 |
| Manual | `@ruleName` 手动引用 | 临时指定规则 |

## Skills 技能机制

| 能力 | 说明 |
| --- | --- |
| 存放位置 | `.cursor/skills/NAME/SKILL.md`（多步流程指令） |
| 调用方式 | `/skill-name` 或 `@skill-name` |
| 作用范围 | frontmatter `paths` glob 限定生效文件 |
| 内置命令 | `/create-skill` 创建技能；`/migrate-to-skills`（Cursor 2.4+）把 rules/slash commands 自动转技能 |
| 兼容加载 | 递归加载 `.agents/skills/`、`.cursor/skills/`（含全局 `~/`），兼容 `.claude/skills/`、`.codex/skills/` |

## 最佳实践

- 规则保持精简（8 行左右最宜），单文件单一职责
- `globs` 尽量收紧，`alwaysApply` 吝啬使用
- 写具体命令而非哲学口号
- Rules 管"常驻约束"，Skills 管"按需流程"——别混用

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Cursor Rules](https://cursor.com/cn/docs/rules) | 官方文档 |
| [Cursor Skills](https://cursor.com/cn/docs/skills) | 技能文档 |
| [Cursor Directory](https://cursor.directory/) | 社区规则模板库 |
| [Awesome CursorRules](https://github.com/PatrickJS/awesome-cursorrules) | GitHub 精选集合 |

## 选型建议

新项目先用通用模板快速启动；成熟项目逐步细化规则；团队统一用私有规则库；多技术栈用多文件 .mdc 规则；需要多步流程（如"发布前检查清单"）时用 Skills，并把旧 .cursorrules 通过 /migrate-to-skills 迁移。