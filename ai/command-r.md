## 模型与定位

Command R 系列是 Cohere 专为企业场景设计的模型，重点优化检索增强生成（RAG）、工具调用和多步 Agent 任务。**Command R2** 为当前 R 线最新模型：内置结构化数据推理模式，可原生生成并执行 SQL、Python、R 代码直连数据库，专为商业智能与数据分析工作流设计，支持 AWS/Azure 私有部署。更高端的 **Command A / Command A+** 系列（2026 旗舰）已承接复杂 Agent 与多模态场景。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 适用场景 |
| --- | --- | --- | --- | --- |
| Command A+ | 218B 总/25B 激活（MoE） | 128K | ✅ Apache 2.0 | 复杂 Agent、多模态、48 语言 |
| Command A | 111B（dense） | 256K | ❌ | 企业 agentic 旗舰 |
| Command R2 | 约 104B | 128K | ❌ | SQL/Python/R 数据分析 |
| Command R+ | 104B | 128K | ❌ | 旧代旗舰（RAG、Agent） |
| Command R7B | 7B | 128K | ✅ | 轻量 RAG、边缘部署 |

## 核心能力

| 功能 | 说明 |
| --- | --- |
| RAG 优化 | 精准引用来源，减少幻觉，适合知识库问答 |
| 工具调用 | 多步工具链执行，支持复杂 Agent 工作流 |
| 数据模式（R2） | 原生生成并执行 SQL/Python/R，直连数据库分析 |
| 128K 上下文 | 处理长文档、多文档检索结果 |
| 多语言 | 支持 10+ 语言（A+ 达 48 种） |
| Grounded Generation | 基于提供文档生成答案，附带来源引用 |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Cohere API](https://cohere.com/) | 官方 API 服务 |
| [AWS / Azure](https://azure.microsoft.com/) | 企业级私有部署 |
| [HuggingFace](https://huggingface.co/CohereForAI) | Command R7B / A+ 权重下载 |

## 版本与下线注意

Command R / R+（2024 时代）为旧版，当前主线为 Command R2 与 Command A/A+；R2 数据分析模式对幻觉 SQL 需谨慎——官方尚未公开真实脏 schema 上的准确率基准，生产环境接入核心数据前建议独立审计。

## 选型建议

企业 RAG 系统首选 Command A（或私有化 A+）；结构化数据分析用 Command R2（注意 SQL 幻觉风险）；轻量部署选 Command R7B（开源）；多语言场景 Cohere 表现优异。