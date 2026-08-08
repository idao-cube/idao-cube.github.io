> 项目地址：[github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | 129K Stars | MIT 协议

## 一、Hermes Agent 是什么？

**Hermes Agent** 是由 **Nous Research** 打造的开源、自进化 AI Agent。它的核心理念很简单：**这个代理会随着你的使用而成长**。

传统 AI Agent 每次对话都是独立的，用完就忘。Hermes 不一样——它有一个内置的**闭环学习机制**：

- 从每次交互中提取经验，自动生成技能
- 技能在使用过程中自我改进
- 跨会话构建用户模型，越用越懂你

> 项目标语："The agent that grows with you"（与你一同成长的代理）

### 核心亮点速览

| 特性 | 说明 |
|---|---|
| **闭环学习** | 自主创建技能，自动改进，跨会话记忆 |
| **多平台** | 一套后端同时支持 Telegram、Discord、Slack、WhatsApp、Signal、CLI |
| **终端 UI** | 完整 TUI 界面，多行编辑、斜杠命令自动补全、流式工具输出 |
| **模型无关** | 支持 Nous Portal、OpenRouter（200+ 模型）、NVIDIA NIM、OpenAI、Hugging Face 等 |
| **定时任务** | 内置 cron 调度器，支持日报、备份、审计等自动化 |
| **子代理** | 启动隔离子代理执行并行任务，通过 RPC 调用工具 |
| **6 种后端** | Local、Docker、SSH、Daytona、Singularity、Modal |

---

## 二、核心设计理念

### 2.1 闭环学习（Closed Learning Loop）

这是 Hermes 最核心的差异化能力。传统 Agent 的"学习"通常只是把对话历史存下来下次当上下文用，Hermes 则完全不同：

1. **经验提炼**：完成复杂任务后，Agent 会自动回顾执行过程，提取可复用的模式
2. **技能生成**：把提炼出的经验封装为可调用的技能文件
3. **技能进化**：技能在使用过程中持续被评估和改进，而非一成不变
4. **用户建模**：跨会话积累对用户偏好、工作风格的理解

这意味着你使用 Hermes 的时间越长，它对你的了解越深，自动化程度越高。

### 2.2 多平台架构

Hermes 采用**单一网关**架构，所有平台共享同一个 Agent 后端：

```text
Telegram ─┐
Discord  ─┤
Slack    ─┼── Gateway ── Agent Core
WhatsApp ─┤
Signal   ─┤
CLI/TUI ─┘
```

在任何平台上的交互都会被记录到同一个记忆系统和技能库中，无缝切换。

### 2.3 模型无关设计

通过 `hermes model` 命令，可以在不修改任何代码的情况下切换底层模型：

```bash
hermes model openrouter:anthropic/claude-sonnet-4-20250514
hermes model nousresearch:hermes-4
hermes model openai:gpt-4o
hermes model nvidia:llama-3.1-405b-instruct
```

支持 200+ 模型提供商，真正的"无锁定"架构。

---

## 三、快速安装与上手

### 系统要求

- Linux、macOS、WSL2（Windows 用户需使用 WSL2，不支持原生 Windows）
- Android 可通过 Termux 安装
- Python 3.11+

### 一键安装

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

### 开发者安装

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
./setup-hermes.sh
```

这个脚本会自动安装 `uv`（Python 包管理器）、创建虚拟环境、安装全部依赖并建立命令行链接。

### 手动安装

```bash
# 安装 uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 创建虚拟环境
uv venv venv --python 3.11
source venv/bin/activate

# 安装完整依赖
uv pip install -e ".[all,dev]"
```

### 首次启动

```bash
hermes
```

首次运行会自动启动配置向导，引导你设置 LLM 提供商、API Key 和基本参数。

---

## 四、核心命令详解

### 基本命令

| 命令 | 用途 |
|---|---|
| `hermes` | 启动交互式 CLI 对话 |
| `hermes model` | 选择 LLM 提供商和模型 |
| `hermes tools` | 配置已启用的工具集 |
| `hermes config set` | 设置配置项 |
| `hermes gateway` | 启动消息网关（连接 Telegram 等平台） |
| `hermes setup` | 完整配置向导 |
| `hermes update` | 更新到最新版本 |
| `hermes doctor` | 诊断配置和连接问题 |
| `hermes claw migrate` | 从 OpenClaw 迁移数据 |

### 对话内斜杠命令

| 命令 | 功能 |
|---|---|
| `/new` 或 `/reset` | 新建对话 |
| `/model provider:model` | 对话中切换模型 |
| `/personality [name]` | 设置个性 |
| `/retry` | 重试上一轮 |
| `/undo` | 撤销上一轮 |
| `/compress` | 压缩上下文 |
| `/usage` | 查看用量统计 |
| `/insights` | 查看洞察分析 |
| `/skills` | 浏览技能列表 |
| `/技能名` | 手动触发指定技能 |

---

## 五、工具系统（Toolsets）

Hermes 内置了 **40+ 工具集**，覆盖开发全场景。你可以通过 `hermes tools` 按需启用或禁用。

### 主要工具集分类

| 类别 | 包含工具 |
|---|---|
| **文件操作** | 读写文件、目录管理、文件搜索 |
| **代码执行** | Python 沙箱、Shell 命令、代码审查 |
| **网络请求** | HTTP 请求、网页抓取、API 调用 |
| **浏览器** | Playwright 集成、网页截图、DOM 操作 |
| **数据库** | SQL 查询、向量数据库、缓存操作 |
| **图像处理** | 图片分析、生成、格式转换 |
| **文档处理** | PDF、Word、Excel、Markdown 转换 |
| **MCP 集成** | 支持任意 MCP 服务器协议工具 |
| **自定义工具** | 通过 Python RPC 编写自己的工具 |

### MCP 集成

Hermes 完全兼容 Model Context Protocol（MCP），可以接入任何 MCP 服务器提供的工具能力：

```json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/workspace"]
    }
  }
}
```

这意味着你可以把 Playwright、Figma、文件系统等 MCP 生态中的数百个工具直接接入 Hermes。

---

## 六、技能系统与闭环学习

### 技能是什么？

在 Hermes 中，技能是**可重用的自动化工作流**，封装了一系列指令和最佳实践。与一次性提示不同，技能可以被版本管理、共享和自动改进。

### 技能生命周期

```
创建 → 使用 → 评估 → 改进 → 再使用
  ↑                         │
  └──────── 闭环 ──────────┘
