## 当 AI Agent 进入华尔街

2025 年底，当 Anthropic 在 GitHub 上开源 `financial-services` 这个仓库时，大多数人的第一反应是把它当作又一套 AI 玩具。7 个贡献者、52 次提交、零个 release——看起来像是 Anthropic 产品团队某个周五下午的 side project。

但数字不会说谎。一年后，这个仓库积累了 20,800 颗星。不是因为它代码写得好看（事实上它的 Python 只占 86.4%，Shell 7.1%，JavaScript 6.5%，代码总量也不大），而是因为它准确地定义了"AI Agent 在金融服务业应该长什么样"。

更重要的是，它的架构解决了一个困扰行业多年的问题：**LLM 的能力很强，但如何在合规框架内把它嵌入已有的金融工作流？**

投行的 IT 系统不是一般的复杂。一个典型跨国投行的技术栈包括 Bloomberg Terminal、FactSet、CapIQ 等多个数据终端，Excel 模型的版本管理，PowerPoint 模板的合规审查，以及一整套从 KYC 到 GL 对账的后台流程。让 AI 在这套体系里干活，不是 API 调用的问题——而是架构问题。

financial-services 这个项目给出了一个参考答案。

## 一源双面：一个系统提示，两种部署方式

这个项目最聪明的一点，是它的"一源双面"设计。每个 Agent 都只有一组系统提示和技能文件，但可以以两种完全不同的方式运行：

1. **Claude Cowork 插件**——分析师在桌面端直接安装使用，通过斜杠命令交互
2. **Claude Managed Agent**——平台团队将其部署为 HTTP API 端点，嵌入自己的工作流引擎

目录结构清晰地反映了这个哲学：

```
plugins/
  agent-plugins/           # 自主 Agent —— 各自包含完整系统提示
  vertical-plugins/        # 技能/命令/连接器的垂直分类源
  partner-built/           # 合作伙伴贡献的插件
managed-agent-cookbooks/   # Managed Agent 部署模板
claude-for-msft-365-install/ # Microsoft 365 插件管理工具
```

关键的工程直觉：**同一个 agents/`<slug>`.md 文件是两种部署方式的单一真相来源。** Cowork 直接加载它，Managed Agent 的 agent.yaml 通过 system.file 引用它。对 Agent 行为的任何修改，只需要改一个文件。

这个设计巧妙避开了金融企业里最常见的"影子 IT"问题——分析师偷偷装个 SaaS 工具，合规部门不知道。Cowork 插件的安装通过 Marketplace 管理，Managed Agent 的部署经过 Platform Engineering 团队的审批流程。同一个 Agent，两种审批通道，各自适用不同场景。

## 10 个 Agent，覆盖投行前中后台

项目提供了 10 个命名 Agent，按功能领域分为四组：

**覆盖与咨询（Coverage & Advisory）：**

- **Pitch Agent**——投资银行 Pitch 书的端到端生成。给定一个标的公司和交易情景，自动构建可比公司分析（Comps）、先例交易、DCF 估值模型、LBO 模型，最后生成一份带品牌模板的 Pitch Deck。这是投行最核心的"拉皮条"工具——合伙人拜访客户前，分析师连夜做的那种工作。
- **Meeting Prep Agent**——客户会议的简报包生成。从内部系统和公开数据抓取客户的最新动态、持仓变化、近期新闻，整理成一页纸的 briefing pack。

**研究与建模（Research & Modeling）：**

- **Market Researcher**——根据行业或主题自动生成行业概览、竞争格局、同业比较和观点短名单。覆盖 equity research 的标准产出。
- **Earnings Reviewer**——财报电话会议 + 监管文件 → 模型更新 → 研究初稿。覆盖了 earnings season 工作量最大的环节。
- **Model Builder**——在 Excel 中实时生成 DCF、LBO、三表模型和可比分析。每个输出单元格都是可追溯的公式。

**基金运营与财务运营（Fund Admin & Finance Ops）：**

