## 模型与定位

适合中文场景对话、知识问答、代码助手与业务流程自动化。GLM-5 系列是当前旗舰：GLM-5.2 支持 1M 无损上下文且 Coding 能力开源 SOTA，GLM-5.1 综合能力全面对齐 Claude Opus 4.6（首个全面对齐的中国模型），GLM-5 编程能力对齐 Claude Opus 4.5。另有 GLM-4.7-Flash 等免费模型普惠可用。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本为主，多模态系列（GLM-5V-Turbo/GLM-4.6V）支持图像 |
| 输出能力 | 文本、结构化 JSON、函数调用 |
| 推理模式 | 支持思考（thinking.enabled）与非思考双模式 |
| 典型模型名 | `glm-5.2`（旗舰，1M）、`glm-5.1`、`glm-5`、`glm-5-turbo`、`glm-4.7`、`glm-4.7-flash`（免费） |
| 上下文窗口 | GLM-5.2 为 1M；GLM-5/5.1/4.7 为 200K；最大输出 128K |

## 常用请求参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `model` | 指定模型 | 生产环境固定版本避免结果漂移 |
| `thinking` | 深度思考开关 | `{"type": "enabled"}` 启用，复杂推理任务建议开启 |
| `temperature` | 控制随机度 | 严谨业务推荐低温 |
| `top_p` | 采样范围 | 与温度择一优先调参 |
| `max_tokens` | 输出长度控制 | 对性能与成本做约束 |
| `tools` | 工具调用定义 | 工作流场景建议显式配置 |
| `stream` | 流式输出 | 聊天体验更自然 |

## 调用与兼容性

兼容 OpenAI ChatCompletions 接口，支持 REST API 与官方 `zai-sdk`（Python/Java）接入。

```bash
# OpenAI 兼容调用示例
curl https://open.bigmodel.cn/api/paas/v4/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${ZHIPU_API_KEY}" \
  -d '{
    "model": "glm-5",
    "messages": [{"role": "user", "content": "介绍一下你自己"}],
    "thinking": {"type": "enabled"},
    "max_tokens": 65536
  }'
```

## 版本与下线注意

> **GLM-5.2** 为最新旗舰：1M 无损上下文，长程任务显著提升（减少上下文漂移与目标遗忘），Coding 与长程任务评测开源 SOTA，已发布 GLM Coding Plan 团队版（企业席位/权限/用量统一管理）。

> **GLM-5.1**：Coding 能力对齐 Claude Opus 4.6，可自主持续工作长达 8 小时，首个综合能力全面对齐的中国模型。**GLM-5**：744B（激活 40B）参数，首次集成 DeepSeek Sparse Attention，SWE-bench-Verified 77.8 / Terminal Bench 2.0 56.2（开源最高分）。`glm-4.5-flash` 即将下线。

## 选型建议

复杂长程工程与深度编码选 GLM-5.2/5.1，通用对话与推理选 GLM-5/4.7，成本敏感或高频调用选免费的 GLM-4.7-Flash；OpenClaw 龙虾场景选 GLM-5-Turbo。模型能力更新频繁，建议定期复测核心用例。