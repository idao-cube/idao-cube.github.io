> 项目地址：[github.com/coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills) | 32.4K Stars | MIT 协议 | 43 Skills | v2.3.0

## 一、这不仅仅是一个技能仓库

2026 年初，一个名为 `marketingskills` 的仓库在 GitHub 上以惊人的速度增长——短短几个月就斩获了 32K+ Stars。它不是什么炫酷的前端框架，也不是什么革命性的 AI 模型，而是一堆 .md 文件。

但正是这堆 .md 文件，正在重新定义"营销"这件事在 AI 时代的执行方式。

**`marketingskills` 是由 Corey Haines 创建的开源项目**，本质上是一套为 AI Agent（Claude Code、Cursor、Windsurf、Codex 等）设计的营销技能指令集。但如果你仅仅把它看作"AI 提示词合集"，你将错过这个项目真正革命性的地方。

这个项目回答了一个根本性问题：**当 AI Agent 能够理解你的产品、你的用户、你的市场时，营销执行应该是什么样的？**

答案是：**一个由 43 个模块化 Skill 构成的、层层依赖的、工具链完备的营销操作系统。**

## 二、架构设计：Skill 的依赖网络与上下文穿透

### 2.1 一切都是上下文

`marketingskills` 最精妙的设计之一是它的上下文穿透机制。所有 43 个 Skill 共享一个基础——`.agents/product-marketing.md`。

这个文件是一切营销活动的"宪法"：

```yaml
# Product Marketing Context
## Product Overview
**One-liner:**
**Product category:**
**Business model:**

## Target Audience
**ICP:**
**Use cases:**

## Competitive Landscape
**Direct competitors:**
**Differentiation:**

## Customer Language
**Verbatim quotes:**
**Words to use/avoid:**
```

任何一个 Skill 被调用时，第一件事就是检查这个文件是否存在。如果存在，Agent 会自动加载它，省去重复询问产品信息的痛苦。这就像给 AI Agent 装上了"长期记忆"。

而文件中的内容是通过 `product-marketing` Skill 本身生成的——它可以自动扫描整个代码库（README、Landing Page、package.json），草拟一份 V1 版本，用户只需审核修正即可。

### 2.2 依赖树：一个 Skill 的生态

```
                ┌────────────────────────────────┐
                │      product-marketing         │
                │  （所有 Skill 的上下文基础）      │
                └──────────────┬─────────────────┘
                               │
    ┌────────────┬─────────────┼─────────────┬──────────────┬──────────────┐
    ▼            ▼             ▼             ▼              ▼              ▼
┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────────┐ ┌──────────┐ ┌───────────┐
│ SEO &   │ │   CRO   │ │ Content │ │  Paid &     │ │ Growth & │ │ Strategy  │
│ Content  │ │         │ │ & Copy  │ │Measurement  │ │Retention  │ │           │
├─────────┤ ├─────────┤ ├─────────┤ ├─────────────┤ ├──────────┤ ├───────────┤
│seo-audit│ │cro      │ │copywrit │ │ads          │ │referrals │ │mktg-ideas │
│ai-seo   │ │signup   │ │copy-edit│ │ad-creative  │ │free-tools│ │mktg-psych │
│site-arch│ │onboard  │ │cold-email││ab-testing   │ │churn     │ │customer-  │
│programm │ │popups   │ │emails   │ │analytics    │ │prevent   │ │research   │
│schema   │ │paywalls │ │social   │ │             │ │community │ │           │
│content  │ │         │ │video    │ │             │ │lead-magnt│ │           │
│aso      │ │         │ │image    │ │             │ │co-mktg   │ │           │
│         │ │         │ │sms      │ │             │ │          │ │           │
└─────────┘ └─────────┘ └─────────┘ └─────────────┘ └──────────┘ └───────────┘
```

Skill 之间互相调用、互相补充。`customer-research` 的研究成果喂给 `copywriting` 和 `cro`。`revops` 的工作流链接 `sales-enablement` 和 `cold-email`。`seo-audit` 的发现指导 `schema` 和 `ai-seo` 的执行。

这不是一个孤立的工具集合，而是一个**相互依赖的知识网络**。

## 三、核心 Skill 深度解析

### 3.1 CRO（转化率优化）：从"我觉得"到"系统化诊断"

在 AI 出现之前，CRO 高度依赖经验丰富的优化师。`cro` Skill 将这个经验系统化为一个可重复执行的框架：

```
CRO Analysis Framework（按影响优先级排列）：
1. 价值主张清晰度（最高影响）
2. 标题有效性
3. CTA 放置、文案和层级
4. 视觉层级和可扫描性
5. 信任信号和社会证明
6. 异议处理
7. 摩擦点
```

当用户给 Agent 一个 URL 说"这个页面转化不行"，Agent 不会随便给建议。它会按框架逐层分析，输出结构化的推荐：