- **Valuation Reviewer**——解析 GP 数据包，运行估值模板，生成 LP 报告草稿。覆盖私募基金的估值委员会工作流。
- **GL Reconciler**——发现总账与子账的断裂，追踪根因，路由到负责人审批。覆盖月底对账中最痛苦的部分。
- **Month-End Closer**——应计项处理、滚转计算、差异分析。会计月结的自动化版本。
- **Statement Auditor**——审计 LP 对账单，确保分配前的一致性。

**运营与开户（Operations & Onboarding）：**

- **KYC Screener**——解析开户文件，运行规则引擎，标记缺失信息。反洗钱合规的第一步。

每个 Agent 的 `agent.yaml`（Managed Agent 部署用）都包含一段调用 `callable_agents` 的定义。以 Pitch Agent 为例：

```yaml
name: pitch-agent
model: claude-opus-4-7
system:
  file: ../../plugins/agent-plugins/pitch-agent/agents/pitch-agent.md
skills:
  - { from_plugin: ../../plugins/agent-plugins/pitch-agent }
callable_agents:
  - { manifest: ./subagents/researcher.yaml }
  - { manifest: ./subagents/modeler.yaml }
  - { manifest: ./subagents/deck-writer.yaml }
tools:
  - type: agent_toolset_20260401
  - type: mcp_toolset, mcp_server_name: capiq }
  - type: mcp_toolset, mcp_server_name: daloopa }
```

这里 Manifest 配置会在部署阶段被 deploy script 解析：`from_plugin` 会实际上传所有技能文件，`callable_agents` 会先创建子 Agent 再注入其 ID。

每个 Pitch Agent 的工作流程是：

1. 确认标公司、行业、情景 → 确定 5-8 个可比公司和 5-10 个先例交易
2. 调用 sector-overview 技能写公司简介和市场定位
3. 通过 CapIQ MCP 拉取交易倍数和监管文件
4. 调用 comps-analysis 技能展开可比分析
5. 调用 lbo-model / dcf-model / 3-statement-model 构建估值模型
6. 生成 football field 估值区间汇总
7. 调用 pitch-deck 技能在银行 PowerPoint 模板上生成 Deck
8. 运行 ib-check-deck 技能验证所有数字的可追溯性

Agent 的系统提示中有一条关键的防护栏："**每个数字必须可追溯。如果某个倍数或先例交易无法从 CapIQ 或监管文件中获取，标注为 [UNSOURCED]，不允许估算。**"

这正是行业需要的——不是"AI 帮你做事"，而是"AI 帮你做事，且每一步都留下审计线索"。

## Skills + Commands + Connectors：三层能力分离

这是项目架构的第二个关键设计：能力按"自动/手动/数据"三个维度分离。

**Skills（技能）**——自动触发的领域知识。当 Claude 觉得当前场景需要用到某个技能时，系统自动加载对应的 SKILL.md 文件。技能的触发条件由文件中的 `triggers` 字段定义，不需要用户手动调用。核心技能包括：

- `comps-analysis`——可比公司分析的标准方法、异常值处理规则
- `dcf-model`——DCF 建模的完整流程，从 FCF 预测到终值计算
- `lbo-model`——LBO 模型的入口/退出假设、资金来源与使用、回报敏感性表
- `audit-xls`——Excel 审计的"蓝/黑/绿"颜色规范，无硬编码规则
- `pitch-deck`——PPT 模板填充规范，每个数字必须绑定到 Excel 命名区域
- `ib-check-deck`——Deck 质量控制清单：总额平衡、脚注完整、日期一致

**Commands（命令）**——用户主动触发的斜杠命令。在 Cowork 会话中直接输入 `/comps`、`/dcf`、`/lbo`、`/earnings`、`/ic-memo` 等直接执行特定任务。commands/ 目录下的每个 markdown 文件定义了一个命令的行为。

**Connectors（连接器）**——MCP 数据源。所有数据连接器集中在 financial-analysis 核心插件中，其他垂直插件共享使用。这种设计避免了 N 个 Agent 各自连接同一数据源导致的认证和配置混乱。

三层分离意味着：分析师可以只安装自己需要的垂直插件组合。如果你做 equity research，只安装 `financial-analysis` 和 `equity-research`，获得对应的技能、命令和数据连接器，不需要关心 fund-admin 或 private-equity 的能力。

