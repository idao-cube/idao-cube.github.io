> 项目地址：[github.com/openclaw/openclaw](https://github.com/openclaw/openclaw) | 367K Stars | MIT 协议

## 一、OpenClaw 是什么？

**OpenClaw** 是一个**自托管、本地优先的个人 AI 助手**——运行在你自己的设备上，数据不出门，在任何消息平台都能用。

官方标语：*"Your own personal AI assistant. Any OS. Any Platform. The lobster way."*

它由 **Peter Steinberger（steipete）** 为 **Molty**（一只太空龙虾 AI 助手）创建，现已成为 GitHub 上最受欢迎的 AI 开源项目之一。

> 核心理念：它在**你已经使用的消息平台上**与你交流，而不是强迫你打开一个新的应用。

### 核心亮点速览

| 特性 | 说明 |
|---|---|
| **自托管** | 运行在你自己的设备或服务器上，数据完全由你掌控 |
| **20+ 消息平台** | WhatsApp、Telegram、Discord、微信、QQ、iMessage 等全覆盖 |
| **多 Agent 路由** | 不同渠道/账号可路由到独立的 Agent，每个 Agent 拥有隔离会话 |
| **Local-first** | 本地优先架构，网关是所有会话、渠道、工具和事件的单一控制平面 |
| **Live Canvas** | 浏览器内的可视化工作空间，支持实时协作 |
| **语音唤醒 + 对话** | macOS/iOS 语音唤醒，Android 连续语音对话 |
| **Companion App** | iOS 和 Android 节点应用，通过 WebSocket 配对连接 |
| **367K Stars** | GitHub 社区热度极高 |

---

## 二、架构设计

### 2.1 整体架构

```
消息平台 ─┐
WhatsApp ─┤
Telegram ─┤
Discord  ─┼── OpenClaw Gateway ── Agent ── LLM
微信 ─────┤                        │
QQ ───────┤                    ┌───┴───┐
iMessage ─┘                Tools  Skills  Canvas
```

**Gateway（网关）**是 OpenClaw 的核心——它是会话、渠道、工具和事件的单一控制平面。所有消息从各平台进入网关，由网关路由到对应的 Agent，Agent 调用工具和技能来完成任务。

### 2.2 多 Agent 路由

OpenClaw 支持**多 Agent 架构**——你可以为不同的消息渠道、账号或联系人配置不同的 Agent：

- 每个 Agent 有独立的会话空间
- 每个 Agent 可以配置不同的模型
- 每个 Agent 可以设定不同的个性（通过 SOUL.md）
- Agent 之间的资源完全隔离

这意味着你可以让工作用的 Telegram 账号使用一个 Agent，个人 WhatsApp 使用另一个，各有各的记忆和配置。

### 2.3 沙箱模式

非主会话支持三种沙箱后端，提供安全隔离：

| 沙箱 | 适用场景 |
|---|---|
| **Docker**（默认） | 标准化容器隔离 |
| **SSH** | 远程执行 |
| **OpenShell** | 轻量级沙箱 |

通过 `sandbox.mode: "non-main"` 配置，将为非主会话提供执行环境隔离，防止恶意操作影响宿主机。

---

## 三、消息平台支持

OpenClaw 支持 **20+ 消息平台**，是目前覆盖最广的开源 AI 助手：

| 平台 | 支持状态 |
|---|---|
| WhatsApp | ✅ |
| Telegram | ✅ |
| Slack | ✅ |
| Discord | ✅ |
| Google Chat | ✅ |
| Signal | ✅ |
| iMessage | ✅ |
| BlueBubbles | ✅ |
| IRC | ✅ |
| Microsoft Teams | ✅ |
| Matrix | ✅ |
| Feishu（飞书） | ✅ |
| LINE | ✅ |
| Mattermost | ✅ |
| Nextcloud Talk | ✅ |
| Nostr | ✅ |
| Synology Chat | ✅ |
| Tlon | ✅ |
| Twitch | ✅ |
| Zalo | ✅ |
| Zalo Personal | ✅ |
| WeChat（微信） | ✅ |
| QQ | ✅ |
| WebChat | ✅ |

> 所有平台共享同一个 Gateway 后端，跨平台对话无缝衔接。

---

## 四、快速安装

### 一键安装（推荐）

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

`onboard` 命令会引导你完成：**配置 LLM 模型 → 设置消息渠道 → 创建工作区 → 安装技能**，并将 Gateway 注册为系统服务（macOS 用 launchd，Linux 用 systemd）。

### 从源码开发

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
pnpm openclaw setup
pnpm gateway:watch
```

### 系统要求

- **Node.js**：v22.14+ 或 v24（推荐）
- **包管理**：pnpm（开发）/ npm/pnpm/bun（CLI 安装）
- **操作系统**：macOS、Linux、Windows（WSL2）
- **新用户快速开始**：推荐 `stable` 发布通道
- **开发者/勇者**：可使用 `dev` 通道跟踪最新开发

### 发布通道

```bash
# 稳定版（推荐）
npm install -g openclaw@latest

# Beta 版
npm install -g openclaw@beta

# Dev 版（跟踪 main 分支）
npm install -g openclaw@dev

# 切换通道
openclaw update --channel stable
openclaw update --channel beta
openclaw update --channel dev
```

---

## 五、核心命令

### 启动与管理

```bash
# 启动 Gateway
openclaw gateway --port 18789 --verbose

# 发送消息
openclaw message send --target +1234567890 --message "你好，OpenClaw"

# 直接交互
openclaw agent --message "帮我做个检查清单" --thinking high

# 诊断配置
openclaw doctor
```

### 聊天内命令

| 命令 | 功能 |
|---|---|
| `/status` | 查看当前状态 |
| `/new` 或 `/reset` | 新建对话 |
| `/think` | 切换思考模式 |
| `/verbose` | 切换详细输出 |
| `/trace` | 查看调用追踪 |
| `/usage` | 查看用量统计 |

---

## 六、配置与自定义

### 最小配置

```json
{
  "agents": {
    "model": "<provider>/<model-id>"
  }
}
```

配置文件位于 `~/.openclaw/openclaw.json`。

### 工作区结构

- **工作区根目录**：`~/.openclaw/workspace`（默认）
- **注入提示文件**：`AGENTS.md`、`SOUL.md`、`TOOLS.md`
- **技能目录**：`~/.openclaw/workspace/skills/<技能名>/SKILL.md`

### 安全模型

OpenClaw 的默认隐私策略非常严格：

- **默认 DM 策略**：需要配对（Pairing）——陌生发送者会收到配对码，验证通过后才能与机器人对话
- **审批用户**：`openclaw pairing approve <channel> <code>`
- **开放 DM**：将 `dmPolicy` 设为 `"open"`，并在 allowlist 中加入 `"*"`
- `openclaw doctor` 会自动检测并警告危险的 DM 配置

---

## 七、核心功能深度解析

### 7.1 Live Canvas

Live Canvas 是 OpenClaw 的**可视化工作空间**，完全由 Agent 驱动：

- 在浏览器中运行的动态画布
- Agent 可以在上面实时生成、修改内容
- 支持 A2UI（Agent-to-UI）协议
- 适合任务看板、数据分析可视化、实时协作等场景

### 7.2 语音唤醒与对话

OpenClaw 拥有完整的语音交互能力：

| 平台 | 能力 |
|---|---|
| **macOS** | 语音唤醒词 |
| **iOS** | 语音唤醒 + 对话 |
| **Android** | 连续语音对话 |

结合 ElevenLabs 和系统 TTS 回退，提供流畅的语音交互体验。

### 7.3 Companion App

OpenClaw 提供 iOS 和 Android 的配套节点应用：

- 通过 **WebSocket** 与你的 OpenClaw Gateway 配对
- 从手机端直接与 Agent 对话
- 支持语音输入
- 所有数据通过你自己的 Gateway，不经过第三方

### 7.4 技能系统

OpenClaw 的技能系统基于 ClawHub 开放标准：

- 技能市场：[clawhub.ai](https://clawhub.ai)
- 技能存储路径：`~/.openclaw/workspace/skills/<skill>/SKILL.md`
- 支持三种类型：内置（Bundled）、托管（Managed）、工作区（Workspace）

### 7.5 Cron 定时任务

与 Hermes Agent 类似，OpenClaw 内置了定时任务系统：

- 支持标准的 cron 表达式
- 任务结果可以投递到任意已连接的消息平台
- 适合日报、监控告警、定时备份等场景

---

## 八、技术栈

| 层级 | 技术选型 |
|---|---|
| **运行时** | Node.js v22.14+ |
| **主要语言** | TypeScript |
| **包管理** | pnpm（开发）、npm/pnpm/bun（安装） |
| **构建** | tsdown |
| **测试** | vitest |
| **代码检查** | oxlint |
| **容器** | Docker、Podman |
| **云部署** | Fly.io、Render |
| **CI** | GitHub Actions |
| **质量** | Semgrep、ShellCheck、markdownlint |
| **UI** | A2UI |
| **沙箱** | Docker / SSH / OpenShell |

---

## 九、与同类项目对比

| 特性 | OpenClaw | Hermes Agent | Claude Code |
|---|---|---|---|
| **自托管** | ✅ 完全本地 | ✅ 支持 | ❌ |
| **消息平台** | 20+ 平台 | 6 个平台 | ❌ 仅 CLI |
| **多 Agent** | ✅ 多 Agent 路由 | ✅ 子代理并行 | ❌ |
| **语音** | ✅ 唤醒 + 对话 | ❌ | ❌ |
| **Live Canvas** | ✅ | ❌ | ❌ |
| **Companion App** | ✅ iOS + Android | ❌ | ✅ 桌面版 |
| **Stars** | 367K | 129K | — |
| **主要语言** | TypeScript | Python | TypeScript |
| **协议** | MIT | MIT | 闭源 |
| **安装** | npm + onboard | pip/uv + install.sh | npx/npm |

---

## 十、适用场景

### 个人使用

- **统一消息助手**：在微信、Telegram、Discord 等所有平台上使用同一个 AI 助手
- **隐私优先**：数据完全自托管，不经过任何第三方服务
- **多设备同步**：电脑、手机、平板均可通过 Gateway 连接

### 开发与集成

- **定时任务**：日报生成、代码审查提醒、系统监控
- **技能扩展**：通过 ClawHub 安装或自己编写技能
- **Workflow 自动化**：结合工具系统实现复杂工作流

### 团队协作

- **群聊 AI 助手**：在 Slack/Discord/Teams 群聊中部署
- **沙箱隔离**：群聊会话运行在 Docker 沙箱中，保障安全
- **多 Agent 分工**：不同渠道配置不同 Agent，各司其职

---

## 十一、总结

OpenClaw 是目前 GitHub 上最受欢迎的开源 AI 项目之一（367K Stars），它的核心定位是**自托管的个人 AI 助手**，让你在既有的消息平台上与 AI 交流，同时完全掌控自己的数据。

与同类项目相比，OpenClaw 最大的差异化优势在于：

1. **极致的平台覆盖**：20+ 消息平台，包括微信和 QQ
2. **本地优先 + 自托管**：数据不出你的设备
3. **多 Agent 架构**：不同场景用不同 Agent
4. **语音 + Canvas + App**：完整的多模态交互能力

> 技术栈：TypeScript / Node.js | 协议：MIT | Stars：367K
>
> 官网：[openclaw.ai](https://openclaw.ai) | 文档：[docs.openclaw.ai](https://docs.openclaw.ai) | 技能市场：[clawhub.ai](https://clawhub.ai)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)