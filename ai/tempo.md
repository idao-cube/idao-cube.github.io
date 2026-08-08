## 产品与定位

Tempo（前身 Tempo Labs，YC S23）是 AI 驱动的可视化 React 应用构建平台。它把"提示词 → 代码"与"可视化拖拽编辑"结合：先用自然语言（或截图、Figma 设计稿、Storybook 组件库）生成结构化 React 组件，再在可视化画布中直接拖拽、改样式——每一次修改都同步到真实源代码，打开 VS Code 即可继续开发，输出推送到 GitHub，不是沙箱产物。

定位介于"设计工具"与"AI 生成器"之间：适合前端产品团队、设计师、PM 与开发者协作，从 mockup 直接到生产级 React 代码。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 提示词生成应用 | 自然语言描述即生成完整 React/TypeScript 应用，多智能体先做用户流程与架构规划再写码 |
| 可视化编辑器 | 拖拽布局、样式面板、组件树，直接改写真实代码而非生成 mockup |
| Figma 插件 | 一键把设计稿转为可编辑的 React 组件（JSX） |
| Storybook 导入 | 连接现有组件库，保证生成输出与设计系统一致 |
| 免费修错 | 所有套餐的 AI 错误修复不消耗积分 |
| GitHub / VS Code | 生成代码直接推 GitHub、本地 VS Code 打开，代码完全自主 |
| MCP App Store | 通过 MCP 接入第三方集成 |

## 定价

| 套餐 | 价格 | 说明 |
| --- | --- | --- |
| Free | $0/月 | 30 积分（每天上限 5），免费修错，适合试水 |
| Pro | $30/月 | 150 积分，解锁全部 code/reasoning 智能体；可 $50 加购 250 积分 |
| Agent+ | $4,500/月 | Tempo 自有工程师+设计师每周交付 1–3 个功能，48–72 小时周转 |

## 调用与兼容性

```bash
# 无 CLI 安装，浏览器使用；生成代码可本地接管
# 推送到 GitHub 后本地开发
git clone <your-repo> && code .
```

- 平台：浏览器 + GitHub + Vercel + VS Code
- 技术栈：仅 React / Next.js（不支持 Vue、Svelte、Angular、React Native）
- 输出：真实 React + TypeScript + Tailwind + shadcn/ui 代码

## 版本与更新注意

- 2023 年 YC S23 以 Tempo Labs 身份发布；2024–2025 增加多智能体规划与 MCP App Store；2025 年推出 Agent+ 人工交付档。
- 2026 年定价稳定在 Free / $30 Pro / $4,500 Agent+。
- 注意：Pro 与 Agent+ 之间存在巨大价格断档，无中间档位；Pro 以下无团队协作功能、无 API、不可自托管。
- 积分按操作消耗（多步生成任务耗得快），重度迭代前先评估用量。

## 选型建议

- 前端 React 产品团队想要"设计工具般的工作流 + 真实可维护代码库"，Tempo 是首选之一。
- 与 Bolt.new 的"沙箱即用"不同，Tempo 输出直接进你的 GitHub 仓库，生产可用。
- 若技术栈混用（Vue/Svelte）或需要自托管/完全本地模型，请考虑 Bolt.new 或开源方案。
- 非技术用户若只想快速验证 MVP，Lovable 上手门槛更低；Tempo 更适合有 React 基础、要长期维护代码的团队。