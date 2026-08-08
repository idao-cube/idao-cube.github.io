## 产品与定位

开源的个人 AI 助手框架，通过 Gateway 控制平面连接多种模型和渠道。支持 macOS/iOS/Android 语音对话、Canvas 渲染与消息平台接入（Discord、Telegram、Nostr 等）。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 多渠道接入 | 语音对话、Canvas 交互、消息平台 |
| 多模型支持 | OpenAI、Claude、Gemini、本地模型 |
| 技能系统 | 可扩展的技能（Skills）和工具生态 |
| 隐私优先 | 数据自主可控，本地运行 |
| 跨平台 | macOS、Linux、Windows (WSL2) |
| Gateway | 控制平面连接模型与渠道 |

## 常用命令

| 命令 | 作用 | 常见建议 |
| --- | --- | --- |
| `openclaw onboard` | 引导设置 | 推荐首次运行使用 |
| `openclaw gateway` | 启动网关 | 默认端口 18789 |
| `openclaw agents` | Agent 管理 | 配置和管理多个 Agent |

## 调用与兼容性

```bash
# 安装
npm install -g openclaw@latest
# 或 pnpm add -g openclaw@latest

# 引导设置
openclaw onboard --install-daemon

# 启动网关
openclaw gateway --port 18789 --verbose
```

## 版本与更新注意

活跃维护中，版本更新频繁。Windows 用户推荐使用 WSL2 环境以获得最佳体验。2026 年 OpenClaw 生态在 Product Hunt 爆发：评分 5.0（71 条评价），成为独立分类，2026 年 3 月"C*Claw"系列产品一度霸榜（KiloClaw 托管版、Kimi Claw 等衍生品），社区已围绕它建立托管服务与观测集成（如 LangWatch 支持 OpenClaw 遥测）。选型时注意区分官方项目与第三方衍生品。

## 选型建议

需要构建个人 AI 助手、跨平台消息集成、语音交互能力的开发者首选 OpenClaw；完全开源可自托管实现数据自主。