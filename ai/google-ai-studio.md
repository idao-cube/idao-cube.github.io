## 产品与定位

Google AI Studio 2.0 把 Google 的 vibe coding 能力整合进浏览器：内置 **Antigravity 编码智能体**（Google 的 agentic 开发引擎）与 **Firebase 后端**（Firestore 数据库、Firebase Auth 登录、Cloud Storage、Hosting、Cloud Functions），一句话即可生成带数据库与登录的生产级全栈应用。支持 Web（React / Next.js / Angular + Node.js 服务端运行时）与原生 Android（Kotlin / Jetpack Compose）。

从"提示词测试场"进化为"从提示词到生产"的全栈平台，免费即可上手——适合想零成本试水 vibe coding 的个人开发者与 Gemini 生态用户。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Antigravity Agent | 理解整个项目结构、跨文件生成代码、自动修错、自动安装 npm 依赖 |
| 内置 Firebase | 一句话自动开通 Firestore + Firebase Auth（Google 登录）并写好代码 |
| 服务端运行时 | Node.js 后端安全调用 API/数据库，密钥存 Secrets Manager 不暴露前端 |
| 多框架 | React / Next.js / Angular 可选；原生 Android（Kotlin + Compose） |
| 多人实时 | 原生支持实时协作、多人游戏等 WebSocket 场景 |
| App Gallery | 现成项目灵感库，一键预览、混改复用 |
| 一键带走 | 应用可一键从 AI Studio 迁移到独立 Antigravity 环境 |

## 定价

| 项 | 说明 |
| --- | --- |
| AI Studio 本体 | 免费（Google 账号即可） |
| Antigravity 用量 | 2026-03 起消耗 AI 积分（简单组件 5–10 积分、整页 20–50、带 Firebase 的全栈 100–300、大规模重构 50–150） |
| Firebase | 免费额度慷慨，超出部分单独计费 |
| 模型 | 默认基于 Gemini 系列（AI Studio 内按套餐配额使用） |

## 调用与兼容性

```bash
# 浏览器使用；部署到 Firebase Hosting
firebase deploy
# 输出：Hosting URL + Functions + Firestore 规则
```

- 平台：浏览器（Web 全栈 + Android 模拟器预览）
- 框架：React / Next.js / Angular / Kotlin + Jetpack Compose
- 后端：Firebase（Firestore、Auth、Storage、Hosting、Cloud Functions）

## 版本与更新注意

- 2026-03-18/20 重大更新：Sundar Pichai 官宣 AI Studio 2.0 = Antigravity agent + 内置 Firebase 集成。
- 2026-05-14 起，旧应用的 Gemini API 集成会自动升级为服务端密钥方案。
- 内部已用该模式构建数十万应用；与独立 Antigravity（IDE/CLI/SDK 三件套）为不同入口，共享同一 agent 引擎。
- 免费档有配额与积分限制，重度使用需关注 Gemini API 用量费用。

## 选型建议

- 想零成本体验"提示词 → 带数据库登录的全栈应用"：Google AI Studio 2.0 是最低门槛的入口。
- Gemini/Google 生态用户首选：与 Firebase、Workspace（Drive/Sheets 接入规划中）深度打通。
- 需要更强控制力（本地 IDE、CLI、自定义 SDK）时迁移到独立 Google Antigravity。
- 非 Google 生态用户：Lovable / Bolt.new 的托管后端体验更"开箱即用"，AI Studio 的 Firebase 体系有一定学习成本。