```

1. **创建**：Agent 在完成复杂任务后自动提取模式，生成技能
2. **使用**：当类似场景出现时，技能自动激活
3. **评估**：每次使用后，Agent 评估技能效果
4. **改进**：根据评估结果自动优化技能内容
5. **循环**：持续迭代，越用越好

### 技能来源

Hermes 兼容 [agentskills.io](https://agentskills.io) 开放标准，可以从社区下载共享技能，也可以把你自己的技能发布到社区。

---

## 七、定时任务与自动化

Hermes 内置了完整的时间调度系统：

```bash
# 创建定时任务
hermes cron add --schedule "0 9 * * 1-5" --task "生成每日站会摘要"

# 列出所有定时任务
hermes cron list

# 删除定时任务
hermes cron remove <id>
```

支持将任务结果投递到任意已连接平台——Telegram 收到日报、Discord 收到审计报告、Slack 收到构建通知等。

---

## 八、子代理与并行执行

对于需要并行处理的工作，Hermes 可以启动隔离的子代理：

```text
主代理 → 拆解任务 → 子代理 A（RPC 调用工具集）
                  → 子代理 B（独立进程）
                  → 子代理 C（SSH 远程执行）
```

子代理完全隔离，互不干扰，各自在自己的环境中执行。结果自动汇总回主代理。

---

## 九、持久化与后端支持

Hermes 支持 **6 种终端后端**，从本地到云端全覆盖：

| 后端 | 适用场景 | 特点 |
|---|---|---|
| **Local** | 本地开发、个人使用 | 零配置，直接运行 |
| **Docker** | 标准化部署 | 环境隔离，一键启动 |
| **SSH** | 远程服务器 | 在服务器上运行，本地交互 |
| **Daytona** | 团队协作 | 云端开发环境 |
| **Singularity** | 高性能计算 | 批量任务处理 |
| **Modal** | Serverless | 按需计费，空闲时休眠 |

其中 Modal 和 Singularity 支持服务端持久化——Agent 空闲时自动休眠，需要时即时唤醒，节省成本。

---

## 十、从 OpenClaw 迁移

如果你是 OpenClaw 老用户，Hermes 提供了一键迁移工具：

```bash
hermes claw migrate
```

会自动迁移以下内容：

- `SOUL.md`、`MEMORY.md`、`USER.md` 等上下文文件
- 已安装的技能
- 命令白名单
- 消息平台配置（Telegram、Discord 等）
- API Key（OpenRouter、OpenAI、Anthropic、ElevenLabs）

---

## 十一、开发与扩展

### 项目结构

```
hermes-agent/
├── agent/           # 核心 Agent 循环
├── skills/          # 技能实现
├── gateway/         # 消息网关
├── plugins/         # 插件系统
├── environments/    # RL 训练环境（Atropos）
├── web/             # Web 界面
├── ui-tui/          # 终端 UI
├── hermes_cli/      # CLI 实现
├── docs/            # 文档
└── cron/            # 定时任务系统
```

### 自定义开发

Hermes 支持多种扩展方式：

1. **自定义技能**：在 `skills/` 目录下创建标准格式的技能文件
2. **自定义工具**：通过 Python RPC 协议暴露任意 Python 函数为工具
3. **MCP 服务器**：对接 MCP 生态中的任意服务器
4. **插件系统**：通过 `plugins/` 目录扩展核心功能

---

## 十二、适用场景

### 个人开发者

- 自动化代码审查、测试生成
- 项目管理与任务追踪
- 技术文档编写与维护
- 跨会话的项目上下文管理

### 团队协作

- Telegram/Discord 群聊中的 AI 助手
- 定时报告生成与推送
- 代码仓库监控与通知
- 知识库问答

### 研究与实验

- 批量轨迹生成与评估
- RL 环境集成（Atropos）
- Agent 行为分析与改进
- 多模型对比测试

---

## 十三、与其他 Agent 框架对比

| 特性 | Hermes Agent | Claude Code | AutoGPT | CrewAI |
|---|---|---|---|---|
| 闭环学习 | ✅ 原生 | ❌ | ❌ | ❌ |
| 多平台 | ✅ Telegram/Discord/Slack 等 | ❌ 仅 CLI | ❌ | ❌ |
| 模型无关 | ✅ 200+ 模型 | ❌ 仅 Claude | ✅ | ✅ |
| 终端 UI | ✅ 完整 TUI | ✅ | ❌ | ❌ |
| 子代理并行 | ✅ | ❌ | ❌ | ✅ |
| 定时任务 | ✅ 内置 cron | ❌ | ❌ | ❌ |
| 技能市场 | ✅ agentskills.io | ✅ skills.sh | ❌ | ❌ |
| MCP 兼容 | ✅ | ✅ | ❌ | ❌ |
| 安装量 | 129K Stars | - | 175K Stars | 27K Stars |

---

## 十四、总结

Hermes Agent 不是一个普通 AI Agent 框架。它的**闭环学习机制**使其区别于市面上绝大多数 Agent 项目——它不只是执行指令，而是在执行中学习、在学习中进化。

对于想深入探索 AI Agent 可能性的开发者来说，Hermes 提供了一个完整的试验场：从 CLI/TUI 交互到多平台消息网关，从技能系统到 MCP 集成，从单 Agent 到并行子代理，从本地执行到云原生部署。

**快速开始：**

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
hermes
```

> 技术栈：Python 88% + TypeScript 9% | 协议：MIT | 最新版本：v0.12.0（2026-04-30）
>
> 文档：[hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs/)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)