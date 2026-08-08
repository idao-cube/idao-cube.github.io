## 一个端口，四十个模型，零停机

如果你在过去一年里用过 Claude Code、Codex CLI 或 Cursor 写代码，你一定遇到过这个问题：订阅配额用完了，rate limit 卡住了，或者某个模型今天就是不在状态。

于是你打开浏览器，登录另一个服务商的后台，复制 API key，改配置，重新开始。再遇到，再换。周而复始。

9Router 想解决的事很简单：**在你和所有 AI 提供商之间，放一个智能路由器。**

它运行在你本地，暴露一个 `http://localhost:20128/v1` 的 OpenAI 兼容端点。所有 AI 编码工具都把请求发到这个地址，9Router 自动决定发给谁——先用你的订阅配额，配额用完了切到便宜的按量付费 API，再不够就转去免费提供商。这一切在毫秒级完成，你的工具甚至不知道背后换过人。

这个项目在 GitHub 上有 8,800 颗星，572 次提交，npm 周下载量可观，被用来连接 40+ 提供商和 100+ 模型。

但 9Router 不止是一个简单的 API 代理。它的架构里有一些值得仔细看的设计。

## 双引擎架构：Next.js 与 Express 的同体共生

大多数本地代理工具都是单一的后端服务。9Router 选择了一条更复杂的路。

它跑在 **Next.js 16 + Express 5** 的双引擎架构上。Next.js 的 App Router 处理所有管理面——仪表盘页面、提供商 CRUD、OAuth 流程、用量分析。Express 嵌入在同一个进程中，处理代理面的 MITM 流量拦截和协议转换。

```
同一进程，两套路由系统：
  Next.js App Router → /api/*         → 仪表盘、管理 API
  Express           → MITM proxy      → HTTPS 拦截 + 请求转发
```

这种设计有实在的好处：管理面和数据面共享内存中的状态，不需要通过 RPC 或 HTTP 调用同步。提供商配置更新后，代理面立即生效。没有缓存一致性问题，没有分布式事务。

代价是进程内的复杂性——两套路由系统的生命周期管理、端口分配、优雅关闭都需要仔细处理。9Router 的 `src/mitm/server.js` 里有一段专职逻辑处理 Express 服务器的启动与 shutdown，与 Next.js 的生命周期挂钩。

## 572 次提交背后的演化轨迹

翻看 9Router 的 CHANGELOG，会发现它的发布节奏极其密集——从 v0.3.89 到 v0.4.31，大约一个月内发布了 40 多个版本，几乎每天一个。

早期的重点是提供商接入：GitHub Copilot、Codex、Claude Code、Gemini CLI……每个新提供商意味着一种新的 OAuth 流程、一组新的 API 格式差异、一套新的 token 刷新机制。

v0.3.98 引入的 **RTK** 是一个转折点。它不再只是做"路由"，而是开始在请求层面做优化——压缩 tool output 以节省 token。这标志着 9Router 从"被动代理"向"主动优化层"的转变。

v0.3.96 的 **Caveman Mode** 走了更远——直接修改 system prompt，让模型用更简洁的语言输出，节省 output token。

v0.4.11 后的版本开始拓展到多媒体领域：Text-to-Speech、Speech-to-Text、图像生成、Web Search/Fetch combo provider。9Router 不再只做 LLM 路由，而是在成为一个**通用的 AI 能力网关**。

最近的 v0.4.31（2026-05-12，也就是今天）加入了 OIDC 仪表盘登录、Linux/arm64 Docker 镜像支持。

这种发布节奏说明两件事：一是项目的维护者有极强的迭代能力，二是社区对它的需求非常明确和紧迫。

## RTK：在请求到达 LLM 之前做减法

RTK（Runtime Token Killer）是 9Router 最引人注目的特性。它的目的是解决一个很具体但也很普遍的问题：**AI 编码工具产生的 tool output 占据了请求 token 的大头，而这些输出大部分是冗余信息**。

当你的 AI 编码助手执行了 `git diff`，返回了 200 行变化；或者执行了 `grep`，返回了 500 行匹配结果——这些内容直接塞进 LLM 的上下文窗口，消耗大量 token，但对回答质量的实际贡献有限。

RTK 在请求发出之前拦截这些 `tool_result`，根据内容特征选择最优压缩策略：

| 过滤器             | 匹配场景        | 压缩方式                       |
| ------------------ | --------------- | ------------------------------ |
| `git-diff`       | git diff 输出   | 保留文件头和变更行，省略上下文 |
| `git-status`     | git status 输出 | 折叠未变更文件                 |
| `grep`           | 搜索结果        | 去重，截断长行                 |
| `find`           | 文件查找        | 省略权限和元数据               |
| `ls` / `tree`  | 目录列表        | 省略时间戳和权限               |
| `dedup-log`      | 日志输出        | 移除重复行                     |
| `smart-truncate` | 通用长文本      | 智能截断，保留首尾             |
| `read-numbered`  | 行号标注文本    | 折叠连续行号                   |

