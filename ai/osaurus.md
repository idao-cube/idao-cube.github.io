## 产品与定位

Osaurus 是 macOS 上的 AI Agent 原生沙盒（harness），用 Swift 为 Apple Silicon 打造，无 Electron、无遥测、无订阅。它坚持"本地优先"：默认用 MLX 优化推理在 M 系列芯片上跑开源模型（Gemma 4、Qwen3.6、GPT-OSS、Llama 等，自带精选量化库），Wi-Fi 关掉也能用；需要更强能力时再按需接入 ChatGPT、Claude、Gemini 等云端模型，所有模型共享同一份持久记忆。MIT 开源，GitHub 7.4K stars、超 18.5 万下载，2026-07 登上 PH 日榜 #2。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 本地模型优先 | MLX 优化推理，模型目录与 LM Studio/Ollama/MLX 共享 |
| 完全离线 | 断网可用，推理在本地芯片毫秒级完成 |
| 自主 Agent | 语音控制、文件夹监听、浏览器插件、并行任务 |
| 硬件沙箱 | sandbox-exec 隔离，SQLCipher 加密存储（2026-04 安全加固） |
| MCP Server | 完整 MCP 协议，20+ 原生插件（邮件/日历/浏览器/视觉/Git/文件系统） |
| 持久记忆 | 跨模型共享上下文与记忆，持续累积 |
| 加密身份 | 基于密码学的 agent 身份与配对凭证 |
| 多云接入 | 需要时接 ChatGPT/Claude/Gemini，数据出境由你决定 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `brew install osaurus` | Homebrew 安装 |
| 下载 .dmg | Releases 页安装后 Spotlight 启动 |
| `model: "foundation"` | macOS 26+ 使用 Apple 本地端侧模型（零成本） |
| `OSU_MODELS_DIR` | 自定义模型存储目录（默认 ~/MLXModels） |

## 调用与兼容性

```bash
# 安装
brew install osaurus

# 环境要求：macOS 15.5+、Apple Silicon（M 系列）
# Linux VM 沙箱需 macOS 26+ (Tahoe)；旧版本自动回退到 sandbox-exec 原生沙箱

# 本地模型运行（示例）
osaurus run --model "gemma-4" --task "整理 ~/Downloads 里的文件"
```

## 版本与更新注意

要求 macOS 15.5+ 与 Apple Silicon，Intel Mac 不支持。沙箱策略随 macOS 版本变化：macOS 26+ 使用 Linux VM 隔离，旧版用 Seatbelt 沙箱（网络为全有或全无，pip/npm 可装但无 apk）。2026-04 完成安全审计加固（存储加密、agent 级配对密钥、请求体大小限制）。MIT 许可，永久免费无使用上限。

## 选型建议

重视隐私、想拥有自己 AI 的 Mac 用户首选 Osaurus——本地模型+断网可用，代码、对话、文件都不出机器。与 OpenClaw（跨平台个人助手，偏消息渠道接入）形成互补：Osaurus 强在 Apple 生态原生与本地推理，OpenClaw 强在多平台消息网关。需要 Windows/Linux 支持或云端托管时，可考虑 OpenClaw/KiloClaw。