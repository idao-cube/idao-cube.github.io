## 产品与定位

Anthropic 2026 年 4 月推出的官方命令行客户端，用于部署和管理云端 Claude Agent。不同于 Claude Code 的交互式编程助手，Ant CLI 专注于 Agent 的创建、配置和云端运行。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Agent 管理 | 创建、配置、部署云端 Agent |
| 环境管理 | 多环境配置和切换 |
| 会话管理 | 启动和管理会话 |
| Shell 集成 | 支持管道和脚本自动化 |
| 按需计费 | 会话按时间计费（$0.08/小时） |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `ant agent create` | 创建新 Agent |
| `ant agent list` | 列出所有 Agent |
| `ant session start` | 启动会话 |
| `ant session stop` | 停止会话 |
| `ant env list` | 列出环境配置 |

## 计费方式

- 会话时间：$0.08/小时
- Token 费用：按标准 Claude API 费率

## 调用与兼容性

```bash
# 安装（Homebrew）
brew install anthropic/ant-cli

# 安装（Go）
go install github.com/anthropic/ant-cli@latest

# 创建 Agent
ant agent create my-agent --model claude-sonnet-4-6

# 启动会话
ant session start my-agent
```

## 版本与更新注意

2026 年 4 月新推出，持续更新中。需要 Anthropic API Key。

## 选型建议

需要云端 Agent 自动化、CI/CD 集成、Shell 脚本自动化首选 Ant CLI；本地交互式编程选 Claude Code。