RTK 的工作方式是：**peek 每个 tool_result 的前 1KB，根据格式特征自动匹配过滤器。** 如果某个过滤器执行失败或反而使输出变大，RTK 会静默保留原始文本。不出错是最高优先级。

效果方面，官方数据是 20-40% 的输入 token 节省。考虑到 AI 编码工作流中 tool output 可以占请求 token 的 50-70%，这个节省是相当可观的。

## Caveman Mode：反其道而行的输出优化

如果说 RTK 是在输入端做减法，Caveman Mode 就是在输出端做减法。

它的原理简单到几乎荒谬：**在 system prompt 里注入一段"穴居人说话"的指令，让模型用尽可能短的文字回答问题。**

这听起来像个玩笑，但它背后有一个严肃的观察：AI 编码工具的输出中包含大量"gratuitous verbosity"——客套话、解释性段落、替代方案的讨论。这些内容对编码任务没有价值，只是白白消耗 completion token。

Caveman Mode 声称可以节省高达 65% 的输出 token。这个数字是否准确取决于任务类型，但方向是对的——对于"重构这个函数""修复这个 bug"这类具象任务，你需要的不是 500 字的解释，而是一个 diff 和一句说明。

Caveman Mode 在 9Router 中有一个可调节的压缩级别，从温和到激进。用户可以在 Dashboard 中启用或禁用它，也可以设置只对特定 combo 生效。

## 三级 Fallback 引擎：从订阅到免费的无缝切换

这是 9Router 最核心的价值主张。它的 fallback 系统有三层嵌套：

**第一层：提供商内的多账户轮询**

```
同一个提供商（如 Claude Code），配置 3 个订阅账号
→ 请求自动分配，一个账号配额用完 → 自动切到下一个
→ 所有账号都到 limit → 尝试 token 刷新
→ 刷新失败 → 降级到提供商级别 fallback
```

**第二层：Combo 内的多模型链**

```
Combo "my-coding-stack":
  1. cc/claude-opus-4-7        (你的订阅，最优质量)
  2. glm/glm-5.1               (便宜按量，$0.6/1M)
  3. kr/claude-sonnet-4.5      (免费，Kiro 提供)

模型级别 fallback：当前模型报错/配额用完 → 自动切到链中下一个
```

**第三层：全局 fallback 策略**

```
订阅 → 便宜按量 → 免费
跨提供商降级，保证永不停止
```

这个 fallback 逻辑实现在 `open-sse/services/accountFallback.js` 中。它根据 HTTP 状态码和错误信息做启发式判断——哪类错误应该触发 fallback，哪类错误应该直接返回。

具体的决策树：

```
请求失败 → 判断错误类型
  ├─ 401/403 → 尝试 token 刷新 → 重试
  ├─ 429/rate limit → 标记账户进入 cooldown → 切到同提供商下一个账户
  ├─ 5xx/网络错误 → 直接尝试下一个账户或 combo 模型
  └─ 其他 → 返回错误给客户端
```

对于 combo 的 fallback，9Router 还有一个 **sticky round-robin** 策略——新请求不需要每次都从第一个模型开始，而是从上次成功的位置继续轮询，避免"好模型永远在排队"的问题。

## 格式翻译引擎：让 GPT 的请求被 Claude 理解

AI 提供商之间的 API 格式差异远不止"字段名不同"。OpenAI 用 `messages` 数组和 `role` 字段，Anthropic 用 `content` 数组和 `role` 前缀，Google Gemini 有自己的 `contents` 和 `parts` 结构。Tool calling 的 schema、streaming 的 chunk 格式、error 响应——每个提供商都有一套自己的约定。

9Router 的翻译引擎在 `open-sse/translator/` 目录下，分为三个层次：

```
检测源格式：
  openai / openai-responses / claude / gemini
  → 根据请求 payload 结构自动识别

选择目标格式：
  openai-chat / openai-responses / claude / gemini / gemini-cli / kiro / cursor / antigravity
  → 根据提供商配置决定

双向翻译：
  request/*   → 将传入请求翻译为提供商格式
  response/*  → 将提供商响应翻译回客户端格式
```

最复杂的部分是 tool calling 的翻译。不同的提供商对 tool definitions 的 schema 描述方式不同，tool call ID 的格式不同，tool result 的嵌入方式也不同。9Router 在处理这些差异时，不只是做简单的字段映射，而是需要重新构建完整的 tool call 生命周期。