- **Quick Wins（立即实施）**：小改动、高影响
- **High-Impact Changes（优先处理）**：大改动、显著提升
- **Test Ideas（A/B 测试建议）**：需要验证的假设
- **Copy Alternatives（文案替代方案）**：标题、CTA 的备选

更深入的是，`cro` 针对不同页面类型提供专用框架——Homepage、Landing Page、Pricing Page、Feature Page、Blog Post——每种页面有独立的优化 Checklist 和心智模型。

### 3.2 Marketing Psychology（营销心理学）：50+ 心智模型的可操作化

这是整个仓库中最令人印象深刻的 Skill 之一。它不仅仅罗列心理学概念，而是将其与营销场景**一一映射**：

| 挑战 | 适用心理模型 |
|-----------|-----------------|
| 转化率低 | Hick's Law（选择越多决策越慢）、Activation Energy（启动能量）、BJ Fogg 行为模型、摩擦理论 |
| 价格异议 | 锚定效应、框架效应、心理账户、损失厌恶 |
| 建立信任 | 权威效应、社会认同、互惠原则、Pratfall Effect（小缺陷让人更可信） |
| 增加紧迫感 | 稀缺性、损失厌恶、Zeigarnik Effect（未完成的任务更占据心智） |
| 留存/减少流失 | 禀赋效应、转换成本、现状偏差 |
| 增长停滞 | 约束理论、局部 vs 全局最优、复利效应 |
| 决策瘫痪 | 选择的悖论、默认效应、助推理论 |
| 新用户引导 | 目标梯度效应（离目标越近动力越强）、IKEA Effect（投入努力更珍惜）、承诺一致性 |

更难得的是，它包含了**定价心理学**专用章节：

- **Charm Pricing / 左位数效应**：$99 感觉比 $100 便宜得多。左数字主导感知。
- **Rounded-Price / 圆整价格效应**：$100 传递优质感，$99 传递性价比。
- **Rule of 100**：$100 以下用百分比折扣（"20% off"），$100 以上用绝对折扣（"省 $50"）。
- **Decoy Effect / 诱饵效应**：设置一个明显劣势的第三选项，让目标选项成为"明显正确的选择"。

这些不是泛泛的心理学术语，而是**可以直接写入营销策略和页面文案的具体战术**。

### 3.3 AI SEO：当 AI 本身成为搜索引擎

`ai-seo` 可能是这个仓库里最"未来感"的 Skill。它解决的是传统 SEO 触及不到的问题：**如何让你的内容被 AI 引用？**

关键洞察：**传统 SEO 让你排名靠前，AI SEO 让你被引用。** 这两者有本质不同。

AI 搜索引擎（ChatGPT、Perplexity、Google AI Overviews、Claude、Gemini）的工作原理与传统搜索引擎完全不同：

- **Google AI Overviews** 出现在约 45% 的搜索中，但会将点击量降低多达 58%
- AI 系统选择来源基于**内容质量、结构和相关性**，而非排名位置
- 品牌通过**第三方来源**被引用的概率是自身域名的 **6.5 倍**
- 优化后的内容被引用的概率是未优化的 **3 倍**

`ai-seo` Skill 提供了三个优化支柱：

**支柱 1：结构——让内容可提取**
AI 系统提取的是段落，不是页面。每段内容应该作为独立声明独立存在：
- 定义块："What is X?" 查询
- 分步块："How to X" 查询
- 对比表格："X vs Y" 查询
- FAQ 块：常见问题
- 统计块：带来源的数据

关键答案段落保持在 **40-60 词**（AI 摘要提取的最佳长度）。

**支柱 2：权威——让内容可引用**
普林斯顿大学 GEO 研究（KDD 2024）给出了量化的优化排名：

| 方法 | 可见性提升 |
|--------|:--------:|
| 引用来源 | +40% |
| 添加统计数据 | +37% |
| 添加引语（带身份） | +30% |
| 权威性语气 | +25% |
| 提高清晰度 | +20% |
| 关键词堆砌 | **-10%（有害）** |

**支柱 3：存在——出现在 AI 搜索的地方**
AI 系统不仅仅引用你的网站——它们引用你出现的任何地方。维基百科占 ChatGPT 引用的 7.8%，Reddit 占 1.8%。

还包含了生成式 AI 时代的实用技术——`/pricing.md`、`/llms.txt` 等机器可读文件，帮助 AI Agent 直接解析产品信息、价格和功能。

### 3.4 Marketing Plan（营销计划）：fCMO 级别的战略输出

这是 v2.3.0 新增的 Skill，代表了整个项目野心的顶峰。它生成的不是一个简单的 Todo 列表，而是一个**13 个章节、8000-12000 词、可直接粘贴到 Notion 的营销计划**。

计划结构：

