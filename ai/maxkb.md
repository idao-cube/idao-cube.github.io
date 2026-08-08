## 产品与定位

MaxKB = Max Knowledge Brain，开源企业级智能体平台（20K+ GitHub Stars）。集成 RAG 流水线、强大工作流引擎与 MCP 工具调用，零代码快速构建智能客服、企业知识库与问答系统。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| RAG 流水线 | 文档上传/网页抓取、自动分块、向量化 |
| 工作流引擎 | 可视化流程编排，复杂业务场景支持 |
| MCP 工具 | 扩展工具生态，安全工具过滤 |
| 私有化部署 | Docker 一键部署，数据完全自主 |
| 多模型支持 | DeepSeek、Llama、Qwen 等私有模型 + OpenAI、Claude 等公网模型 |
| 零代码集成 | 嵌入第三方系统，无需开发 |

## 常用场景

| 场景 | 说明 |
| --- | --- |
| 智能客服 | 基于产品文档和 FAQ 的智能问答 |
| 企业知识库 | 内部制度、流程、规范的智能检索 |
| 学术研究 | 论文、文献的智能分析和问答 |
| 教育辅助 | 课程内容和习题的智能答疑 |

## 调用与兼容性

```bash
# Docker 一键部署
docker run -d \
  -p 8080:8080 \
  -v maxkb_data:/opt/maxkb/admin \
  1panel/maxkb

# 连接 GPUStack 模型
# 1. MaxKB 添加 Model -> 选择 Generic Proxy
# 2. 配置 API URL 和 Key
# 3. 添加 Embedding 和 Reranker 模型
```

## 版本与更新注意

v2.7+ 版本持续更新，支持最新模型和功能。建议通过 Docker 保持最新版本。

## 选型建议

企业知识库问答、智能客服、内部助手首选 MaxKB；配合 GPUStack 可构建完全私有化的企业 AI 平台。