例如，当一个 OpenAI-format 的客户端发送 tool definition 给一个 Claude 后端时，翻译引擎需要：

1. 将 OpenAI 的 `functions` 或 `tools` 参数转换为 Anthropic 的 `tools` 格式
2. 适配 `tool_choice` 的语义差异（OpenAI 用 `auto`/`none`/`{type:"function"}`，Claude 用 `auto`/`any`/`{type:"tool"}`）
3. 返回时将 Claude 的 `tool_use` content block 翻译为 OpenAI 的 `tool_calls` 格式

## MITM 代理：当 CLI 工具不知道 9Router 的存在

某些 AI 编码工具（如 Cursor、Copilot）不提供自定义端点的选项——它们硬编码了自己的后端地址。9Router 用 MITM（中间人）代理来解决这个问题。

`src/mitm/` 目录下的代码实现了一个完整的 HTTPS 中间人代理：

- **证书生成**：`src/mitm/cert/` 使用 `node-forge` 和 `selfsigned` 动态生成 CA 证书，并安装到系统信任存储
- **DNS 劫持**：`src/mitm/dns/` 将目标域名（如 `api.anthropic.com`）解析到本地
- **请求拦截**：`src/mitm/handlers/` 包含针对不同提供商的特化处理器
- **流量转发**：HTTPS 连接被终止在本地，解密后检查请求内容，然后通过 `http-proxy-middleware` 转发

MITM 模式在跨平台上有不少细节要处理。Windows 上需要管理员权限来安装证书和修改 hosts 文件（`src/mitm/winElevated.js`）。Linux 上需要根据发行版（Debian/Arch/Fedora/openSUSE）将证书注入不同的 NSS 数据库。macOS 上需要 `security add-trusted-cert`。

9Router 还集成了 **Tailscale Funnel**，允许 MITM 代理通过 Tailscale 的网络暴露到公网，实现远程访问。

## SSE 引擎：流式传输的编排核心

AI 提供商的核心交互模式是 SSE（Server-Sent Events），9Router 的 SSE 引擎是其性能的关键。

核心代码在 `src/sse/` 和 `open-sse/` 两个目录：

```
src/sse/handlers/chat.js         → 入口：解析请求、处理 combo、选择账户
open-sse/handlers/chatCore.js    → 核心：翻译、执行、retry、流处理
open-sse/executors/*             → 提供商特化执行器
open-sse/services/accountFallback.js → 账户级 fallback 逻辑
open-sse/utils/stream.js         → 流转换工具
open-sse/utils/streamHandler.js  → 流处理器
```

对于 streaming 请求，9Router 维护了一个**双向流转换管线**：

```
客户端 SSE 流 → 9Router 格式检测 → 翻译为提供商格式 → 发送到提供商
提供商的 SSE 流 → 同步翻译回客户端格式 → 发送到客户端
```

翻译不是等全部完成再做，而是**逐 chunk 流式转换**。这意味着第一个 token 的延迟只比直连多一次本地进程间通信的时间（通常 <1ms）。

`open-sse/utils/stream.js` 中包含了 `[DONE]` 信号处理、流中断恢复、以及 usage 信息从 stream 尾部提取的逻辑。

## 数据模型：从 JSON 文件到 SQLite

9Router 的数据层经历了一次迁移：从最初的 lowdb（JSON 文件数据库），到现在的 SQLite 三层适配：

```
better-sqlite3 (原生最快) → node:sqlite (Node ≥22.5 内置) → sql.js (WASM 回退)
```

`src/lib/localDb.js` 管理所有持久化状态：

| 实体                | 说明                       |
| ------------------- | -------------------------- |
| providerConnections | OAuth/API key 连接配置     |
| providerNodes       | 自定义兼容节点（自建端点） |
| modelAliases        | 模型别名映射               |
| combos              | 多模型 fallback 链         |
| apiKeys             | 本地 API key 鉴权          |
| settings            | 全局设置                   |
| pricing             | 价格覆盖配置               |

用量数据单独存储在 `src/lib/usageDb.js` 中，包括 `usage.json`（聚合数据）和 `log.txt`（请求日志）。

值得注意的是，`usageDb` 目前仍然绑定在 `~/.9router/usage.json`，没有跟随 `DATA_DIR` 环境变量——这是一个已知的架构遗留问题。

## 从 LLM 网关到通用 AI 能力网关

翻看最近的 CHANGELOG，能清楚地看到 9Router 的范围在扩大：

