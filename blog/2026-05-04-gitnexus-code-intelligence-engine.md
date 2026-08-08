> 项目地址：[github.com/abhigyanpatwari/GitNexus](https://github.com/abhigyanpatwari/GitNexus) | 35.4K Stars | PolyForm Noncommercial 1.0.0 协议

## 一、GitNexus 是什么？

**GitNexus** 是一个"零服务器代码智能引擎"。它从你的代码库中构建**知识图谱**，通过 MCP（Model Context Protocol）让 AI Agent 获得结构化的代码理解能力。

官方描述：*"Zero-Server Code Intelligence Engine"*

> 传统方式下，AI 工具只能"看到"代码文本，却不理解代码之间的语义关系。GitNexus 在索引阶段就把代码的结构、依赖、调用链、执行流全部整理成知识图谱——AI 只需一次查询就能获取完整的代码上下文。

### 核心亮点速览

| 特性 | 说明 |
|---|---|
| **知识图谱** | 将代码库的结构、依赖、调用链预计算为图结构 |
| **完全本地** | CLI 模式下零网络调用，索引存储在本地 |
| **14 种语言** | TypeScript/JavaScript/Python/Java/Go/Rust/C++ 等 |
| **16 个 MCP 工具** | 查询、上下文、影响分析、检测变更、重命名等 |
| **MCP 协议** | 标准 Model Context Protocol 接入任意 AI 编辑器 |
| **Web UI** | 浏览器端可视化图谱浏览器，代码不离机 |
| **多仓库** | 支持多仓库分组管理与跨仓库查询 |
| **混合搜索** | BM25 + 语义向量 + RRF 融合排序 |
| **Agent 技能** | 自动安装代码技能到 Claude Code |

---

## 二、工作原理

### 2.1 索引流水线

GitNexus 在索引阶段对代码库进行深度分析，流程如下：

```text
源代码
  ↓
1. 结构分析 —— 遍历文件树，映射目录/文件关系
2. AST 解析 —— Tree-sitter 提取语法树中的符号
3. 依赖解析 —— 解析 imports、调用、继承关系
4. 社区检测 —— 将相关符号聚类为功能社区
5. 执行流追踪 —— 从入口点追踪调用链
6. 混合搜索索引 —— BM25 + 语义向量 + RRF 融合
  ↓
知识图谱（.gitnexus/）
```

这些工作**在索引时一次性完成**，所以查询时无需 AI 反复拼凑上下文——一次调用就能获得完整信息。

### 2.2 与传统 Graph RAG 的区别

| 对比 | GitNexus | 传统 Graph RAG |
|---|---|---|
| **计算时机** | 索引时预计算结构分析 | 查询时实时推理 |
| **查询次数** | 单次调用获取完整上下文 | 多次查询拼凑信息 |
| **结果质量** | 结构化、完整、确定 | 碎片化、受限于 LLM 推理 |
| **适用模型** | 小模型也能准确回答 | 依赖大模型推理能力 |

---

## 三、两种使用模式

### 3.1 CLI + MCP（推荐日常开发）

在本地索引代码库，运行 MCP 服务器，AI 编辑器通过 MCP 协议连接。所有数据本地运行，零网络请求。

### 3.2 Web UI（浏览器端）

完全在浏览器中运行的**可视化图谱浏览器 + AI 聊天**：

- 代码永远不会离开你的机器
- 支持 WebGL 图谱可视化（Sigma.js + Graphology）
- AI 聊天基于 LangChain ReAct

通过 `gitnexus serve` 的桥接模式，Web UI 还可以连接到本地后端，浏览所有已索引的仓库。

---

## 四、16 个 MCP 工具详解

GitNexus 提供了 16 个 MCP 工具，分为两类：

### 单仓库工具（11 个）

| 工具 | 功能 |
|---|---|
| `list_repos` | 发现所有已索引仓库 |
| `query` | 混合搜索（BM25 + 语义 + RRF） |
| `context` | 360° 符号全景视图（引用/参与流程） |
| `impact` | 影响范围分析（深度分组 + 置信度评分） |
| `detect_changes` | Git diff 影响映射 |
| `rename` | 多文件协调重命名 |
| `cypher` | 原始图谱查询 |
| `group_list` | 列出仓库分组 |
| `group_sync` | 同步分组 |
| `group_contracts` | 分组接口契约查询 |
| `group_query` | 跨仓库分组查询 |
| `group_status` | 分组状态检查 |

### Agent 技能（4 个）

GitNexus 自动安装 4 个 Agent 技能到 `.claude/skills/`：

| 技能 | 用途 |
|---|---|
| **Exploring** | 代码库探索 |
| **Debugging** | 调试辅助 |
| **Impact Analysis** | 变更影响分析 |
| **Refactoring** | 重构优化 |

`--skills` 标志还可通过 Leiden 社区检测生成**仓库专属的 SKILL.md** 文件。

---

## 五、快速安装

### 安装

```bash
npm install -g gitnexus
```

### 索引代码库

```bash
cd /path/to/your/project
npx gitnexus analyze
```

这一条命令完成全部工作：索引代码库 → 安装 Agent 技能 → 注册 Claude Code 钩子 → 创建 AGENTS.md/CLAUDE.md 上下文文件。

### 配置 MCP 编辑器

**Claude Code**（完整集成：MCP + 技能 + 钩子）：

```bash
claude mcp add gitnexus -- npx -y gitnexus@latest mcp
```

**Cursor**（`~/.cursor/mcp.json`）：

```json
{
  "mcpServers": {
    "gitnexus": {
      "command": "npx",
      "args": ["-y", "gitnexus@latest", "mcp"]
    }
  }
}
```

同样支持 **Codex**（MCP + 技能）、**Windsurf**（MCP）、**OpenCode**（MCP + 技能）。

> Claude Code 获得最深度的集成——PreToolUse 钩子在搜索时自动注入图谱上下文，PostToolUse 钩子在提交后检测索引是否过时。

---

## 六、命令行速查

### 核心命令

| 命令 | 用途 |
|---|---|
| `gitnexus setup` | 一次性 MCP 编辑器配置 |
| `gitnexus analyze [path]` | 索引代码库（支持 `--force`、`--skills`、`--embeddings`） |
| `gitnexus mcp` | 启动 MCP 服务器（stdio） |
| `gitnexus serve` | 启动 Web UI 桥接服务器 |
| `gitnexus list` | 列出所有已索引仓库 |
| `gitnexus wiki` | 从知识图谱生成文档 |
| `gitnexus group create/add/remove/sync/query` | 多仓库分组管理 |

---

## 七、支持的编程语言（14 种）

GitNexus 对以下语言提供完整支持——包括 imports、bindings、exports、继承、类型注解、构造函数推断、框架识别、入口点检测：

| 语言 | 状态 |
|---|---|
| **TypeScript** | ✅ 完整支持 |
| **JavaScript** | ✅ 完整支持 |
| **Python** | ✅ 完整支持 |
| **Java** | ✅ 完整支持 |
| **Kotlin** | ✅ 完整支持 |
| **C#** | ✅ 完整支持 |
| **Go** | ✅ 完整支持 |
| **Rust** | ✅ 完整支持 |
| **PHP** | ✅ 完整支持 |
| **Ruby** | ✅ 完整支持 |
| **Swift** | ✅ 完整支持 |
| **C** | ✅ 完整支持 |
| **C++** | ✅ 完整支持 |
| **Dart** | ✅ 完整支持 |

---

## 八、多仓库架构

每个 `gitnexus analyze` 在仓库内创建 `.gitnexus/` 目录（已加入 `.gitignore`），并在 `~/.gitnexus/registry.json` 注册指针。

MCP 服务器读取注册表后可以服务任何已索引的仓库：

- 数据库连接按需延迟打开
- 5 分钟无活动后自动驱逐
- 最多 5 个并发连接
- 只有一个仓库时，`repo` 参数可选

分组功能支持跨仓库的接口契约查询和影响分析。

---

## 九、Wiki 文档生成

GitNexus 可以从知识图谱中自动生成 LLM 驱动的文档：

```bash
gitnexus wiki
```

- 按模块生成文档页面
- 自动交叉引用
- 基于图谱结构确保覆盖率

---

## 十、技术栈

| 层级 | CLI 模式 | Web 模式 |
|---|---|---|
| **运行时** | Node.js（原生） | 浏览器（WASM） |
| **语法解析** | Tree-sitter native | Tree-sitter WASM |
| **数据库** | LadybugDB native | LadybugDB WASM |
| **嵌入向量** | HuggingFace transformers.js | transformers.js（WebGPU/WASM） |
| **搜索** | BM25 + 语义 + RRF 融合 | BM25 + 语义 + RRF 融合 |
| **Agent 接口** | MCP（stdio） | LangChain ReAct |
| **可视化** | — | Sigma.js + Graphology（WebGL） |
| **前端** | — | React 18 + TypeScript + Vite |
| **聚类** | Graphology | Graphology |

---

## 十一、企业版

通过 [akonlabs.com](https://akonlabs.com) 提供 SaaS 或自托管企业版：

| 功能 | 说明 |
|---|---|
| **PR Review** | 拉取请求的影响范围分析 |
| **自动更新 Wiki** | 代码变更后文档自动同步 |
| **自动重索引** | 监听文件变更自动更新 |
| **多仓库支持** | 增强版多仓库管理 |
| **OCaml 语言** | 额外语言支持 |
| **优先支持** | 功能/语言优先级定制 |

---

## 十二、Docker 部署

```bash
docker compose up -d
```

两个容器镜像，Cosign 密钥签名，支持 K8s 准入验证：

| 组件 | GHCR | Docker Hub |
|---|---|---|
| CLI/后端 | `ghcr.io/abhigyanpatwari/gitnexus:latest` | `akonlabs/gitnexus:latest` |
| Web UI | `ghcr.io/abhigyanpatwari/gitnexus-web:latest` | `akonlabs/gitnexus-web:latest` |

---

## 十三、总结

GitNexus 解决了 AI 编码工具的一个根本性难题：**如何让 AI 真正理解代码的结构和语义，而不只是看到代码文本。**

它的解法是在索引阶段预计算代码知识图谱——结构、依赖、调用链、执行流、语义关系——然后通过 MCP 协议提供 16 个结构化工具。AI 编辑器只需一次调用就能获取完整的代码上下文，而不是反复查询来拼凑信息。

对于使用 Claude Code、Cursor、Windsurf 等 AI 编辑器的开发者来说，GitNexus 是当前最强大的代码上下文增强工具之一。35.4K Stars 和 14 种语言支持也证明了社区的认可。

**快速开始：**

```bash
npm install -g gitnexus
cd your-project
npx gitnexus analyze
claude mcp add gitnexus -- npx -y gitnexus@latest mcp
```

> 技术栈：TypeScript + Tree-sitter + LadybugDB + MCP | Stars：35.4K
>
> 在线体验：[gitnexus.vercel.app](https://gitnexus.vercel.app) | Web UI 版完全在浏览器中运行，代码不上传

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)