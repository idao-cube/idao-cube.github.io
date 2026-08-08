## 产品与定位

Cline（前身 **Claude Dev**）是**开源（Apache 2.0）** 的 AI 编码 Agent，以 VS Code 扩展起步，现已扩展到 CLI、SDK 与 JetBrains。它在开发者社区极受欢迎：**8M+ 安装量、65K+ GitHub stars**，Samsung 等大厂已采用。

Cline 的核心卖点是**开放与可控**：支持 30+ 模型提供商 BYOK（Anthropic / OpenAI / Google / OpenRouter / AWS Bedrock / Ollama 本地模型），数据与代码不经过 Cline 自有云，适合对代码隐私敏感、讨厌厂商锁定的开发者。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Plan / Act 双模式 | 先规划后执行，重要操作可确认 |
| 可审查 diff | 每一步改动清晰展示，随时回退 |
| Checkpoints | 任务节点快照，一键还原 |
| MCP 支持 | 接入任意 Model Context Protocol 工具 |
| Computer Use | Agent 可直接操作浏览器/桌面 |
| .clinerules | 项目级规则文件，约束 agent 行为 |
| Spend Limit | 预算上限，防止 token 跑飞 |
| 多端 | VS Code / CLI / SDK / JetBrains |

## 常用命令（CLI）

| 命令 | 作用 |
| --- | --- |
| `cline` | 启动 CLI 会话 |
| `cline --model anthropic/claude-sonnet-4-5` | 指定模型 |
| `cline --mode plan` | 进入规划模式 |
| `cline --session-resume` | 恢复历史会话 |

## 调用与兼容性

VS Code 扩展从市场安装；CLI 需 Node.js。模型侧自带 API key（BYOK）：可填 Anthropic、OpenAI、Google、OpenRouter、Bedrock、Ollama 等任意一家。

## 版本与更新注意

- 定价：扩展本身免费，费用 = 自付模型推理；**ClinePass $9.99/月**（开源权重模型打包，首月 $4.99）；Enterprise 定制。
- 开源迭代快，插件生态活跃，注意关注版本更新与 breaking changes。

## 选型建议

- 要开源可控、自带模型、数据不出本地的开发者 → Cline 首选。
- 想用 Ollama 本地模型离线编码 → Cline 支持最完整。
- 想开箱即用、不愿折腾模型配置 → Claude Code / Copilot 更省心。