## 11 个 MCP 数据连接器：AI 与金融数据的桥梁

MCP（Model Context Protocol）是 Anthropic 推出的开放协议，用于 AI 模型与外部数据源交互。financial-services 项目集成了 11 个金融数据 MCP 服务器，这是目前我看到的最全面的金融 AI 数据连接器清单：

| 数据商       | MCP 端点                  | 用途               |
| ------------ | ------------------------- | ------------------ |
| Daloopa      | mcp.daloopa.com           | 自动化财务数据提取 |
| Morningstar  | mcp.morningstar.com       | 基金和股票研究数据 |
| S&P Global   | kfinance.kensho.com       | Capital IQ 数据    |
| FactSet      | mcp.factset.com           | 财务数据和行业分析 |
| Moody's      | api.moodys.com            | 信用评级和风险数据 |
| MT Newswires | vast-mcp.blueskyapi.com   | 实时金融新闻       |
| Aiera        | mcp-pub.aiera.com         | 财报电话会议分析   |
| LSEG         | api.analytics.lseg.com    | 伦敦交易所数据     |
| PitchBook    | premium.mcp.pitchbook.com | PE/VC 市场数据     |
| Chronograph  | ai.chronograph.pe         | 私募投资管理       |
| Egnyte       | mcp-server.egnyte.com     | 文件存储和文档管理 |

所有这些连接器在 `financial-analysis` 核心插件中的 `.mcp.json` 文件中定义，格式统一为 HTTP 端点：

```json
{
  "mcpServers": {
    "daloopa": { "type": "http", "url": "https://mcp.daloopa.com/server/mcp" },
    "morningstar": { "type": "http", "url": "https://mcp.morningstar.com/mcp" },
    "sp-global": { "type": "http", "url": "https://kfinance.kensho.com/integrations/mcp" }
  }
}
```

每个 Agent 在自己的 agent.yaml 中选择需要的数据连接器。Pitch Agent 只需要 CapIQ 和 Daloopa，而 GL Reconciler 则需要 Egnyte 来访问对账文件。

值得注意的是，每个 MCP 提供商可能要求单独订阅或 API 密钥。这个项目不捆绑许可证——它提供连接协议，数据访问权由各银行自行采购。

## Managed Agent Cookbooks：从头到尾的部署方案

对于需要将 Agent 嵌入自有工作流引擎的金融机构，`managed-agent-cookbooks/` 目录提供了完整的部署参考。

每个 Agent 的 cookbook 包含：

