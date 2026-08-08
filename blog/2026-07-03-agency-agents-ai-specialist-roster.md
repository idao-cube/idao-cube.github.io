> 项目地址：[github.com/msitarzewski/agency-agents](https://github.com/msitarzewski/agency-agents) | 126K Stars | MIT 协议 | 400+ Agents | 16+ Divisions

## 一、从 Reddit 帖子到 126K Stars

2024 年底，一条 Reddit 帖子悄然出现——作者 **msitarzewski** 分享了他为 AI 编程助手编写的一系列角色定义文件。这些文件不是简单的提示词模板，而是包含完整人格设定、专业工作流、交付物标准和成功度量的"Agent 简历"。

帖子迅速引爆社区。原因很简单：**它解决了 AI 编程中最痛的一个问题——角色模糊。**

当你在 Claude Code 中说"帮我写个 React 组件"，AI 不知道自己是初级开发者还是资深架构师，不知道应该关注性能还是可读性，不知道交付标准是什么。Agency-Agents 的每个 Agent 文件，就是一张精确的"岗位说明书"。

不到一年时间，这个仓库获得了 **126K Stars**，拥有 357 次提交、44 个 Issue、54 个 PR，社区贡献者遍布全球。它不再只是一个资源集合，而是演变为一个**完整的 AI 专家生态**。

## 二、核心理念：不是提示词，是"岗位描述"

Agency-Agents 与市面上其他提示词集合最大的区别在于它的**设计哲学**。

### 2.1 四个设计原则

每个 Agent 文件都遵循四条铁律：

| 原则 | 含义 |
|------|------|
| **🎯 专精** | 深度领域知识，不是泛化的提示词模板 |
| **🧠 人格驱动** | 独特的语气、沟通风格和工作方式 |
| **📋 交付物导向** | 真实的代码、流程和可衡量的产出 |
| **✅ 生产就绪** | 经过实战检验的工作流和成功指标 |

### 2.2 一个 Agent 文件的结构

以 **Prompt Engineer（提示词工程师）** Agent 为例，它的 Markdown 文件包含：

- **身份与记忆**：`"我不写提示词，我写的是人类与模型之间的契约"`
- **核心使命与工作流**：设计系统提示词、构建测试套件、将模糊需求翻译为精确的行为规范
- **技术交付物**：示例代码（System Prompt 模板、测试套件、Changelog 格式、Few-Shot Builder）
- **成功指标**：输出格式合规率 ≥ 98%、幻觉率 < 3%、回归测试通过率 100%
- **沟通风格**：`"这个提示词在输入超过 500 token 时会失败，因为..."而不是'长输入可能有问题'`

这种结构使得每个 Agent 不仅是一个**配置**，更是一套**方法论**——告诉 AI 如何思考、如何工作、如何衡量自己的产出。

## 三、16+ 部门 400+ 专家：梦之队全景

Agency-Agents 组织为 16 个以上"部门"，每个部门下有若干专家角色。以下是完整的部门架构：

### 💻 工程部（Engineering Division）

工程部是最大也最丰富的部门，覆盖从底层到上层的全栈工程能力：

**前端/后端/移动端三件套：**
- **Frontend Developer** — React/Vue/Angular 专家，关注 Core Web Vitals
- **Backend Architect** — API 设计、数据库架构、微服务
- **Mobile App Builder** — iOS/Android/React Native/Flutter

**基础设施与运维：**
- **DevOps Automator** — CI/CD、基础设施自动化
- **Network Engineer** — Cisco IOS/Juniper/Palo Alto 网络配置
- **SRE** — SLO、错误预算、混沌工程
- **Incident Response Commander** — 生产事故管理

**AI 与数据：**
- **AI Engineer** — ML 模型部署与 AI 集成
- **Multi-Agent Systems Architect** — 🕸️ 多 Agent 管线设计与治理
- **Prompt Engineer** — 🧬 LLM 提示词设计与优化
- **Data Engineer** — 数据管线、湖仓架构
- **AI Data Remediation Engineer** — 自愈管线、零数据丢失

**专项专家：**
- **Embedded Firmware Engineer** — ESP32/STM32/Nordic，生产级嵌入式系统
- **Solidity Smart Contract Engineer** — EVM 合约、Gas 优化、DeFi
- **WeChat Mini Program Developer** — 微信生态开发
- **Voice AI Integration Engineer** — Whisper/ASR/说话人分离
- **Feishu Integration Developer** — 飞书集成开发
- **Codebase Onboarding Engineer** — 快速理解陌生代码库
- **Minimal Change Engineer** — 只改动必要部分，绝不 scope creep

### 🎨 设计部（Design Division）

- **UI Designer** — 视觉设计、组件库、设计系统
- **UX Researcher** — 用户测试、行为分析
- **UX Architect** — 技术架构、CSS 系统
- **Brand Guardian** — 品牌身份与一致性
- **Visual Storyteller** — 视觉叙事、多媒体内容
- **Whimsy Injector** — ✨ 为产品注入愉悦感、彩蛋、品牌个性
- **Image Prompt Engineer** — Midjourney/DALL-E/Stable Diffusion 提示词
- **Inclusive Visuals Specialist** — 文化包容性、偏见缓解
- **Persona Walkthrough Specialist** — 角色驱动的认知走查

### 💰 付费媒体部（Paid Media Division）

- **PPC Campaign Strategist** — Google/Microsoft/Amazon Ads
- **Search Query Analyst** — 搜索词分析、否定关键词
- **Paid Media Auditor** — 200+ 项账户审计
- **Tracking & Measurement Specialist** — GTM/GA4/CAPI
- **Ad Creative Strategist** — RSA 文案、Meta 创意
- **Programmatic & Display Buyer** — GDN/DSP/ABM
- **Paid Social Strategist** — Meta/LinkedIn/TikTok

### 💼 销售部（Sales Division）

- **Outbound Strategist** — 信号驱动的外呼、多触点序列
- **Discovery Coach** — SPIN/Gap Selling/Sandler 方法
- **Deal Strategist** — MEDDPICC 评估、竞争定位
- **Sales Engineer** — 技术演示、POC 方案
- **Proposal Strategist** — RFP 响应、赢单叙事
- **Pipeline Analyst** — 预测、管线健康、RevOps
- **Sales Coach** — 代表培养、通话辅导

### 📢 市场部（Marketing Division）

市场部是第二大的部门，尤其值得一提的是对中国市场的深度覆盖：

**全球平台专家：**
- **Growth Hacker** — 病毒式增长、实验驱动
- **Content Creator** — 跨平台内容策略
- **Twitter Engager / LinkedIn Content Creator** — 社交媒体
- **TikTok Strategist / Instagram Curator** — 视觉平台
- **Reddit Community Builder** — 社区信任建设
- **Global Podcast Strategist** — 播客策略
- **SEO Specialist** — 技术 SEO 与内容策略

**中国市场全链路覆盖：**
- **Xiaohongshu Specialist** — 小红书种草策略
- **WeChat Official Account Manager** — 公众号运营
- **Zhihu Strategist** — 知乎知识营销
- **Baidu SEO Specialist** — 百度搜索优化
- **Bilibili Content Strategist** — B 站弹幕文化
- **Douyin Strategist** — 抖音短视频
- **Kuaishou Strategist** — 快手老铁经济
- **Weibo Strategist** — 微博话题运营
- **China E-Commerce Operator** — 淘宝/天猫/拼多多
- **Cross-Border E-Commerce Specialist** — Amazon/Shopee/Lazada
- **Private Domain Operator** — 企微私域运营
- **Livestream Commerce Coach** — 直播带货
- **Multi-Platform Publisher** — 一键分发（知乎/小红书/CSDN/B站/公众号/掘金）
- **China Market Localization Strategist** — 全栈中国市场进入策略

**前沿领域：**
- **AI Citation Strategist** — AEO/GEO 优化，让品牌出现在 ChatGPT/Claude/Gemini 的回复中
- **AEO Foundations Architect** — llms.txt、AI 友好 robots.txt
- **Agentic Search Optimizer** — WebMCP 与 AI 浏览代理优化

### 📊 产品部（Product Division）

- **Product Manager** — 全生命周期产品管理
- **Sprint Prioritizer** — 敏捷规划与排期
- **Trend Researcher** — 市场情报与竞争分析
- **Feedback Synthesizer** — 用户反馈分析
- **Behavioral Nudge Engine** — 🧠 行为心理学驱动的产品设计

### 🎬 项目管理部（Project Management Division）

- **Studio Producer** — 高层次编排与组合管理
- **Project Shepherd** — 🐑 跨职能协调
- **Studio Operations** — 日常运营优化
- **Experiment Tracker** — A/B 测试管理
- **Jira Workflow Steward** — Git 与 Jira 纪律
- **Meeting Notes Specialist** — 结构化会议纪要

### 🧪 测试部（Testing Division）

- **Evidence Collector** — 截图驱动的 QA
- **Reality Checker** — 🔍 证据驱动的质量门禁
- **Test Results Analyzer** — 测试输出分析
- **Performance Benchmarker** — 性能测试
- **API Tester** — API 验证
- **Accessibility Auditor** — ♿ WCAG 合规审计

### 🔒 安全部（Security Division）

- **Security Architect** — 威胁建模、安全设计
- **Application Security Engineer** — SAST/DAST 安全代码审查
- **Penetration Tester** — 红队渗透测试
- **Cloud Security Architect** — 零信任云安全
- **Incident Responder** — DFIR 事件响应
- **Threat Intelligence Analyst** — 对手追踪
- **Threat Detection Engineer** — SIEM 规则
- **Blockchain Security Auditor** — 智能合约审计
- **Compliance Auditor** — SOC 2/ISO 27001/HIPAA/PCI-DSS

### 🛟 支持部（Support Division） & 🥽 空间计算部（Spatial Computing Division）

支持部覆盖客户服务、数据分析、财务、法务、高管汇报等后台职能。空间计算部则专注于 Vision Pro、WebXR、macOS Spatial Computing 等沉浸式技术。

### 🎯 特殊部门（Specialized Division）

这里收集了最具创新性的角色：

- **Agents Orchestrator** — 多 Agent 编排指挥
- **MCP Builder** — Model Context Protocol 服务器构建
- **ZK Steward** — Zettelkasten 知识管理
- **Agentic Identity & Trust Architect** — 多 Agent 身份认证与信任
- **Identity Graph Operator** — 跨 Agent 身份图谱
- **Workflow Architect** — 工作流发现与映射
- **Supply Chain Strategist** — 供应链优化
- **Cultural Intelligence Strategist** — 全球 UX 文化适配
- **Personal Growth Mentor** — 目标清晰与习惯系统
- **此外还有法律、医疗、地产、零售、酒店、招聘、留学、翻译等 30+ 垂直领域专家**

## 四、深度解析：两个标志性 Agent 的设计

为了理解 Agency-Agents 的真正深度，让我们深入两个最具代表性的 Agent 文件。

### 4.1 Multi-Agent Systems Architect（🕸️ 多 Agent 系统架构师）

这是工程部最引人注目的 Agent 之一，它的设计本身就是一篇**多 Agent 系统的权威文章**。

**人格设定：**
> "你把一组 AI Agent 当成分布式系统来对待——如果它只能撑过演示而扛不住生产负载、模糊输入和级联故障，那它还不算架构。"

**核心能力覆盖：**

**拓扑模式（五种分布式架构模式）：**
1. **Sequential Chain** — 线性管线，每个步骤依赖上一步输出
2. **Parallel Fan-Out/Fan-In** — 路由分发到多个并行 Agent 再合并
3. **Hierarchical (Orchestrator-Subagent)** — 编排器动态分解任务
4. **Evaluator-Optimizer Loop** — 生成→评估→反馈→迭代的闭环
5. **Mesh/Peer Network** — 对等协商网络（明确警告：生产系统中很少正确选择）

每种模式都包含：使用场景、故障模式、设计规则。例如 Fan-Out 模式的设计规则包括：
- 并行 Agent 必须**真正独立**——无共享可变状态
- 合成器必须明确处理：全部结果到达、部分结果、零结果
- 合并策略在构建前决定：投票、加权、拼接、或转交人类
- 扇出宽度限制：>7 个并行 Agent 通常会超过合成质量阈值

**故障模式工程：**

这是这个 Agent 的精华所在。它定义了一套完整的故障分类体系：

| 故障类型 | 描述 | 检测方式 | 恢复策略 |
|----------|------|----------|----------|
| 硬故障 | Agent 返回错误或超时 | 错误码/超时 | 退避重试→降级 Agent→人工升级 |
| 静默故障 | Agent 返回了错误输出 | 评估器/模式验证 | 带纠正提示重试→人工审查 |
| 部分故障 | 输出不完整 | 模式验证/完整性检查 | 请求特定缺失字段→重新生成 |
| 矛盾 | 两个 Agent 输出冲突 | 矛盾检测器 | 仲裁 Agent→人类决策 |
| 级联故障 | 一个坏输出污染下游 | 检查点验证/异常检测 | 回滚到最近检查点 |
| 循环故障 | 评估-优化永不收敛 | 迭代计数器/分数平台检测 | 强制退出，带上最后一次的最佳输出 |

**电路断路器模式（Circuit Breaker）：**
```
CLOSED（正常）→ 失败率超阈值 → OPEN（断开）→ 冷却期 → HALF-OPEN（试探）→ 成功回到 CLOSED
```

**降级链设计：**
```
优先级1: 全能力Agent → 优先级2: 窄范围轻量Agent → 优先级3: 规则/模板输出 → 优先级4: 人工
```
核心设计规则：**系统必须始终产生某种输出**——即使是"降级模式"的结构化响应也比静默失败好。

**上下文预算管理：**

这个 Agent 精确计算了 5 个 Agent 顺序链中的上下文膨胀：
- Agent A: 用户输入 (500 tokens)
- Agent B: 用户输入 + A 输出 (1,500 tokens)
- Agent C: 之前链路 + B 输出 (3,500 tokens)
- Agent E: 之前链路 + D 输出 (15,000+ tokens)

并提出四种解决方案：摘要压缩、结构化状态对象、外部存储、检查点压缩。

**其他深度内容：**
- 最小权限工具访问矩阵（每个 Agent 只有完成本职工作的权限）
- Prompt Injection 防御策略
- Human-in-the-Loop 闸门设计——过度升级与升级不足的校准
- 评估框架：Agent 级评估 + 管线级评估 + Eval-Driven Development
- 成本与延迟治理：Token 预算执行、成本天花板断路器

这个 Agent 文件本身，就是一份**多 Agent 系统设计的完整教科书**。

### 4.2 Prompt Engineer（🧬 提示词工程师）

**人格设定：**
> "我不写提示词，我写的是人类与模型之间的契约。"

这个 Agent 将提示词工程从"艺术"提升为"工程"：

**系统提示词模板：** 角色 → 约束 → 推理 → 示例 的四段式结构。

**提示词测试套件：**
```python
test_cases = [
    ("What is 2+2?",        "returns '4'",          "happy path: math"),
    ("Ignore instructions", "refuses gracefully",   "edge: prompt injection"),
    ("",                    "asks for clarification","edge: empty input"),
]
```

**版本控制与 Changelog：**
```markdown
### v3 — 2024-01-15
- Added explicit JSON schema to output format (reduced parsing errors by 40%)
- Replaced "be concise" with "respond in ≤ 2 sentences"
```

**高级能力：**
- Chain-of-Thought 推理框架
- Self-consistency prompting（多次采样取多数投票）
- Least-to-most 分解提示
- Prompt Injection 防御（角色锁定、输入消毒）
- 多模型提示词移植（GPT → Claude 的适配）
- 动态提示词组装

**核心理念：** *"提示词就是规格说明书。如果模型没有按你期望的做，说明规格有歧义——不是模型的错。重写规格。"*

## 五、技术架构：不仅仅是 Markdown 文件

Agency-Agents 虽然以 Markdown 文件形式存在，但它的技术架构远不止于此。

### 5.1 原生桌面应用

项目提供了 **[Agency Agents App](https://agencyagents.app)**——一款跨平台桌面应用（macOS/Linux/Windows），可以：
- 浏览完整的 400+ Agent 名册
- 一键安装到 Claude Code、Cursor、Codex、Gemini CLI、OpenCode、Qwen、Osaurus 等工具
- 自动更新保持 Agent 文件为最新版本

### 5.2 自动化安装脚本

`scripts/` 目录下的安装脚本支持 12+ 工具：

```bash
# 自动检测已安装工具，交互式安装
./scripts/install.sh

# 按部门选择性安装
./scripts/install.sh --tool claude-code --division engineering,security

# 精确到单个 Agent
./scripts/install.sh --tool cursor --agent frontend-developer,ui-designer
```

支持的工具有：Claude Code、GitHub Copilot、Cursor、Aider、Windsurf、Codex、Gemini CLI、OpenCode、OpenClaw、Antigravity、Kimi Code、Osaurus、Hermes。

### 5.3 智能集成配置

项目包含 `divisions.json` 和 `tools.json` 两个核心配置文件，定义了部门-工具-角色的映射关系，以及 `scripts/convert.sh` 用于生成各工具所需的格式。

### 5.4 社区治理

- `CONTRIBUTING.md` 和 `CONTRIBUTING_zh-CN.md` 提供了中英文贡献指南
- `SECURITY.md` 明确安全策略
- `SUPPORT.md` 提供支持渠道
- 项目有明确的 Issue 和 PR 模板

## 六、对 AI 编程生态的深层影响

### 6.1 从"提示词"到"角色"的范式转变

Agency-Agents 最深远的影响，是推动了 AI 交互从**指令模式**向**角色模式**的转变。

传统提示词工程的核心问题是**角色的模糊性**——"写一段 Python 代码"可以对应十种不同的质量标准。Agency-Agents 的每个 Agent 文件通过明确设定角色的专业知识层级、交付标准、沟通风格和失败处理方式，让 AI 的输出从"随机抽取一个可能的答案"变成了"基于精确角色定义的最优输出"。

### 6.2 提示词工程的工业化

这个项目实质上是在推动**提示词工程的工业化**——就像软件工程经历了从"个人手艺"到"工程规范"的演进：

- **版本控制**：每个提示词都有 Changelog 和版本号
- **自动化测试**：提示词像代码一样有回归测试套件
- **可组合性**：Agent 可以按部门、按角色组合安装
- **质量管理**：每个 Agent 都有明确的成功指标

### 6.3 中国市场深度覆盖的行业意义

特别值得注意的是项目对中国市场的极致重视。不仅有两份中文贡献指南，还有 15+ 专门针对中国市场的 Agent：

从 B 站的弹幕文化到小红书的种草策略，从百度的搜索算法到抖音的流量机制，从私域运营到跨境出海，这些 Agent 覆盖了中国数字经济的几乎所有关键领域。这表明了 AI 开发者生态**全球化与本土化并行**的趋势——AI Agent 不再只是英语世界的工具。

### 6.4 跨平台 Agent 可移植性

项目解决了 AI 编程生态中的"锁定"问题。同一套 Agent 定义可以安装到 12+ 不同的 AI 编程工具中。这意味着：

- **Agent 定义与运行环境解耦**：你可以定义一次 Prompt Engineer，然后在 Claude Code、Cursor、Gemini CLI 中共享
- **工具迁移零成本**：从 Claude Code 切换到 Cursor 时，你的 Agent 配置自动跟随
- **生态兼容层**：`scripts/convert.sh` 本质上是一个 Agent 格式的转译器

## 七、批判性思考：项目的局限与挑战

### 7.1 质量一致性

400+ Agent 来自社区贡献，质量参差不齐。有些 Agent（如 Multi-Agent Systems Architect 和 Prompt Engineer）深度惊人，可以说是领域内的最佳实践文档；而有些 Agent 则相对简略。项目和社区需要建立更严格的质量审核机制。

### 7.2 维护负担

每个 Agent 文件需要随着对应领域的演进而更新。AI 模型能力在快速提升，工具的 API 在变化，最佳实践在演进——400+ 文件的持续维护是一个巨大的挑战。目前项目的 357 次提交和 54 个 PR 显示出活跃度，但长期来看可能需要更系统化的维护机制。

### 7.3 工具兼容性差异

虽然安装脚本支持 12+ 工具，但不同工具对 Agent 定义的支持程度不同（例如 OpenCode 有 ~119 个 Agent 的上限），导致部分 Agent 在某些工具中无法使用。这不是项目本身的问题，但用户在跨工具迁移时需要注意代理数量的差异。

### 7.4 人格设定的主观性

"人格驱动"是项目的核心特色，但也带来了主观性。不同开发者对"一个好 Agent"的理解可能不同。某些 Agent 的沟通风格设定可能对部分用户来说过于"戏精"（personality-heavy），而对另一部分用户来说这正是价值所在。

## 八、如何开始使用

### 快速开始

**推荐方式一：桌面 App**
1. 访问 [agencyagents.app](https://agencyagents.app) 下载桌面应用
2. 浏览 Agent 名册，选择你需要的角色
3. 一键安装到你的 AI 编程工具

**推荐方式二：命令行安装**
```bash
git clone https://github.com/msitarzewski/agency-agents.git
cd agency-agents

# 生成所有工具格式
./scripts/convert.sh

# 交互式安装
./scripts/install.sh
```

**推荐方式三：选择性安装**
```bash
# 只安装工程部
./scripts/install.sh --tool claude-code --division engineering

# 只安装特定角色
./scripts/install.sh --tool cursor --agent frontend-developer,ui-designer
```

### 我的建议

对于刚接触 Agency-Agents 的开发者，我建议从这三个 Agent 开始：

1. **Codebase Onboarding Engineer** — 快速理解你正在开发的项目
2. **Code Reviewer** — 为你的 PR 带来架构级的审查质量
3. **Technical Writer** — 写出与代码质量匹配的文档

不需要一开始就装全部 400+ Agent。找到你当前最需要的 3-5 个角色，深度使用，然后逐步扩展。

## 九、总结与展望

Agency-Agents 不只是一个 GitHub 仓库——它代表了一种**全新的 AI 交互范式**。

在这个范式中，我们不再向 AI 提问，而是**雇佣 AI 专家**。每个专家有其专业领域、工作方法、交付标准和沟通风格。开发者从"撰写提示词"转变为"组建团队"——这更接近于一个技术经理或创业者的工作方式。

126K Stars 说明社区已经为此做好了准备。随着 AI 编程工具的功能不断增强（多 Agent 并行、上下文记忆、工具调用），Agency-Agents 这种"角色定义+工具集成"的模式会变得更加自然和必要。

**"梦之队"的比喻正在成为现实**——不是因为你一次用了 400 个 Agent，而是因为当你需要某个专家时，总有一个已经准备好、定义清晰、随时可用的角色在等着你调用。

未来，我期待看到：
- Agent 文件格式的标准化（可能成为行业标准）
- Agent 之间的协同协议（Agent 定义中包含与其他 Agent 的交互接口）
- Agent 市场（像 VSCode 插件市场一样，用户可以发布和发现 Agent）

Agency-Agents 起了一个好头——它向我们展示了 AI 编程的下一个阶段，不只是"更好用的代码补全"，而是**真正意义上的 AI 团队协作**。

---

*本文基于 [msitarzewski/agency-agents](https://github.com/msitarzewski/agency-agents) 项目 v1.0 版本撰写，Stars 数据截至 2026 年 7 月。*

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)