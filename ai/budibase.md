## 产品与定位

Budibase 是开源低代码平台，2026-03-12 正式推出 AI Agents（Beta），把自己定位为"all-in-one 开源 AI 工作流工具包"：在同一个工作区里构建内部工具、可视化自动化，以及能真正读写数据、触发工作流的 AI Agent。它把 App 构建器、自动化构建器、Agent 构建器、内置数据库和知识库（RAG）打包在一起，适合运营团队把审批流、服务台分诊、员工自助查询等日常重复工作交给 agent 自动化。ISO 27001 认证，可自托管，合规友好。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| Agent Builder | 自然语言指令定义 agent，配置工具/模型/知识源，内置意图路由 |
| 多通道部署 | Slack、Discord、Microsoft Teams，即将支持 Teams 与更多渠道 |
| 模型无关 | OpenAI/Anthropic/Mistral/自定义，任何 OpenAI 兼容 API 含本地模型 |
| 工具调用 | 表读写、API 请求、自动化调用均可作为工具，按权限显式授权 |
| 知识检索 | 向量数据库（含 pgvector）+ 知识源，回答有依据 |
| Evals | 上线前按场景测试评估 agent 行为 |
| Automation Builder | 20+ 触发器与动作，事件驱动后台自动化 |
| 低代码 App | 40+ 预置组件、内置 Budibase DB、RBAC/SSO/SAML/SCIM、审计日志 |

## 计费方式

| 方案 | 价格 | 要点 |
| --- | --- | --- |
| Open Source（自托管） | 免费 | 无限 apps/automations/agents，无软件授权费 |
| Pro | $19/月 | 5K actions、2K AI credits、1 creator、1 workspace |
| Premium | $49/月 | 20K actions、10K credits、SSO、备份、10 workspaces |
| Business | $299/月 | 250K actions、50K credits、强制 SSO、用户组 |
| Enterprise | 定制 | 无限 actions、SCIM、审计日志、SLA |
| 附加项 | — | 终端用户 $5/user/月，额外 creator $50/月 |

## 调用与兼容性

```bash
# 自托管（Docker）
docker run -d -p 80:80 budibase/budibase

# 或直接使用 Budibase Cloud
# https://account.budibase.com
```

```yaml
# Agent 工具授权示例（表级权限）
tools:
  - budibase.Tickets.list_rows
  - budibase.Tickets.get_row
  - budibase.Tickets.update_row   # 仅显式授权的操作才可用
```

## 版本与更新注意

Agents 目前仍是 Beta，复杂对话场景可能不稳定。计费为 action 硬上限（Pro 仅 5K actions/月），超额必须升级，高频自动化团队注意评估用量。SSO、审计日志等企业功能需要 Premium 及以上；自托管开源版无官方支持。

## 选型建议

面向"把 AI 接进现有运营工作流"的团队：员工自助、审批路由、工单分诊场景首选 Budibase——无需写胶水代码即可让 agent 动真数据。对比：Appsmith 无 action 上限但缺 AI agent；Retool 更偏开发者；n8n 偏复杂自动化编排。监管行业（政府/医疗）看重自托管与 ISO 27001 时优势明显。