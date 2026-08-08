## 产品与定位

Antigravity 是 Google 推出的 **agentic 开发平台**（2025 年末公开预览，**2.0 版于 2026-05-19 发布**）。它不是单一工具，而是三件套：

- **Antigravity IDE**：完整 agentic IDE，含 agent manager、artifacts、代码库理解；
- **Antigravity CLI**：终端优先的自主编码 agent，支持 shell 命令与后台子代理；
- **Antigravity SDK**：Python 库，用于原型化自定义 agent harness。

平台**模型可选**：Gemini 3 Pro、Claude Sonnet 4.5、GPT-OSS 均可用。个人使用免费，是「免费试水 agentic 编程」的最佳入口之一。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Agentic IDE | agent manager + artifacts + 代码库理解 |
| CLI | 终端自主编码 agent + shell 命令 + 后台子代理 |
| SDK | Python 构建自定义 agent harness |
| 多模型 | Gemini 3 Pro / Claude Sonnet 4.5 / GPT-OSS 可选 |
| 个人免费 | $0 即可使用核心功能 |
| 跨平台 | Windows / macOS / Linux |

## 2026 定价

| 计划 | 价格 | 说明 |
| --- | --- | --- |
| 个人版 | $0/月 | 免费使用 |
| Google AI Pro | 订阅制 | 更高限额 + 弹性 AI credit 池 |
| AI Ultra | $100/月（5x 限额）或 $200/月（20x 限额） | 2026-05-19 新推/降价（20x 从 $250 降至 $200） |
| 组织版 | 经 Google Cloud 消费制 | Gemini Enterprise Agent Platform |

## 调用与兼容性

```bash
# CLI 安装（macOS/Linux）
brew install --cask google-antigravity
```

需登录 Google 账号；组织版走 Google Cloud 企业流程。

## 版本与更新注意

- 2.0（2026-05-19）是重要里程碑：UI、agent 能力、定价全面升级。
- 产品仍在快速演进，API 可能变动，SDK 用户关注版本迁移。

## 选型建议

- Google 生态用户、想免费体验完整 agentic 工作流 → Antigravity 首选。
- 只需要终端 agent → 轻量用 Gemini CLI 即可。
- 需要跨厂商 IDE（VS Code/JetBrains 习惯）→ Cline / Continue 更贴合。