## 产品与定位

OpenCode Go 是一项低成本的订阅服务 —— **首月 $5，之后 $10/月** —— 让你能够稳定地访问流行的开源编程模型。由 OpenCode 团队精选模型与提供商组合，经过基准测试，专为编程 Agent 场景优化。

## 定价与使用限制

| 维度 | 额度 |
| --- | --- |
| 首月体验 | $5 |
| 后续月费 | $10/月 |
| 5 小时限额 | $12 |
| 每周限额 | $30 |
| 每月限额 | $60 |

超出限额后，可开启「使用余额」选项，自动扣除 Zen 账户余额继续调用。

## 支持模型

| 模型 | 说明 |
| --- | --- |
| GLM-5 / GLM-5.1 | 智谱最新系列 |
| Kimi K2.5 / K2.6 | Moonshot 最新模型 |
| DeepSeek V4 Pro / Flash | 深度求索 V4 系列 |
| MiMo-V2-Pro / V2-Omni / V2.5 / V2.5-Pro | 小米 MiMo 系列 |
| MiniMax M2.5 / M2.7 | MiniMax |
| Qwen3.5 Plus / Qwen3.6 Plus | 阿里千问 |

模型部署在美国、欧盟和新加坡，为全球用户提供低延迟稳定访问。

## 使用方式

1. 登录 [OpenCode Zen](https://opencode.ai/auth)，订阅 Go 并复制 API Key
2. 在 TUI 中运行 `/connect`，选择 `OpenCode Go`，粘贴 API Key
3. 运行 `/models` 查看可用模型列表

也可通过 API 直接调用，端点：`https://opencode.ai/zen/go/v1/chat/completions`

## 隐私与合规

所有提供商遵循零留存策略，不会使用你的数据进行模型训练。

## 选型建议

追求低成本、稳定访问开源编程模型的个人用户首选；企业用户可参考 OpenCode Enterprise 方案。