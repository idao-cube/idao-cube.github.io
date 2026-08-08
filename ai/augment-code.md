## 产品与定位

Augment Code 定位企业级 agentic AI 编程平台，面向大型、复杂的生产代码库（百万行级）。其核心是 **Context Engine**——语义索引整个代码仓库，让 AI 只检索与当前任务相关的上下文，而不是把海量代码塞进提示词，从而显著节省 token 并提升准确性。

在内部基准（Terminal Bench 2.0）上，Augment 比 Claude Code 节省约 33% 的 token，在私有仓库场景节省约 41%；在 40 万+ 文件的代码库上达到 70.6% SWE-bench 水平。它是少数把"AI 代码审查""测试覆盖""事件排查""工单转 PR"等工程流程都做成产品化 agent 的平台，主要服务金融、云厂商、大型互联网公司等对安全合规有要求的团队。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Context Engine | 语义索引整个代码库，按需检索最相关的上下文，节省 token |
| Cosmos | agent 编排平台：在云端沙箱中并行运行编码 loops、CI 修复、迁移任务 |
| Auggie CLI | 终端内的自主编码 agent，用于脚本化、CI 与远程执行 |
| AI Code Review | PR 内联审查（含安全、测试覆盖、正确性检查）；Elasticsearch 3.6M 行 Java 盲测中正确性 +14.8 |
| Test Coverage | 分析测试覆盖缺口，自动补充单元测试 |
| Incident Management | 从日志/监控事件定位根因并给出修复建议 |
| Ticket to PR | 从工单（Jira/Linear）自动生成实现计划与 PR |
| 其他 agent | 依赖升级 bot、CI 失败调查、安全漏洞分诊、文档同步 |

## 计费方式

| 方案 | 价格 | 说明 |
| --- | --- | --- |
| Business | $100/月（flat） | 最多 50 席位、无按席位费；含每月 $100 usage 额度；超出部分 LLM 按提供商公开价 + 40% 服务费，Cosmos compute $0.19/小时（5 分钟计费），可 top-up 即付即用 |
| Enterprise | 定制 | SSO/OIDC/SCIM、SOC 2、CMEK 加密、ISO 42001、数据驻留，按合同定价 |

2025-10 曾因从固定套餐转向信用制引发用户反弹；2026 年起旧版 Indie $20 / Standard $60 / Max $200 套餐已废弃，官网统一为 Business + Enterprise 两档。

## 调用与兼容性

```bash
# CLI 登录并开始会话
augment login
augment start

# VS Code / JetBrains：安装 Augment 官方扩展后即可在编辑器中对话、审查、执行 agent
```

模型支持 GPT-5.5 / GPT-5.4、Kimi K2.6、Claude Opus 4.7 / Sonnet 4.6、Gemini 3 等；Prism 路由按任务自动选择模型，平均节省 20–30% 成本。云端 agent 需要联网；可通过 Remote Agents 在远程仓库上运行。

## 版本与更新注意

- 2025-10 定价改为信用制，2026 年调整为 flat Business + Enterprise，官方价格页面为主要依据。
- Context Engine 是差异化核心：对中小型仓库收益有限，对百万行级仓库的上下文准确性与成本优势最明显。
- 以企业安全合规（SOC 2、CMEK、数据驻留）为卖点，适合有审计要求的团队。
- 部分 agent 功能（事件排查、工单转 PR）仍在快速迭代。

## 选型建议

- 大型代码库团队：Context Engine 的索引与检索机制解决"模型记不住整个仓库"的问题。
- 有代码审查规范的企业：内置 AI Code Review 可补充人工审查盲区。
- 需要弹性算力跑长任务（迁移、CI 修复）的团队：Cosmos 云沙箱按小时计费。
- 个人/小团队注意：$100/月 flat 对少量用户偏贵，且深度依赖云端，不适合离线开发。