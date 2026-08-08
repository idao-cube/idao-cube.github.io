## 产品与定位

Warp 由前 Google Sheets 首席工程师 Zach Lloyd 创立（Sequoia、GV、Sam Altman、Marc Benioff、Dylan Field 投资，总部纽约），最初以"AI 原生终端"闻名，2026-04-28 宣布核心代码开源（AGPL-3.0，GitHub 63K+ stars，Rust 重写），并正式定位为 **Agentic Development Environment（ADE）**，号称近 100 万开发者使用，客户包括 Docker、Ramp、Peloton 以及超过半数 Fortune 500。

Warp 的三件套架构：**Warp Terminal**（现代开源终端，可挂载任意编码 agent——Claude Code、Codex、OpenCode、Gemini CLI 等）、**Warp Agent**（内置多模型编码 agent，具备多 agent 编排、模型路由、代码库索引与细粒度权限）、**Oz**（云端 agent 编排平台，在云端运行/编排 Claude Code、Codex、Warp Agent 等，提供 SDK/CLI 可编程启动，本地-云端任务无缝交接）。OpenAI 是其创始赞助商，agent 工作流由 GPT-5.5 驱动。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Warp Terminal | 开源现代终端，AI 补全、命令诊断、团队命令共享 |
| Warp Agent | 内置编码 agent：多 agent 编排、模型路由、代码库索引、细粒度权限 |
| Oz | 云 agent 编排平台：云端运行/编排多个 agent，SDK/CLI 可编程启动 |
| 多模型路由 | 支持 Claude Code/Codex/Gemini CLI 等外部 agent + Kimi/MiniMax/Qwen 等开源模型与 auto(open) 路由 |
| 界面模式 | 纯终端 → 最小 agent 设置（diff view + file tree）→ 完整 ADE，按需切换 |
| Settings 文件 | 程序化配置、跨设备同步团队默认值 |
| 本地-云端交接 | 本地会话可无缝切换到云端 Oz 继续跑长任务 |

## 计费方式

| 方案 | 价格 | 说明 |
| --- | --- | --- |
| 开源版 | 免费下载 | 终端与基础 agent 功能 |
| 企业版 | 商业方案 | 面向组织的托管、管理与安全需求 |

agent 的模型推理按各自提供商计费；Warp 自身以免费开源 + 企业服务为商业模式。

## 调用与兼容性

```bash
# macOS / Linux / Windows 下载 Warp 桌面应用
# 内置 Agent 开箱即用；也可在终端中直接使用已安装的 Claude Code / Codex / OpenCode
# 云端编排：warp oz --help 查看 Oz 的 SDK/CLI 启动与交接命令
```

界面模式可通过设置切换（纯终端 / 最小 agent 视图 / 完整 ADE）；新增 settings 文件支持程序化控制与跨设备同步。

## 版本与更新注意

- 2026-04-28 开源是重大转折：从闭源 AI 终端变为开源 ADE，同期新增 Kimi、MiniMax、Qwen 开源模型与 auto(open) 路由。
- 终端类工具竞争激烈（Ghostty、Kitty、iTerm2 等），Warp 的差异化在 AI agent 编排与云端 Oz。
- Rust 重写后性能与内存占用显著改善；开源版与企业版功能边界以官网为准。
- 适合重度终端用户与希望把多个 agent 收拢到一个入口的团队。

## 选型建议

- 重度终端用户：希望终端本身具备 AI 能力与 agent 挂载能力。
- 想统一调度多个编码 agent（本地 + 云端）的开发者：Oz 编排是差异化亮点。
- 偏好开源、可控的工程师：AGPL-3.0 需注意企业内部分发/修改的许可证要求。
- 追求极致极简、只用单一 IDE 内置 agent 的用户：Warp 是补充工具而非替代品。