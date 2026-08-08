## 产品与定位

Gemini CLI 是 Google 开源的**终端 AI 编程 Agent**，与 Claude Code、OpenCode 同类。它拥有高达 **1M token 的上下文窗口**、慷慨的免费额度，且完全开源，模型可选（默认 Gemini 系列，可切换其他提供商）。

它是 Google 生态开发者的首选，也是「想免费试水 AI 编程 Agent」用户的最佳起点——不花一分钱就能体验 Agent 化编码工作流。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 终端 Agent | 对话式编码、自动编辑文件、执行命令 |
| 1M 上下文 | 一次加载超大代码库/长文档 |
| 多模型可选 | 默认 Gemini，可配其他模型提供商 |
| 开源 | Apache 2.0，本地可审计可自托管 |
| 免费额度 | 慷慨的免费层，低成本起步 |
| 代理循环 | 支持 subagent、工具调用等复杂任务编排 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `gemini` | 启动交互式会话 |
| `gemini "重构 utils 目录"` | 单条指令直接执行任务 |
| `gemini --model gemini-3-pro` | 指定模型 |
| `gemini --export-chat` | 导出会话记录 |

## 调用与兼容性

```bash
# 安装
npm install -g @google/gemini-cli
# 或 Homebrew
brew install --cask gemini-cli
```

支持 macOS / Linux / Windows（WSL），需 Google AI API key 或 Gemini API 密钥。

## 版本与更新注意

- 快速迭代中，功能与模型频繁更新，关注 GitHub Releases。
- 终端工具，无图形界面；喜欢 IDE 内体验可配 Google Antigravity。

## 选型建议

- Google 生态用户、想零成本体验 Agent 编码 → Gemini CLI。
- 追求最大上下文（超大型 monorepo）→ Gemini CLI 的 1M 窗口是突出优势。
- 需要团队级协作、云端沙箱 → 配合 Antigravity 或 Codex 使用。