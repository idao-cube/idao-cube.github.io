## 产品与定位

适合技术团队构建 AI 驱动的工作流自动化，支持可视化编排与代码编写双模式。通过 AI Agent 节点实现多模型调用、工具调用与记忆管理，可替代 Zapier/Make 等闭源方案实现更低成本的自动化。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| AI Agent 节点 | 原生支持多模型调用、工具调用、JSON 结构化输出 |
| 记忆管理 | 支持 Window/Buffer/Vector Store 三种记忆类型 |
| 500+ 集成 | 覆盖数据库、API、消息平台、支付等主流服务 |
| 双模式 | 可视化节点编排 + JavaScript/Python 代码 |
| 部署方式 | 云托管、自托管（Docker/K8s）、本地 |

## 常用参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 支持 OpenAI/Claude/Gemini/Ollama 等 |
| `systemPrompt` | 系统提示 | 定义智能体角色与行为约束 |
| `memoryType` | 记忆类型 | 短会话用 Window，长期任务用 Vector Store |
| `tools` | 可用工具 | HTTP Request、Code、Calculator 等 |
| `temperature` | 采样温度 | 结构化输出建议低温 |
| `maxTokens` | 输出限制 | 控制响应长度避免成本溢出 |

## 调用与兼容性

通过 Webhook 触发工作流，支持 REST API 调用与定时调度。AI Agent 节点支持 Function Calling，可与外部 API 和自定义工具联动构建复杂 Agent 系统。

```bash
# Docker 快速启动
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

## 版本与更新注意

版本迭代较快，建议关注官方 Release Notes 获取新功能和安全更新。自建实例需定期备份工作流配置。

## 选型建议

需要将 AI 能力嵌入业务流程、跨系统数据集成、自动化内容生产时可优先评估 n8n；自托管可实现数据完全自主可控。