> 项目地址：[github.com/open-metadata/OpenMetadata](https://github.com/open-metadata/OpenMetadata) | 13.8K Stars | Apache 2.0 协议

## 一、OpenMetadata 是什么？

**OpenMetadata** 是一个开源、统一的元数据平台，为数据发现、数据可观测性和数据治理而设计。它提供了一个**中央元数据仓库**，支持列级血缘追踪和团队协作。

官方描述：*"A unified metadata platform for data discovery, data observability, and data governance."*

> 简而言之：如果你有大量数据资产（表、仪表盘、管道等），需要一个地方来组织、搜索、理解和管理它们——这就是 OpenMetadata 做的事情。

### 核心亮点速览

| 特性 | 说明 |
|---|---|
| **统一元数据仓库** | 连接数据资产、用户、工具产生的元数据 |
| **84+ 连接器** | 覆盖数据仓库、数据库、仪表盘、消息系统、管道等 |
| **列级血缘** | 字段级别的数据血缘追踪，支持手动编辑 |
| **数据质量** | 无代码质量测试，交互式结果面板 |
| **数据治理** | 域、数据产品、标签、角色权限控制 |
| **数据可观测性** | 数据新鲜度、体积、质量、延迟指标 |
| **协作** | 事件通知、告警、公告、对话线程 |
| **SSO + RBAC** | 单点登录 + 基于角色的访问控制 |
| **205 个版本** | 持续迭代，最新 v1.12.6 |

---

## 二、架构设计

OpenMetadata 由四大核心组件构成：

### 1. 元数据 Schema（Metadata Schemas）

- 元数据的核心定义和词汇表
- 基于通用抽象和类型构建
- 支持自定义扩展和属性

### 2. 元数据存储（Metadata Store）

- 中央元数据仓库
- 存储和管理元数据图谱
- 连接数据资产、用户、工具生成的元数据

### 3. 元数据 API（Metadata APIs）

- 基于 Schema 构建的生产和消费元数据的接口
- RESTful API 设计
- 支持事件通知和 Webhook

### 4. 摄取框架（Ingestion Framework）

- 可插拔框架
- **84+ 连接器**支持各种数据源
- 通过 Airflow 等工具调度

```text
数据源 ─┐
数据库 ─┤
仪表盘 ─┼── 摄取框架 ── 元数据 API ── 元数据存储 ── UI
管道 ───┤                              │
消息系统 ┘                       ┌─────┴──────┐
                           数据发现  数据质量  数据治理
```

---

## 三、功能矩阵

### 3.1 数据发现

| 功能 | 说明 |
|---|---|
| **搜索** | 关键词搜索、关联查询、高级过滤 |
| **浏览** | 按表、主题、仪表盘、管道、服务分类浏览 |
| **数据字典** | 字段定义、数据类型、描述信息 |

### 3.2 数据血缘

- **列级血缘**：字段级别的端到端数据流向
- **查询过滤**：基于 SQL 查询自动推导血缘
- **手动编辑**：无代码的可视化血缘编辑器

### 3.3 数据质量

- **无代码测试**：通过 UI 配置质量规则，无需写代码
- **测试套件**：将相关测试分组管理
- **结果面板**：交互式仪表盘展示质量结果

### 3.4 数据治理

| 功能 | 说明 |
|---|---|
| **域（Domains）** | 按业务领域组织数据资产 |
| **数据产品** | 定义和发布数据产品 |
| **标签分类** | 基于标签和术语的自动分类 |
| **Owner 管理** | 数据资产的负责人和干系人 |
| **RBAC** | 基于角色的访问控制 |
| **SSO** | 单点登录集成 |

### 3.5 数据可观测性

- **数据新鲜度**：数据是否按时更新
- **数据体积**：数据量的变化趋势
- **数据质量**：质量测试通过率
- **数据延迟**：数据到达是否及时
- **异常告警**：自动检测异常并通知

### 3.6 协作

- **事件通知**：数据变更时自动通知
- **告警**：自定义告警规则
- **公告**：在数据资产上发布公告
- **对话**：在数据资产上发起讨论
- **Webhook**：集成 Slack、Microsoft Teams、Google Chat

---

## 四、84+ 连接器

OpenMetadata 的摄取框架支持连接各种数据源，覆盖：

| 类别 | 示例 |
|---|---|
| **数据仓库** | Snowflake、BigQuery、Redshift、ClickHouse |
| **数据库** | MySQL、PostgreSQL、Oracle、SQL Server、MongoDB |
| **数据湖** | Delta Lake、Apache Iceberg、Hudi |
| **仪表盘** | Metabase、Superset、Tableau、PowerBI、Redash |
| **消息系统** | Kafka、Redpanda |
| **管道** | Airflow、dbt、Fivetran、Nifi |
| **ML 平台** | MLflow、SageMaker |
| **存储** | S3、ADLS、GCS |
| **对象存储** | MinIO |

---

## 五、快速安装

### Docker 部署（推荐）

```bash
# 克隆仓库
git clone https://github.com/open-metadata/OpenMetadata.git
cd OpenMetadata/docker

# 启动所有服务
docker-compose up -d

# 访问 UI
open http://localhost:8585
```

### Kubernetes 部署

通过 OpenMetadata Kubernetes Operator 部署到 K8s 集群：

```bash
# 使用 Helm Chart
helm repo add open-metadata https://helm.open-metadata.org
helm install openmetadata open-metadata/openmetadata
```

### 官方沙箱

无需安装，直接体验：[sandbox.open-metadata.org](http://sandbox.open-metadata.org)

---

## 六、技术栈

| 层级 | 技术选型 |
|---|---|
| **前端** | TypeScript（43.6%） |
| **后端** | Java（34.6%） |
| **数据摄取** | Python（19.8%） |
| **构建** | Maven + Yarn + Makefile |
| **容器** | Docker + Kubernetes Operator |
| **搜索** | Elasticsearch（内部集成） |
| **数据库** | MySQL / PostgreSQL（元数据存储） |
| **调度** | Airflow（摄取管道调度） |
| **MCP** | Model Context Protocol 支持 |

---

## 七、与其他元数据平台对比

| 维度 | OpenMetadata | Apache Atlas | Amundsen | DataHub |
|---|---|---|---|---|
| **开源协议** | Apache 2.0 | Apache 2.0 | Apache 2.0 | Apache 2.0 |
| **Stars** | 13.8K | 5.0K | 4.4K | 10.2K |
| **连接器数量** | 84+ | 有限 | 中等 | 丰富 |
| **列级血缘** | ✅ 原生 | ✅ | ❌ | ✅ |
| **数据质量** | ✅ 内置无代码测试 | ❌ | ❌ | ✅ |
| **数据可观测性** | ✅ 内置指标 | ❌ | ❌ | ❌ |
| **治理** | ✅ 域 + 数据产品 | ✅ | ❌ | ✅ |
| **无代码测试** | ✅ | ❌ | ❌ | ❌ |
| **UI 体验** | 现代化 | 传统 | 简洁 | 现代化 |
| **MCP 支持** | ✅ 原生 | ❌ | ❌ | ❌ |

---

## 八、适用场景

### 数据团队

- **数据目录**：所有数据资产的统一搜索入口
- **数据血缘**：了解数据从哪里来、到哪里去、被谁使用
- **数据质量**：自动化质量监控和告警

### 数据工程师

- **管道监控**：追踪 ETL/ELT 管道的元数据和运行状态
- **影响分析**：某个字段变更会影响哪些下游报表和模型
- **数据发现**：快速找到需要的数据集和表

### 数据治理团队

- **合规管理**：通过标签分类和 RBAC 实现数据合规
- **数据产品**：定义和管理数据产品的生命周期
- **血缘审计**：满足数据法规要求的血缘追溯

### 平台工程师

- **集成枢纽**：通过 84+ 连接器连接现有数据基础设施
- **MCP 集成**：通过 Model Context Protocol 对接 AI Agent
- **自动化**：通过 API 和 Webhook 自动化元数据流程

---

## 九、社区与生态

| 资源 | 链接 |
|---|---|
| **官网** | [open-metadata.org](https://open-metadata.org) |
| **文档** | [docs.open-metadata.org](https://docs.open-metadata.org) |
| **Slack** | [slack.open-metadata.org](https://slack.open-metadata.org) |
| **沙箱体验** | [sandbox.open-metadata.org](http://sandbox.open-metadata.org) |
| **发布版本** | 205 个 release，最新 v1.12.6 |

---

## 十、总结

OpenMetadata 是目前最活跃的开源元数据平台之一（13.8K Stars）。它以统一元数据仓库为核心，覆盖了数据发现、血缘追踪、质量监控、治理合规、可观测性和团队协作的完整链路。

84+ 连接器、列级血缘、无代码数据质量测试、MCP 协议支持——这些能力使其在大数据生态中具有极强的集成能力。对于正在建设数据平台、需要元数据管理工具的团队来说，OpenMetadata 是一个值得深入评估的开源方案。

**快速开始：**

```bash
git clone https://github.com/open-metadata/OpenMetadata.git
cd OpenMetadata/docker
docker-compose up -d
# 访问 http://localhost:8585
```

> 技术栈：TypeScript 44% + Java 35% + Python 20% + Elasticsearch | 协议：Apache 2.0
>
> 最新版本：v1.12.6（2026-04-22）| 在线体验：[sandbox.open-metadata.org](http://sandbox.open-metadata.org)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)