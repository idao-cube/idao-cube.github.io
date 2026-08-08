## 产品与定位

开源的 LLM 应用编排平台，通过可视化拖拽构建 AI 应用和工作流。20K+ GitHub Stars，支持 200+ 模型集成，适合快速构建 AI Agent、客服机器人、内容生成系统等。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 可视化编排 | 拖拽节点构建工作流，无需代码 |
| 多模型支持 | OpenAI、Claude、Gemini、DeepSeek、本地模型等 |
| 知识库 | 文档上传、自动分块、向量化、RAG 检索 |
| Agent 构建 | Tool Use、迭代节点、函数调用 |
| API 发布 | 一键发布为 API，快速集成 |
| 团队协作 | 多用户协作、权限管理 |

## 常用节点类型

| 节点 | 作用 |
| --- | --- |
| LLM 节点 | 模型调用，支持系统推理模型 |
| Parameter Extractor | 从自然语言提取结构化参数 |
| Template | Jinja2 模板格式化输出 |
| Iteration | 循环执行子工作流 |
| HTTP Request | 调用外部 API |
| Knowledge Retrieval | 知识库检索增强 |

## 调用与兼容性

```bash
# Docker 快速部署
docker run -it --rm \
  -p 80:80 \
  -v ~/.dify:/dify/storage \
  dify/dify-community
```

## 版本与更新注意

活跃维护中，版本迭代较快。支持向量数据库扩展（Elasticsearch、Milvus、pgvector 等）。

## 选型建议

需要快速构建 AI 应用原型、客服机器人、多平台内容生成首选 Dify；开源可自托管，数据完全自主可控。