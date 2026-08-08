## 工具与定位

Matt Pocock（TypeScript 领域知名教育者）维护的开源 Agent 技能集。与 Superpowers 这类"全流程方法论"不同，它坚持**小而可组合**——每个技能只解决一个具体问题。最出名的 **grill-me**（116,000+ stars）会用 16-50 个问题对需求进行拷问式澄清，被社区称为"今年 ROI 最高的改变"——先问清需求再动手，大幅减少返工。

## 核心能力

| 技能 | 作用 |
| --- | --- |
| grill-me | 16-50 个问题的深度需求澄清，避免做错方向 |
| grill-with-docs | 澄清同时生成 shared-language 团队共识文档 |
| tdd | 测试驱动开发的轻量引导 |
| diagnose | 系统化问题诊断 |
| improve-codebase-architecture | 渐进式架构改进 |

## 安装与使用

| 命令 | 说明 |
| --- | --- |
| `npx skills@latest add mattpocock/skills` | 安装整个技能集 |
| 单独使用 | 可按需挑选 grill-me / tdd / diagnose 等单一技能 |

## 平台兼容

| 平台 | 说明 |
| --- | --- |
| Claude Code | 原生支持（~/.claude/skills） |
| Codex / Cursor / Gemini CLI | 通过 npx skills CLI 安装 |
| 其他 Agent | 兼容通用 skills 分发机制 |

## 版本与更新注意

技能集持续迭代，grill-me 的问题库会随社区反馈扩充；建议关注 repo 更新。作者明确反对"all-in-one"式技能框架（认为笨重），因此本集合不提供完整 SDLC 流程，需与其他工具搭配。

## 选型建议

需求常常模糊、返工成本高的团队强烈建议先引入 grill-me 单独体验；追求轻量、反感强方法论约束的开发者可选本集合替代 Superpowers；两者也可混用——用 grill-me 澄清需求、再用 Superpowers 的 TDD/审查技能收尾。