```
1. 执行摘要 —— 3 大核心策略、90 天优先级、12 月目标
2. 战略框架 —— 品类定位、ICP、品牌声音
3. 当前状态 —— 团队、预算、已完成/进行中/卡住的事项
4. 获客（Acquisition） —— 渠道策略
5. 激活（Activation） —— 用户首次体验优化
6. 留存（Retention） —— 生命周期、流失预防
7. 推荐（Referral） —— 口碑和推荐计划
8. 收入（Revenue） —— 定价、包装、增购
9. 90 天路线图 —— 按周分配、责任人明确
10. 12 月展望 —— 融资里程碑对应节点
11. 营销运营栈 —— 每个 AARRR 阶段对应的 Skill 和工具集成
12. 战术想法库 —— 139 个想法按 AARRR 阶段 + 客户端状态标注
13. 衡量、RACI、开放决策 —— 北极星指标、归因模型
```

不同融资阶段的营销预算参考：
- **Pre-seed / 自筹**：$0-$2K/月，仅限有机渠道
- **种子轮完成**：$5-$15K/月，首次付费测试
- **种子轮推进**：$20-$50K/月，第二个营销人员
- **A 轮**：$50-$150K/月，品牌 + 内容 + 设计师
- **B 轮+**：$150K+/月，品牌广告 + PR agency

计划还包含了 SaaS 增长的真实规律——**不是教科书上的指数曲线，而是 S 曲线 + 平台的交替演进**。健康的 SaaS 增长往往是线性的（每月可预测的新增），偶尔被阶梯式增长（新企业级产品线、新细分市场、新渠道突破）打断。

## 四、工具集成：80+ 工具的 CLI/MCP 生态

`marketingskills` 不仅仅是 Skill 的组合。它还提供了一个**完备的工具注册表**（`tools/REGISTRY.md`），覆盖了现代营销技术栈的各个方面：

| 类别 | 代表性工具 |
|----------|-------------------|
| 分析 | GA4（MCP）、Mixpanel、Amplitude、PostHog、Segment |
| SEO | Google Search Console、Semrush、Ahrefs、DataForSEO、RankParse（MCP） |
| 数据增强 | Clearbit、Apollo、ZoomInfo（MCP）、Clay（MCP） |
| CRM | HubSpot、Salesforce、Close |
| 支付 | Stripe（MCP）、Paddle |
| 推荐/联盟 | Rewardful、Tolt、PartnerStack |
| 邮件 | Mailchimp（MCP）、Customer.io、Resend（MCP）、SendGrid |
| 短信 | Twilio、Klaviyo、Postscript |
| 广告 | Google Ads（MCP）、Meta Ads、LinkedIn Ads、TikTok Ads |
| 冷邮件 | Hunter、Snov、Lemlist、Instantly |
| 社交 | Buffer |
| 视频 | Wistia、HeyGen（MCP）、Hyperframes |
| 评论 | Trustpilot、G2 |
| 自动化 | Zapier（MCP） |
| 搜索引擎 | Exa（MCP） |

更重要的是，这些工具都有零依赖的 Node.js CLI 封装（`tools/clis/`），AI Agent 可以直接调用，不需要复杂的 OAuth 流程——API Key 放环境变量即可。

对于没有原生 MCP 的工具（HubSpot、Salesforce、Meta Ads 等），**Composio** 集成层提供了统一的 MCP 接入点。

这意味着：**一个 AI Agent + 这些 Skill + 这些工具 = 一个完整的营销执行团队**。

## 五、139 种营销策略：按阶段、预算和时间线分类

`marketing-ideas` Skill 包含 139 种经过验证的营销策略，按 17 个类别组织：

| 类别 | 策略数 | 例子 |
|----------|:-----:|--------|
| 内容 & SEO | 10 | 程序化 SEO、术语表营销、内容再利用 |
| 竞争对手 | 3 | 对比页、营销柔术 |
| 免费工具 | 9 | 计算器、生成器、Chrome 扩展 |
| 付费广告 | 12 | LinkedIn、Google、重定向、播客广告 |
| 社交 & 社区 | 10 | LinkedIn 受众、Reddit 营销、短视频 |
| 邮件 | 9 | 创始人邮件、上手序列、赢回 |
| 合作伙伴 | 11 | 联盟计划、集成营销、Newsletter 互换 |
| 活动 | 8 | 网络研讨会、大会演讲、虚拟峰会 |
| PR & 媒体 | 4 | 媒体报道、纪录片 |
| 发布 | 10 | Product Hunt、终身优惠、赠品 |
| 产品驱动 | 10 | 病毒循环、Powered-by、免费迁移 |
| 内容形式 | 13 | 播客、课程、年度报告、年度回顾 |
| 非传统 | 13 | 奖项、挑战、游击营销 |
| 平台 | 8 | 应用市场、评论网站、YouTube |
| 国际化 | 2 | 扩张、地区定价 |
| 开发者 | 4 | DevRel、认证 |
| 特定受众 | 3 | 推荐、播客巡演、客户语言 |

