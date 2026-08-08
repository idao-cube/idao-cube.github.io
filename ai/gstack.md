## 工具与定位

GStack 是 Y Combinator CEO Garry Tan 公开的 Claude Code 配置仓库（约 110K stars），把一家创业公司开发流程中的**关键角色与方法论**沉淀为 23 个角色化 slash 命令——从产品愿景到发布后复盘，每个命令让 Claude Code 扮演特定角色执行任务，适合创始人/小团队把"YC 式打法"固化进日常开发。

## 核心能力

| 命令角色 | 作用 |
| --- | --- |
| CEO / product vision | 产品方向与愿景梳理 |
| designer | 设计决策与评审 |
| eng manager | 工程管理与任务拆解 |
| release manager | 发布流程把关 |
| doc engineer | 文档编写与维护 |
| QA | 质量保障与测试 |
| post-launch retrospective | 发布后复盘迭代 |

共 23 个角色化 slash 命令，覆盖需求→设计→开发→QA→发布→复盘的完整循环。

## 安装与使用

| 步骤 | 说明 |
| --- | --- |
| 1. git clone | 克隆 garrytan/gstack 仓库 |
| 2. 运行 setup 脚本 | 自动安装配置与命令 |
| 3. 指定宿主 | `--host kiro` / `--host cursor` 等参数适配不同 Agent |

## 平台兼容

| 平台 | 说明 |
| --- | --- |
| Claude Code | 原生（默认宿主） |
| Cursor | setup 脚本 `--host cursor` |
| Kiro 等新兴 Agent | `--host kiro` 等参数支持 |

## 版本与更新注意

配置随 Garry Tan 本人工作流持续演进，fork 后可自由定制；注意命令依赖特定 Agent 的 slash command 语法，跨平台迁移需核对。面向创业公司场景，大型组织流程可能不完全匹配。

## 选型建议

创始人、独立开发者或小型团队希望复用 YC 式角色化工作流时首选 GStack；与技能库（Agent Skills/Superpowers）互补——GStack 提供角色视角，技能库提供操作步骤。企业级团队可取其角色划分思路自建配置。