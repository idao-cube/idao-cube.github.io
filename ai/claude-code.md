## 产品与定位

Anthropic 官方推出的终端编程助手，基于 Claude 模型构建。强调安全性、可靠性和深度代码理解，适合专业开发者进行复杂项目工作。2026 年生态大幅扩张：除终端外，现在也可通过 **VS Code / JetBrains 扩展**使用，并新增语音、云端自动修复、桌面控制等能力，是 vibe-coding 榜单中「会读码者首选」。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 终端交互 | 纯命令行界面，无需 IDE |
| IDE 扩展 | VS Code / JetBrains 扩展内使用 |
| Voice Mode | 2026 新增语音交互模式 |
| Auto-fix | 云端自动修复 PR / CI 失败 |
| Computer Use CLI | 命令行控制桌面应用 |
| 文件编辑 | 读取、创建、修改代码文件 |
| Git 操作 | 自动 commit、branch 管理 |
| 搜索替换 | 正则匹配和批量修改 |
| 项目理解 | 分析代码结构和依赖 |
| 安全优先 | 明确权限控制，不自动执行 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `claude` | 启动交互会话 |
| `/help` | 查看帮助和命令列表 |
| `/edit` | 切换编辑模式 |
| `/bash` | 执行 shell 命令 |
| `/web` | 网页搜索和获取内容 |

## 调用与兼容性

```bash
# 安装
npm install -g @anthropic-ai/claude-code

# 启动
claude

# 指定模型
ANTHROPIC_MODEL=claude-sonnet-4-6 claude
```

## 版本与更新注意

- 持续更新支持最新 Claude 模型版本。
- **Claude Max 订阅**（$100/月 / $200/月）提供高配额与优先算力；轻量使用可按 token 计费。
- 注意模型配额和使用限制，Voice Mode / Computer Use 为较新能力，功能仍在完善中。

## 选型建议

需要 Claude 模型深度集成、终端工作流首选 Claude Code；与 Anthropic API 配合可实现完全可控的 AI 编程。