按阶段推荐：
- **发布前**：Waitlist 推荐、早鸟定价、Product Hunt 准备
- **早期阶段**：内容 & SEO、社区、创始人主导的销售
- **增长阶段**：付费获客、合作伙伴、活动
- **规模阶段**：品牌活动、国际化、媒体

按预算推荐：
- **免费**：内容 & SEO、社区建设、社交媒体
- **低预算**：定向广告、赞助、免费工具
- **中等预算**：活动、合作伙伴、PR
- **高预算**：收购、会议、品牌活动

## 六、从 v1.x 到 v2.x：一个项目的演化逻辑

从版本历史可以看出这个项目的演进思路：

**v1.x → v2.0（2026-05-05）**：重构了 17 个 Skill 名称，合并了 `page-cro` + `form-cro`，统一命名规范。这是从"功能膨胀"到"架构整洁"的必经之路。

**v2.0 → v2.1**：增加了 `sms` Skill，拓展了短信营销的合规框架（TCPA、A2P 10DLC）。

**v2.2**：增加了 `prospecting` Skill 和 GitHub 潜在客户发现 CLI，将触角延伸到了销售开发领域。

**v2.3（最新）**：
- `marketing-plan` Skill — 13 章节的 fCMO 级营销计划生成器
- 引用了 Corey Haines 自己的书 *Founding Marketing* 中的预算规划、增长模式和团队框架
- 将整个项目的输出从"战术执行"提升到了"战略规划"层面

这个进化路径很清晰：**从单个营销战术的自动化，到完整营销体系的操作系统化**。

## 七、为什么这很重要

### 7.1 营销的"软件化"

传统的营销知识存在于人的头脑中。经验丰富的 CMO 知道如何做 CRO、如何写 Copy、如何设计实验。但这些知识是隐性的、难以复制的。

`marketingskills` 项目将这些隐性知识**显性化、结构化、可编程化**。任何一个 AI Agent，只要加载这些 Skill，就能在营销任务上表现出 "mid-level marketer" 的水准。

这不是在替代营销人员——这是在将营销知识**封装为可执行的协议**。

### 7.2 上下文驱动的营销

所有 Skill 共享 `product-marketing.md` 的设计，揭示了 AI 时代营销操作的核心原则：**一切从上下文出发**。

传统营销中，"了解产品"是一个 onboarding 过程。AI 时代，这是一个结构化的数据文件。Agent 不需要"学习"你的产品，它只需要读一个文件。

### 7.3 工具链的闭环

Skill + API/MCP 集成的组合，让 AI Agent 不仅能"想"（战略规划），还能"做"（工具执行）。从 SEO 审计到广告文案生成，从冷邮件撰写到数据分析，整个营销工作流可以在 Agent 内部闭环完成。

## 八、局限与挑战

作为一个开源项目，`marketingskills` 并非没有局限：

1. **内容深度参差不齐**：有些 Skill（如 `marketing-psychology`、`ai-seo`、`churn-prevention`）极其深入，有些则相对浅显。

2. **执行依赖工具链**：很多 Skill 推荐的工具（GA4、Stripe、Ahrefs）需要 API Key 和付费订阅，不是所有团队都能轻易获取。

3. **AI Agent 本身的限制**：即使有最好的 Skill，当前 AI Agent 在创意策略、品牌直觉和文化敏感度方面仍然无法替代人类营销专家。

4. **语言/市场偏向**：所有内容以英语为主，针对北美 SaaS 市场。对于中国、东南亚等市场的适用性需要验证。

## 九、总结

`marketingskills` 不是一份提示词合集，它是**营销知识的可执行操作系统**。它用 43 个模块化的 Skill、139 种增长策略、80+ 工具集成，构建了一个 AI Agent 能够理解和执行的营销知识网络。

对于营销人员，它是**思考框架的加速器**——你可以看到最优秀的营销实践如何被系统化、结构化地呈现。

对于创业者，它是**营销团队的平替**——一个 AI Agent + 这些 Skill = 一个入门级营销团队的执行力。

对于开发者，它是**AI Agent 技能工程的最佳实践**——如何设计上下文共享、依赖管理、工具集成的参考范本。

32K Stars 不是巧合。当 AI Agent 越来越多地参与到知识工作中时，像 `marketingskills` 这样的项目正在定义**知识如何被封装、共享和执行**的新范式。

> 项目地址：[github.com/coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)
> 作者：[Corey Haines](https://corey.co)
> 相关资源：[Swipe Files](https://swipefiles.com) | [Conversion Factory](https://conversionfactory.co) | [Magister Marketing](https://magistermarketing.com)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)