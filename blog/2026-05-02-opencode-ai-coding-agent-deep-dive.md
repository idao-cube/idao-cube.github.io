> 项目地址：[github.com/anomalyco/opencode](https://github.com/anomalyco/opencode) | 153K Stars | MIT 协议

## 一、OpenCode 是什么？

**OpenCode** 是由 **anomalyco**开发的开源 AI 编码 Agent。它运行在终端中，提供完整的 TUI（终端 UI）体验。

核心定位很简单：**一个完全开源、不锁定任何模型供应商的 AI 编程助手**。

官方标语：*"The open source AI coding agent."*

> 项目最初由 Neovim 用户构建，因此对终端体验有极致追求。

### 核心亮点速览

| 特性                        | 说明                                                |
| --------------------------- | --------------------------------------------------- |
| **双 Agent 架构**     | build（全权限开发）+ plan（只读分析），Tab 一键切换 |
| **模型无关**          | 支持 Claude、OpenAI、Google、本地模型，不锁定供应商 |
| **LSP 原生支持**      | 开箱即用的语言服务器协议支持                        |
| **TUI 体验**          | 极致的终端 UI，支持多行编辑、斜杠命令、流式输出     |
| **客户端/服务器架构** | 前端只是客户端，可从手机 App 远程驱动               |
| **桌面 App（Beta）**  | 提供 macOS/Windows/Linux 桌面客户端                 |
| **153K Stars**        | GitHub 社区热度极高                                 |

---

## 二、双 Agent 架构：Build vs Plan

OpenCode 内置了两个独立的 Agent，通过 `Tab` 键即可在对话中即时切换。

### Build Agent（全权限开发）

默认 Agent，拥有完整的系统操作权限：

- 读写文件、创建 / 修改 / 删除
- 执行 Shell 命令
- 运行测试、构建、部署
- 安装依赖、配置环境
- 完整的代码修改和执行链路

适合日常开发、功能实现、调试修复等场景。

### Plan Agent（只读分析）

只读模式的 Agent，专为探索和分析设计：

- 阅读代码、搜索文件、理解架构
- **拒绝文件写入操作**
- 执行 Bash 命令前**征求用户许可**
- 适合在修改代码前先理解项目的结构

适合浏览不熟悉的代码库、做修改前的架构分析、审查代码。

> 两个 Agent 共享同一个对话上下文。你可以先用 Plan 分析完代码结构和影响范围，再切到 Build 执行修改——一个非常流畅的"先分析后执行"工作流。

---

## 三、模型无关架构

OpenCode 的核心理念之一：**不被任何模型供应商锁定**。

你可以在任意时刻切换底层模型，无需修改任何配置：

```bash
# 使用 Claude
opencode --model claude-sonnet-4-20250514

# 使用 OpenAI
opencode --model gpt-4o

# 使用 Google
opencode --model gemini-2.5-pro

# 使用本地模型
opencode --model ollama:codellama
```

> 团队推荐通过 OpenCode Zen 获取最佳开箱体验，但工具本身完全不强制使用特定供应商。

---

## 四、快速安装

### 一键安装（推荐）

```bash
curl -fsSL https://opencode.ai/install | bash
```

### 通过包管理器安装

```bash
# macOS（推荐）
brew install anomalyco/tap/opencode

# 或通过官方 Homebrew 仓库
brew install opencode

# Windows
scoop install opencode
# 或
choco install opencode

# Linux（Arch）
sudo pacman -S opencode
paru -S opencode-bin

# 全平台
npm i -g opencode-ai@latest
# 也支持 bun/pnpm/yarn

# 通过 mise
mise use -g opencode
```

### 系统要求

- **macOS**：原生支持
- **Linux**：原生支持
- **Windows**：支持（通过 Scoop/Chocolatey）

### 桌面 App（Beta）

OpenCode 提供桌面客户端，可在 [opencode.ai/download](https://opencode.ai/download) 或 [Releases 页面](https://github.com/anomalyco/opencode/releases) 下载：

| 平台                | 格式                           |
| ------------------- | ------------------------------ |
| macOS Apple Silicon | `.dmg`                       |
| macOS Intel         | `.dmg`                       |
| Windows             | `.exe`                       |
| Linux               | `.deb` / `.rpm` / AppImage |

```bash
# macOS 安装桌面版
brew install --cask opencode-desktop

# Windows
scoop install opencode-desktop
```

---

## 五、核心特性深度解析

### 5.1 TUI 终端体验

OpenCode 的终端 UI 是它的标志性特性。作为由 Neovim 用户构建的项目，它把终端交互做到了极致：

- **多行编辑**：在终端中流畅地编写和编辑多行提示
- **斜杠命令自动补全**：`/` 唤起命令列表，Tab 补全
- **流式工具输出**：工具执行结果实时流式展示
- **代码高亮与语法着色**：终端内完整的代码高亮
- **分屏视图**：同时查看编辑器和输出

### 5.2 内置 LSP 支持

与 Claude Code 等工具不同，OpenCode **开箱即用地集成了 LSP（Language Server Protocol）**：

- 自动检测项目中的语言服务器
- 提供精确的代码补全、跳转定义、引用查找
- 实时代码诊断和错误提示
- 类型检查和内联提示

这意味着 OpenCode 理解你的代码不仅仅是基于文本模式匹配，而是真正具备了 IDE 级别的语言感知能力。

### 5.3 客户端 / 服务器架构

OpenCode 采用客户端/服务器分离设计，这是它与大多数 AI 编码 Agent 的本质区别：

```text
手机 App ─┐
桌面 App ─┼── OpenCode Server ── LLM
TUI ──────┘
```

- **服务端**：核心 Agent 逻辑、代码处理、模型交互
- **客户端**：用户界面（TUI、桌面 App、未来可扩展移动端）

这意味着你可以在一台远程服务器上运行 OpenCode Server，然后从本地 TUI 或手机 App 驱动它。前端只是一个客户端实现——未来可以出现更多客户端。

### 5.4 内置 Agent：@general

除了 Build 和 Plan 两个主 Agent，OpenCode 内部还有一个 **general 子 Agent**，专为复杂搜索和多步骤任务设计：

- 在消息中输入 `@general` 即可调用
- 适合需要跨多个文件、多步骤推理的复杂查询
- 能自动拆解并执行子任务

---

## 六、命令速查

### 启动与模型选择

```bash
# 启动交互式对话
opencode

# 指定模型启动
opencode --model claude-sonnet-4-20250514

# 指定工作目录
opencode --directory /path/to/project

# 传递系统提示
opencode --system-prompt "你是一个资深 Rust 开发者"
```

### 对话内操作

| 操作         | 功能                        |
| ------------ | --------------------------- |
| `Tab`      | 切换 Build / Plan Agent     |
| `/`        | 唤起斜杠命令列表            |
| `@general` | 调用通用子 Agent            |
| 多行编辑     | 终端内流畅编辑长提示        |
| 流式输出     | 实时查看工具执行和 LLM 响应 |

---

## 七、配置与自定义

### 配置文件优先级

安装脚本的检测顺序：

1. `$OPENCODE_INSTALL_DIR` 环境变量指定的目录
2. `$XDG_BIN_DIR`
3. `$HOME/bin`
4. `$HOME/.opencode/bin`（默认）

### 编辑器支持

OpenCode 为多种编辑器提供了开箱即用的配置：

- **VS Code**：`.vscode/` 配置目录
- **Zed**：`.zed/` 配置目录
- **Neovim**：终端 TUI 本身就是 Neovim 风格

---

## 八、与 Claude Code 对比

OpenCode 的 README 直接承认它与 Claude Code "在能力上非常相似"，但指出了几个关键差异：

| 维度                    | OpenCode              | Claude Code    |
| ----------------------- | --------------------- | -------------- |
| **开源**          | ✅ 100% 开源（MIT）   | ❌ 闭源        |
| **供应商锁定**    | ❌ 模型无关，自由切换 | ✅ 仅限 Claude |
| **LSP 支持**      | ✅ 开箱即用           | ❌ 不支持      |
| **双 Agent 架构** | ✅ Build + Plan       | ❌ 单一模型    |
| **客户端/服务器** | ✅ 支持远程驱动       | ❌ 仅本地 CLI  |
| **桌面 App**      | ✅ Beta 版提供        | ✅ 有桌面版    |
| **语言**          | TypeScript + Rust     | TypeScript     |
| **Stars**         | 153K                  | —             |

---

## 九、谁适合用 OpenCode？

### 适合的场景

- **追求开源自由**：需要 100% 开源、MIT 协议的编码助手
- **多模型用户**：不想被锁定在单一模型供应商
- **终端爱好者**：极致 TUI 体验，Neovim 用户一定会喜欢
- **远程开发**：客户端/服务器架构，可在服务器上运行
- **LSP 重度用户**：开箱即用的语言服务器支持
- **先分析后执行**：Plan Agent 分析 + Build Agent 执行的工作流

### 当前限制

- 相对较新（与 Claude Code 等成熟产品比），生态尚在发展
- 桌面 App 仍为 Beta
- 文档和教程尚在完善中

---

## 十、技术架构

| 层级               | 技术选型                |
| ------------------ | ----------------------- |
| **主要语言** | TypeScript（60.2%）     |
| **文档**     | MDX（36.2%）            |
| **CSS/UI**   | CSS（2.8%）             |
| **原生模块** | Rust（0.4%）            |
| **构建系统** | Turborepo 管理 Monorepo |
| **包管理**   | Bun                     |
| **基础设施** | SST                     |
| **代码检查** | Oxlint                  |
| **格式工具** | Prettier                |
| **Git 钩子** | Husky                   |
| **Nix 支持** | Flake                   |

---

## 十一、总结

OpenCode 是 AI 编码助手领域一个重要的开源力量。它的**双 Agent 架构**（Plan+Build）提供了流畅的"先分析后执行"体验，**模型无关设计**让你不被任何供应商锁定，**客户端/服务器分离**则为未来的多端交互打开了可能性。

153K Stars 和 MIT 协议意味着它在开源社区有强大的信任基础。无论你是终端爱好者、多模型用户，还是单纯追求开源自由的开发者，都值得一试。

**快速开始：**

```bash
curl -fsSL https://opencode.ai/install | bash
opencode
```

> 技术栈：TypeScript 60% + Rust + MDX | 协议：MIT | 最新版本：v1.14.31（2026-05-01）
>
> 官网：[opencode.ai](https://opencode.ai) | 文档：[opencode.ai/docs](https://opencode.ai/docs)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)