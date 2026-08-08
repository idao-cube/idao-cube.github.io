## 产品与定位

作为 AI 编程助手的浏览器自动化工具，提供真实 Chromium 控制能力。配合视觉模型可实现网页截图描述、交互自动化和数据抓取。兼容 pi、OpenCode 等主流编程助手。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 页面导航 | `open <url>` 访问任意网页 |
| 元素交互 | `click`、`fill`、`press` 等 DOM 操作 |
| 快照提取 | `snapshot -i` 获取可交互元素列表 |
| 截图支持 | 返回 base64 图片供视觉��型分析 |
| 内容读取 | `get text`、`get title`、`get url` |
| 会话清理 | 自动关闭浏览器进程 |

## 常用命令

| 命令 | 作用 | 示例 |
| --- | --- | --- |
| `open <url>` | 打开网页 | `open https://example.com` |
| `snapshot -i` | 获取交互元素 | 返回带 @ref 的元素列表 |
| `click @<ref>` | 点击元素 | `click @e1` |
| `fill @<ref> <text>` | 输入文本 | `fill @e2 "query"` |
| `screenshot [--full]` | 页面截图 | 返回图片供视觉分析 |
| `close` | 关闭浏览器 | 清理会话资源 |

## 调用与兼容性

```bash
# 安装
npm install -g agent-browser
agent-browser install  # 下载 Chromium

# 配合 pi 使用
pi encode ./my-project
# 在 pi 中使用 browser 工具

# 配合 OpenCode 使用
opencode encode ./my-project
# 通过 MCP 扩展接入
```

## 依赖要求

- Node.js ≥ 20
- Chromium 浏览器（通过 install 命令下载）
- 视觉能力模型（用于截图分析）：Claude Sonnet/Opus、GPT-4o、Gemini Pro 等

## 版本与更新注意

需配合对应版本的编程助手使用，截图结果会自动截断保护上下文窗口。

## 选型建��

需要浏览器自动化能力的 AI 编程助手时安装；适合网页测试、内容抓取、数据采集等场景。