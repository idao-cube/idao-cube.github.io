## 产品与定位

UI Bakery 是 AI 加持的低代码**内部工具构建器**：在已有数据库与 API 之上快速搭建后台管理面板、Dashboard、CRUD 应用、自动化流程与企业门户。核心卖点是"连接真实数据源而非造玩具"——内置 AI App Agent 可对话生成应用与查询，产出可导出的 React 代码，支持云托管与自托管（Docker/专属 VM），是 Retool 的平替选项。

适合内部团队：取代散落的电子表格工作流，把数据操作搬进带权限、可审计的后台系统。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| AI App Agent | 自然语言生成应用界面与数据查询，含 AI 用量积分 |
| 数据源连接 | 数据库（PostgreSQL 等）、REST/graphQL API 直连 |
| 自动化 | 内置自动化执行（每月额度），连接业务事件 |
| 权限体系 | 内置角色；Team 档起支持 RBAC 与审计日志 |
| 代码导出 | 导出 React 代码，逃离厂商锁定 |
| 自托管 | Docker / 专属 VM 部署，Enterprise 支持 BYO AI 模型与自定义 SSO |

## 定价

| 套餐 | 价格 | 说明 |
| --- | --- | --- |
| Free | $0 | 无限应用与数据源、托管数据库、试用 AI 积分、1000 次自动化/月 |
| Builder | $20/开发者/月（年付；月付 $25） | 含 $25 AI 积分、环境隔离、应用导出、内置角色、50 个只读席位 |
| Team | $35/开发者/月（年付；月付 $40） | 含 $40 AI 积分、RBAC、审计日志、5,000 次自动化/月 |
| Enterprise | 定制 | 专属 VM、自定义 SSO、应用迁移、BYO 模型 |

按"可编辑开发者席位"计费（只读用户免费），团队越大成本线性上升。

## 调用与兼容性

```bash
# 浏览器低代码构建 + AI 对话生成；Enterprise 可自托管
docker run uibakery/self-hosted   # 自托管示例（Docker）
```

- 平台：Cloud（SaaS）或 Self-hosted（Docker / 专属 VM）
- 输出：React 代码导出；数据源支持主流数据库与 REST/graphQL API
- 合规：SOC 2、GDPR（Enterprise 可谈 HIPAA）

## 版本与更新注意

- 2026 年定价稳定：Free / Builder $20–25 / Team $35–40（每开发者），自托管同样有免费档。
- 自托管套餐的 AI 功能仍需连接 UI Bakery 云端消耗积分——纯离线部署但想用 AI 时注意数据流向。
- Free 档没有环境隔离（dev/staging/prod）与应用导出，认真使用建议至少 Builder。
- 被 PH 归类为 AI Coding Agents 且 5.0 满分好评（6 评），2019 年起持续运营的老牌工具。

## 选型建议

- 团队要快速交付内部后台/管理面板，且已有真实数据库或 API：UI Bakery 比从零写代码快得多。
- 与 Retool 对比：UI Bakery 按开发者席位计费，业务用户多时通常更便宜；且提供可导出的 React 代码与自托管。
- 需要严格合规（审计日志、自定义 SSO、数据驻留）：Team/Enterprise 档才覆盖，先评估需求再选档。
- 纯 vibe coding 场景（从 0 造产品）不适用——它面向"已有数据、要建后台"的内部工具场景。