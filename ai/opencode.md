## 产品与定位

面向开发者打造的开源 AI 编程助手，140K+ GitHub Stars，支持 75+ 模型提供商。与 Claude Code 功能相近但完全开源、不绑定特定模型，强调隐私优先与终端优先体验。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 多会话并行 | 支持同一项目多个 Agent 并行工作 |
| MCP 工具 | 支持 Model Context Protocol 扩展工具生态 |
| LSP 集成 | 自动加载项目对应语言服务器 |
| 任意模型 | 支持 Claude/GPT/Gemini 及本地模型 |
| 多端支持 | 终端 TUI / 桌面应用 / IDE 扩展 |
| 隐私优先 | 代码和上下文数据不存储到外部 |

## 常用命令

| 命令 | 作用 | 常见建议 |
| --- | --- | --- |
| `opencode` | 启动 TUI 交互界面 | 默认启动交互模式 |
| `opencode encode [project]` | 编码任务 | 指定项目路径开始工作 |
| `opencode mcp` | MCP 服务器管理 | 添加管理扩展工具 |
| `opencode serve` | 启动 API 服务器 | 支持远程调用和控制 |
| `--model` / `-m` | 指定模型 | 格式 `provider/model` |
| `--continue` / `-c` | 继续上次会话 | 恢复中断的工作 |

## 调用与兼容性

```bash
# 快速开始
opencode encode ./my-project

# 使用指定模型继续会话
opencode -c -m anthropic/claude-sonnet-4-6

# 启动远程 API 服务器
opencode serve --port 18789
```

## 版本与更新注意

活跃维护中，功能更新较快。建议关注官方文档获取最新特性和模型支持。

## 选型建议

需要开源、可控、跨模型编程助手的团队首选 OpenCode；与 VS Code / Cursor / Neovim 等主流编辑器配合使用效果最佳。