1. **agent.yaml**——完整的 Agent 配置，包括模型选择（claude-opus-4-7）、工具集、MCP 服务器、技能列表、子 Agent 引用
2. **subagents/*.yaml**——叶子 Worker 子 Agent 的定义（每个 Agent 有 2-3 个子任务 worker）
3. **steering-examples.json**——触发 Agent 的示例事件，用于测试和文档
4. **README.md**——安全等级说明和跨 Agent 交接指南

Pitch Agent 的 3 个子 Agent：

| 子 Agent    | 权限            | 职责                                        |
| ----------- | --------------- | ------------------------------------------- |
| researcher  | Read            | 研究标的公司和行业                          |
| modeler     | Read            | 构建估值模型                                |
| deck-writer | **Write** | 生成 Pitch Deck（唯一有写权限的 sub-agent） |

安全设计上有意限制：**deck-writer 是唯一拥有 Write 权限的子 Agent，其他 sub-agent 只能读取数据**。这意味着一个数据偏差不会被错误地写到最终的客户交付物中。

部署方式是一个 shell 脚本：

```bash
scripts/deploy-managed-agent.sh gl-reconciler
```

脚本会解析 agent.yaml 中的 Manifest 配置，完成以下工作：

- 解析 `system.file` 引用 → 内联系统提示文本
- 解析 `skills.from_plugin` → 上传技能文件并获取 skill_id
- 解析 `callable_agents.manifest` → 先创建子 Agent，注入其 agent_id
- 最终 POST 到 `/v1/agents` 创建 Orchestrator Agent

项目还提供了 `scripts/check.py` 用于预提交验证——lint 每个 Manifest、验证所有交叉文件引用可解析、检查 agent-plugins 下的技能副本是否与 vertical-plugins 源保持同步。

## 跨 Agent 交接：写代码时考虑的安全问题

当项目有 10 个知情的 Agent 时，一个自然的问题是：它们能互相调用吗？

Anthropic 团队的选择很务实——**Agent 之间不直接调用**。当一个 Agent 需要另一个 Agent 的产出时，它会在输出中发射一个 `handoff_request` JSON blob：

```json
{
  "type": "handoff_request",
  "target_agent": "valuation-reviewer",
  "payload": {
    "event": "Review portco valuations for fund X as of 2026-Q1",
    "context_ref": "fund_X_2026Q1_val_package"
  }
}
```

然后由 `scripts/orchestrate.py` 这个参考事件循环来路由。这个脚本的设计细节值得品味 —— 它处理了一个真实的安全威胁：

> "攻击者可以控制已处理的文档内容。如果在一个被处理的 PDF 中嵌入 handoff_request blob，而 Orchestrator 恰好回显了文本内容，那么攻击者就可以伪造跨 Agent 交接。"

缓解措施：

1. 硬编码允许目标列表（hard-allowlist），只有 10 个 Agent 可以被交接
2. JSON Schema 校验 payload，`event` 最大 2000 字符，`context_ref` 严格正则
3. 实际上，参考代码明确注明这不是生产实现——真正的金融工作流应该用 Temporal、Airflow 或 Guidewire 事件总线，而不是 shell 脚本 + 正则

这个安全设计给了我一个启示：**在 Agent 系统中，文档中毒（document poisoning）不是理论问题，而是工程问题。** 任何从不可信来源读取内容的 Agent，都必须假设输出可能包含被注入的指令。

## 微软 365 集成：金融行业的入口之战

项目包含一个独立的 `claude-for-msft-365-install/` 目录，用于在企业环境中部署 Claude 的 Microsoft 365 插件——让 Claude 在 Excel、PowerPoint、Word 和 Outlook 内部运行。

这不是一个 Cowork 插件，而是一个 **Claude Code 插件**，专为 IT 管理员设计：

```
claude plugin install claude-for-msft-365-install@claude-for-financial-services
/claude-for-msft-365-install:setup
```

这个脚本会：

1. 生成自定义的 Office 加载项 Manifest，指向企业自建的 LLM 网关（Vertex AI、Bedrock 或内部网关）
2. 通过 Microsoft Graph 授予 Azure 管理员权限
3. 为每个用户配置路由策略

技术意义在哪里？金融行业的主流工作是发生在 Microsoft Office 里的。"AI 帮你写代码"对投行有用，但"AI 在你的 Excel 里直接建模"才是真正的 game changer。

一个做 DCF 模型的投行分析师的工作流变成：

1. 在 Outlook 收到 MD 的邮件："帮我跑一下 XYZ 的 DCF"
2. Claude 在 Excel 中打开模型模板
3. 通过 MCP 连接 CapIQ 拉取最新财务数据
4. 运行 dcf-model 技能构建完整的现金流预测
5. 结果生成在 Excel 里，每个公式都带注释说明来源
6. 在 PowerPoint 中生成 football field 估值区间图表
7. 分析师只需要审查和微调，不需要从头建模

关键在于，整个过程发生在企业已有的 Office 环境中，不需要切换到任何新的 Web 应用。

## 安全分级与合规考量

每个 Agent 的 README 都包含一个 security tier 标签。虽然没有公开的完整分级矩阵，但从配置中可以推断出逻辑：

- **只读 Agent**（Market Researcher）——只能查询数据，无法修改任何文件
- **审核 Agent**（Valuation Reviewer）——可以生成报告草稿，但无法对外发送
- **执行 Agent**（Pitch Agent）——可以生成最终交付物，但系统提示明确禁止"对外通信"

Pitch Agent 的系统提示写得很清楚：

> "该 Agent 没有电子邮件或消息工具；客户沟通发生在 Agent 之外。"

这种"能力裁剪"模式让我想起另一件事：Anthropic 没有选择在代码层面强制执行权限控制，而是通过系统提示和工具集组合来约束 Agent 行为。Agent 有 Read/Write/Edit 工具的访问权限，但没有 email 或 message 工具——架构上的选择比规则更可靠。

项目还包含一个 `mcp-categories.json` 文件，定义了所有 MCP 连接器的分类体系。这为未来实现更细粒度的数据访问控制埋了伏笔。

## 合作伙伴生态：LSEG 和 S&P Global

`plugins/partner-built/` 目录目前包含两个合作伙伴插件：

- **LSEG**——债券相对价值分析、互换曲线、FX Carry、期权波动率、宏观利率监测，基于 LSEG 数据
- **S&P Global**——单页报告、财报预览、融资摘要，基于 S&P Capital IQ 数据

这意味着 Anthropic 选择了一个"平台 + 生态"的策略。他们没有自己做 LSEG 的数据解析，而是让 LSEG 团队直接贡献插件。这既保证了数据准确性和合规性，也降低了 Anthropic 自身的开发和维护负担。

对于金融科技领域的读者来说，这是一个值得关注的信号：MCP 协议正在成为"AI 应用与金融数据商"之间的标准接口。如果这个趋势持续，未来每个主要金融数据商都会提供 MCP 端点——就像过去十年每个数据商都有自己的 REST API 一样。

## 项目设计的五个工程启示

回顾整个项目的架构，有五个设计决策值得思考：

**一、技能与 Agent 的分离。** 技能（SKILL.md）是独立的领域知识模块，可以被多个 Agent 复用。Agent 只是技能的组装者和工作流的执行者。这种"技能在前、Agent 在后"的模式，比"为每个 Agent 单独编写能力"的常见做法更可维护——当 DCF 模型的假设条件变化时，只需要改一个 dcf-model 技能文件，所有用到它的 Agent 自动同步。

**二、纯文件系统，无构建步骤。** 项目中的所有 Agent 定义、技能文件、命令脚本都是 markdown 和 JSON。没有任何代码需要编译、打包或构建。这意味着业务人员可以直接编辑技能文件——投行的量化分析团队可以维护自己的 dcf-model 技能，不需要等待 AI 团队发版。

**三、Manifest 到 API 的透明映射。** agent.yaml 中的 `system.file`、`skills.from_plugin`、`callable_agents.manifest` 等都是部署时的语法糖，deploy script 会解析成真实的 API 调用。这让开发者可以用简洁的 YAML 编写 Agent 配置，同时部署后直接对应 API 层的完整控制权。

**四、硬编码的安全边界。** 跨 Agent 交接的 allowlist 是硬编码在 orchestrate.py 中的，而不是从配置文件读取。这种"安全配置即代码"的模式避免了配置文件被篡改的风险。在金融行业，这种偏执可能是必要的。

**五、梯度化的 Agent 能力。** 从只读到完全执行，每个 Agent 的能力范围被精心裁剪。Pitch Agent 可以生成客户 Deck 但不允许发邮件，KYC Screener 可以审查文件但不允许做开户决定。这不是技术的限制，而是流程的设计。

## 不是最终答案，是一个架构参考

需要明确的是：这是一个参考实现，不是产品。Anthropic 在 README 的开头就声明了免责声明：

> "此仓库中的任何内容均不构成投资、法律、税务或会计建议。这些 Agent 生成的分析师工作产品——模型、备忘录、研究报告、对账结果——需由合格专业人士审阅。"

项目的价值不在于直接产出合规的投行工作流，而在于提供了一个可参考的架构模式：如何组织金融 AI Agent 的能力、如何连接数据源、如何管理跨 Agent 的协作、如何在合规框架内部署。

现实中的投行不会直接把这个仓库部署到生产环境。它们会 fork 它，替换掉 MCP 连接器的认证方式，把 handoff 路由从参考脚本换成 Temporal 工作流，把系统提示改写成符合自家合规审阅要求的版本。

但 fork 一个 20,800 星的开源项目来构建自己的投行 AI 中台，比从零开始设计这些架构要容易得多。这大概就是这个项目真正的价值——不是在做一个产品，而是在制定一个标准。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)