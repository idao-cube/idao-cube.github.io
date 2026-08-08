## 产品与定位

Hermes 是开源自进化 AI Agent（119K+ GitHub Stars），不是绑定 IDE 的编程助手，也不是单 API 聊天包装，而是运行在服务器上、能学习记忆、越跑越强的自主 Agent。支持 OpenRouter（200+模型）、OpenAI、Claude、Kimi、MiniMax 等多模型切换。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 多平台接入 | Telegram、Discord、Slack、WhatsApp、Signal、Email、CLI |
| 记忆增长 | 持久化记忆、跨会话 FTS5 搜索、自动生成技能 |
| 技能自创 | 复杂任务后自动创建可复用技能，使用中持续优化 |
| 调度自动化 | 自然语言 Cron 定时任务，报告、备份、日报自动执行 |
| 并行子 Agent | 隔离子 Agent 独立对话、终端、Python RPC 脚本 |
| 真实沙箱 | local/Docker/SSH/Daytona/Singularity/Modal 六种后端 |
| 浏览器控制 | Web 搜索、浏览器自动化、视觉、图片生成、TTS、多模型推理 |
| 研究工具 | 批量轨迹生成、RL 训练、轨迹压缩 |

## 常用命令

| 命令 | 作用 | 常见建议 |
| --- | --- | --- |
| `curl -fsSL .../install.sh \| bash` | 一键安装 | 官方安装脚本 |
| `hermes setup` | 引导配置 | 首次运行使用 |
| `hermes model` | 切换模型 | 运行时动态切换 |
| `hermes agents` | Agent 管理 | 配置多 Agent |

## 调用与兼容性

```bash
# 一键安装
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# 配置向导
hermes setup

# 指定模型启动
hermes model anthropic/claude-sonnet-4-6
```

## 版本与更新注意

活跃维护中，功能持续迭代。建议关注 GitHub 获取最新功能和模型��持。

## 选型建议

需要跨平台个人助手、多模型统一管理、自动任务调度首选 Hermes；研究场景可利用其批量处理和 RL 训练能力。