- **Speech-to-Text**：支持 OpenAI、Gemini、Groq、Deepgram、AssemblyAI、HuggingFace 等多个 STT 提供商
- **Text-to-Speech**：30+ 预制声音，支持 Coqui、Inworld、Tortoise 等 TTS 引擎
- **图像生成**：Cloudflare Workers AI、Fal.ai 等图像生成提供商
- **Web Search/Fetch**：将多个搜索提供商组合成一个虚拟提供商，支持 combo fallback
- **MCP 桥接**：将本地的 stdio MCP 插件通过 SSE 暴露到 `/api/mcp/[plugin]/sse`
- **Skills 系统**：管理和执行自定义 AI 技能

这意味着 9Router 正在从一个"LLM 代理路由"演进为**"通用 AI 能力编排层"**。它的核心价值不再是"帮你省钱"，而是"提供一个统一的接口来管理你所有的 AI 能力"。

## 四种典型使用场景

**场景一：最大化订阅价值**

你有一份 Claude Pro 订阅（$20/月），但经常用不完配额。设置 combo：

```
cc/claude-opus-4-7   → 先用订阅
glm/glm-5.1          → 配额用完后切到低价按量（$0.6/1M）
kr/claude-sonnet-4.5 → 免费保底
```

月均支出：$20 + ~$5 backup = $25，但零停机。

**场景二：零成本开发**

不需要任何订阅，直接接免费提供商：

```
kr/claude-sonnet-4.5 → Kiro 免费 Claude
kr/glm-5             → Kiro 免费 GLM
oc/<auto>            → OpenCode Free 无认证
```

月支出：$0。适合学习、个人项目、原型开发。

**场景三：24/7 不间断编码**

```

cc/claude-opus-4-7        → 最优质量
cx/gpt-5.5                → 第二订阅
glm/glm-5.1               → 便宜按量（日重置）
minimax/MiniMax-M2.7      → 最便宜（5h 重置）
kr/claude-sonnet-4.5      → 免费无限
```

五层 fallback，理论上永远不会有"无模型可用"的时刻。

**场景四：团队统一接入**

通过 OIDC 集成公司 SSO，团队成员使用统一入口，管理员在 Dashboard 上配置提供商策略和用量上限。

## 技术上的取舍与不足

9Router 在架构上也有一些值得讨论的取舍。

最明显的问题是**单进程模型**。Next.js 和 Express 在同一个进程运行，虽然避免了分布式一致性问题，但也意味着任何一个子系统的崩溃都会拖垮整个服务。如果 MITM 代理因为证书问题挂掉，仪表盘也会跟着不可用。

**用量数据未跟随 DATA_DIR**。这是一个小的但令人恼火的架构不一致。用户设置了 `DATA_DIR` 却发现日志还在 `~/.9router` 下，需要手动配置或创建软链接。

**请求日志的安全性**。当 `ENABLE_REQUEST_LOGS=true` 时，完整的请求头和请求体会写入日志目录。如果这个目录被不当共享或备份，可能导致 API key 泄露。项目文档中标注了这一点，但默认不应该开启。

**SQLite 适配层的性能**。三层数据库驱动的 fallback 设计很稳妥，但 `better-sqlite3 → node:sqlite → sql.js` 的性能差异是显著的。在某些部署环境（如 Docker 内 Node 未编译原生模块），自动降级到 sql.js 后写入性能可能下降数倍。

但这些不是致命缺陷。对于一个以天为单位迭代的开源项目来说，架构的健康度取决于它能否快速响应社区需求。9Router 在这方面做得很好。

## 为什么 8,800 个开发者选择在自己电脑上跑一个代理

AI 编码工具在过去两年经历了一场寒武纪大爆发。Claude Code、Codex CLI、Cursor、Cline、Continue、OpenClaw、Kilo Code——每个工具都在争夺开发者的终端。但它们共享一个核心事实：都依赖 LLM 后端的 API。

开发者的需求很朴素：**我想用最好的模型，不想管配额管理、API 格式转换、多账号轮询这些基础设施问题。** 9Router 恰好提供了这层抽象。

它不是唯一在做这件事的项目——OpenRouter、Portkey 等都是类似的思路。但 9Router 有两个独特之处：

1. **完全本地运行**。数据不经过第三方，适合有安全合规要求的场景。
2. **面向 CLI 工具链设计**。它的接口和功能设计围绕"AI 编码助手怎么用"而不是"通用 API 代理应该有什么"。

在一个月发布 40 多个版本的激进节奏下，9Router 积累了 305 个 issue 和 126 个 PR——社区在推动它快速进化。从 v0.3.89 到 v0.4.31，它从一个简单的 API 代理成长为一个涵盖语音、图像、搜索、MCP 的 AI 能力网关。

它可能不是架构最优雅的项目，但它是开发者最需要的那个。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)