你是不是也有这样的困扰：

- 用 Claude Code 写代码，总觉得它不够懂需求，生成的内容还要反复修改
- 想让它做个 Dashboard / 落地页，结果生成的界面丑到没法直接用
- 想让它读写本地文件、操作浏览器、联动设计工具，却完全不知道怎么配置
- 会话一清空，之前的调试经验、项目细节全忘了，每次都要重新铺垫上下文

这篇文章整理了 **32 个精选 Skills + 8 个 MCP 服务器**，全部分享给你。一键安装、自动触发，让你的 Claude Code 从「只会补代码的助手」，变成「能帮你搞定全流程的开发搭档」。

---

## 一、先搞懂核心：Skills vs MCP 到底有啥区别？

很多新手刚上手会搞混这两个扩展能力，先一句话讲透：

- **Skills**：封装好的提示词 / 标准化工作流，让 Claude 变成特定领域的「专业人士」，本质是让 AI **更懂怎么干**
- **MCP 服务器**：真正的工具能力，能让 Claude 访问本地文件、浏览器、外部 API、第三方工具，本质是让 AI **真的能去干**

> 绝大多数能力都会**自动触发**，你不需要手动调用。当你说「帮我写个 README」「审查一下这段代码」时，Claude Code 会自动激活对应的能力。

### 核心区别对照表

| 对比维度 | Skills 技能 | MCP 服务器 |
|---|---|---|
| 核心本质 | 提示词 / 标准化工作流封装 | 本地运行的工具 / API 服务 |
| 安装方式 | `npx skills add` 一键安装 | 修改 `mcp.json` 配置文件 |
| 运行位置 | Claude 大模型内部 | 本地独立进程 |
| 访问外部资源 | 不支持 | 支持本地系统、浏览器、第三方服务 |
| 额外依赖 | 仅需 node 环境，无需 API Key | 部分服务需要 API Key |

**一句话总结：Skills 让 Claude 更聪明，MCP 让 Claude 更能干，两者搭配使用才能把能力拉满。**

---

## 二、Skills 技能全指南（32 个精选）

技能是 Claude Code 最轻量化的扩展方式，通过 Skills CLI 即可一键安装，类似前端常用的 npm 包管理器。

### 2.1 前置必看：技能安装与管理

#### 核心安装命令

```bash
# 1. 搜索社区技能（关键词匹配）
npx skills find <关键词>

# 2. 安装技能（-y 跳过确认，-g 全局安装，必加！）
npx skills add <owner/repo@skill> -y -g

# 3. 查看已安装的全部技能
npx skills list -g

# 4. 检查技能更新
npx skills check

# 5. 更新所有已安装技能
npx skills update
```

> 关键提醒：安装技能**必须加 `-g` 全局安装参数**，否则 Claude Code 无法识别；安装完成后**必须重启 Claude Code** 才能生效。

#### 技能市场

所有社区开源技能均可在官方市场查看：[skills.sh](https://skills.sh/)，有完整的安装量排行榜，帮你快速筛选热门优质技能。

#### 已安装技能查看方式

```bash
# Mac/Linux
ls ~/.claude/skills/
ls ~/.agents/skills/

# Windows
dir C:\Users\你的用户名\.claude\skills\
```

Claude Code 内直接输入 `/` 即可唤起完整技能列表，点击即可手动触发。

---

### 2.2 32 个精选技能分类清单

所有技能按开发场景分类，标注安装命令、核心能力和触发场景。

#### 🔧 必装入口类（1 个）

**find-skills — 技能发现神器**
- 安装：`npx skills add find-skills -y -g`
- 能力：技能市场的内置搜索引擎，支持关键词匹配、热门推荐
- 触发：当你说「有没有处理 PDF 的技能」时自动激活
- 感受：所有新手第一个必装的技能，相当于给 Claude 装了个「应用商店」

#### 🎨 前端开发全栈类（9 个）

| 技能 | 安装命令 | 核心能力 | 安装量 |
|---|---|---|---|
| **frontend-design** | `npx skills add frontend-design -y -g` | 网页/Dashboard/落地页设计 | 52.7K |
| **web-artifacts-builder** | `npx skills add web-artifacts-builder -y -g` | 复杂 SPA、带组件库的前端项目 | - |
| **canvas-design** | `npx skills add canvas-design -y -g` | 架构图、流程图、可视化绘图 | 6.1K |
| **theme-factory** | `npx skills add theme-factory -y -g` | 主题美化、视觉风格统一 | - |
| **vercel-react-best-practices** | `npx skills add vercel-labs/agent-skills@vercel-react-best-practices -y -g` | React 开发最佳实践 | 109.8K |
| **web-design-guidelines** | `npx skills add vercel-labs/agent-skills@web-design-guidelines -y -g` | 网页设计规范与 UI 优化 | 83.1K |
| **vercel-composition-patterns** | `npx skills add vercel-labs/agent-skills@vercel-composition-patterns -y -g` | 组件组合模式与复用策略 | 29.7K |
| **shadcn** | `npx skills add shadcn/ui@shadcn -y -g` | shadcn/ui 组件库专属支持 | - |
| **vercel-react-native-skills** | `npx skills add vercel-labs/agent-skills@vercel-react-native-skills -y -g` | React Native 开发指导 | 21.6K |

#### 📄 文档与办公处理类（6 个）

| 技能 | 安装命令 | 核心能力 |
|---|---|---|
| **technical-writer** | `npx skills add technical-writer -y -g` | 标准化 README、API 文档、技术教程 |
| **doc-coauthoring** | `npx skills add doc-coauthoring -y -g` | 技术提案（RFC）、系统设计文档 |
| **docx** | `npx skills add docx -y -g` | Word 文档创建、编辑、格式转换 |
| **pptx** | `npx skills add pptx -y -g` | PPT 演示文稿生成与编辑 |
| **pdf** | `npx skills add pdf -y -g` | PDF 合并/拆分/OCR/水印 |
| **xlsx** | `npx skills add xlsx -y -g` | Excel 数据清洗、公式计算、图表 |

#### 🏗️ 架构设计与代码质量类（5 个）

| 技能 | 安装命令 | 核心能力 |
|---|---|---|
| **planning-with-files** | `npx skills add planning-with-files -y -g` | 复杂任务拆解、进度追踪、会话恢复 |
| **project-planner** | `npx skills add shubhamsaboo/awesome-llm-apps@project-planner -y -g` | 需求文档、架构设计、开发计划 |
| **architecture-patterns** | `npx skills add wshobson/agents@architecture-patterns -y -g` | 架构模式推荐与设计指导 |
| **architecture-decision-records** | `npx skills add wshobson/agents@architecture-decision-records -y -g` | ADR 架构决策记录生成 |
| **requesting-code-review** | `npx skills add obra/superpowers@requesting-code-review -y -g` | 专业代码审查、质量优化 |

#### 🧠 记忆与上下文管理类（3 个）

| 技能 | 安装命令 | 核心能力 |
|---|---|---|
| **memory-intake** | `npx skills add memory-intake -y -g` | 经验/踩坑记录/项目规范写入记忆库 |
| **memory-audit** | `npx skills add memory-audit -y -g` | 记忆库健康检查、无效内容清理 |
| **memory-evolution** | `npx skills add memory-evolution -y -g` | 记忆库优化、关联结构整理 |

#### 🧪 测试与自动化类（2 个）

| 技能 | 安装命令 | 核心能力 |
|---|---|---|
| **webapp-testing** | `npx skills add webapp-testing -y -g` | 基于 Playwright 的 E2E 测试 |
| **test-driven-development** | `npx skills add obra/superpowers@test-driven-development -y -g` | TDD 红绿重构循环引导 |

#### ⚡ 开发提效类（4 个）

| 技能 | 安装命令 | 核心能力 |
|---|---|---|
| **brainstorming** | `npx skills add obra/superpowers@brainstorming -y -g` | 多角度分析，快速生成方案 |
| **systematic-debugging** | `npx skills add obra/superpowers@systematic-debugging -y -g` | 结构化 bug 排查、根因定位 |
| **writing-plans** | `npx skills add obra/superpowers@writing-plans -y -g` | 开发任务拆解、实施计划 |
| **executing-plans** | `npx skills add obra/superpowers@executing-plans -y -g` | 按计划分步执行、进度追踪 |

#### 🔒 安全审计类（1 个）

- **audit-website**：`npx skills add squirrelscan/skills@audit-website -y -g` — 网站安全漏洞扫描，安装量 15.3K

#### 🛠️ 自定义技能开发类（1 个）

- **skill-creator**：`npx skills add skill-creator -y -g` — 引导创建自定义技能，封装重复工作流

---

### 2.3 拿来即用：分场景一键安装脚本

#### 新手入门包（10 个必装）

```bash
#!/bin/bash
set -e

npx skills add find-skills -y -g
npx skills add frontend-design -y -g
npx skills add technical-writer -y -g
npx skills add docx -y -g
npx skills add pptx -y -g
npx skills add pdf -y -g
npx skills add obra/superpowers@requesting-code-review -y -g
npx skills add obra/superpowers@brainstorming -y -g
npx skills add obra/superpowers@systematic-debugging -y -g

echo "✅ 新手入门包安装完成！重启 Claude Code 即可生效"
```

#### 前端开发者专属包

```bash
#!/bin/bash
set -e

npx skills add find-skills -y -g
npx skills add frontend-design -y -g
npx skills add web-artifacts-builder -y -g
npx skills add canvas-design -y -g
npx skills add theme-factory -y -g
npx skills add vercel-labs/agent-skills@vercel-react-best-practices -y -g
npx skills add vercel-labs/agent-skills@web-design-guidelines -y -g
npx skills add vercel-labs/agent-skills@vercel-composition-patterns -y -g
npx skills add shadcn/ui@shadcn -y -g
npx skills add webapp-testing -y -g
npx skills add obra/superpowers@requesting-code-review -y -g
npx skills add obra/superpowers@systematic-debugging -y -g
npx skills add technical-writer -y -g

echo "✅ 前端开发者专属包安装完成！重启 Claude Code 即可生效"
```

#### 全栈开发全能包（全部 32 个）

```bash
#!/bin/bash
set -e

# 🔧 必装入口
npx skills add find-skills -y -g

# 🎨 前端开发全栈类
npx skills add frontend-design -y -g
npx skills add web-artifacts-builder -y -g
npx skills add canvas-design -y -g
npx skills add theme-factory -y -g
npx skills add vercel-labs/agent-skills@vercel-react-best-practices -y -g
npx skills add vercel-labs/agent-skills@web-design-guidelines -y -g
npx skills add vercel-labs/agent-skills@vercel-composition-patterns -y -g
npx skills add shadcn/ui@shadcn -y -g
npx skills add vercel-labs/agent-skills@vercel-react-native-skills -y -g

# 📄 文档办公
npx skills add technical-writer -y -g
npx skills add doc-coauthoring -y -g
npx skills add docx -y -g
npx skills add pptx -y -g
npx skills add pdf -y -g
npx skills add xlsx -y -g

# 🏗️ 架构与代码质量
npx skills add planning-with-files -y -g
npx skills add shubhamsaboo/awesome-llm-apps@project-planner -y -g
npx skills add wshobson/agents@architecture-patterns -y -g
npx skills add wshobson/agents@architecture-decision-records -y -g
npx skills add obra/superpowers@requesting-code-review -y -g

# 🧠 记忆管理
npx skills add memory-intake -y -g
npx skills add memory-audit -y -g
npx skills add memory-evolution -y -g

# 🧪 测试自动化
npx skills add webapp-testing -y -g
npx skills add obra/superpowers@test-driven-development -y -g

# ⚡ 开发提效
npx skills add obra/superpowers@brainstorming -y -g
npx skills add obra/superpowers@systematic-debugging -y -g
npx skills add obra/superpowers@writing-plans -y -g
npx skills add obra/superpowers@executing-plans -y -g

# 🔒 安全审计
npx skills add squirrelscan/skills@audit-website -y -g

# 🛠️ 自定义技能
npx skills add skill-creator -y -g

echo "✅ 全栈开发全能包安装完成！重启 Claude Code 即可生效"
```

---

## 三、MCP 服务器全指南（8 个亲测可用）

MCP（Model Context Protocol）是 Claude Code 更底层的扩展机制，能让 Claude 突破大模型的限制，真正访问本地文件系统、浏览器、数据库、第三方工具。

### 3.1 前置必看：MCP 配置全流程

#### 配置文件路径

```bash
# 全局配置（所有项目生效，推荐）
# Mac/Linux
~/.claude/mcp.json
~/.claude/mcp.local.json

# Windows
C:\Users\你的用户名\.claude\mcp.json

# 项目级配置（仅当前项目生效）
项目根目录/.mcp.json
```

#### 标准配置格式

```json
{
  "mcpServers": {
    "服务器名称": {
      "command": "执行命令",
      "args": ["命令参数"],
      "env": {
        "环境变量名": "环境变量值"
      }
    }
  }
}
```

> ⚠️ 修改配置文件后**必须重启 Claude Code** 才能生效。生效后，输入框下方会显示「工具」图标，点击即可查看已连接的 MCP 服务器。

---

### 3.2 8 个精选 MCP 服务器清单

#### 🧠 neural-memory — 神经网络记忆系统

解决痛点：技能里的记忆功能是轻量版，需要更强大的跨会话记忆能力。

```bash
# 安装（二选一）
pip install neural-memory     # Python 方式（推荐）
npm install -g neural-memory  # Node 方式
```

```json
{
  "mcpServers": {
    "neural-memory": {
      "command": "neural-memory",
      "args": ["--mcp"]
    }
  }
}
```

核心能力：跨会话长期记忆、神经元结构化记忆模型、知识图谱可视化。纯本地运行，无需 API Key。

#### 🌐 playwright — 浏览器自动化

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}
```

全浏览器支持，可执行 E2E 测试、页面自动化、视频录制。无需 API Key。

#### 📁 filesystem — 文件系统访问

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/你的用户名/开发工作区路径"]
    }
  }
}
```

> ⚠️ 严禁开放系统根目录，仅授权开发工作区目录即可。

#### 🤔 sequential-thinking — 链式推理增强

```json
{
  "mcpServers": {
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    }
  }
}
```

把复杂问题拆成多步结构化推理，处理复杂算法、架构设计问题时效果拔群。

#### 🌐 web-reader — 网页内容抓取

```json
{
  "mcpServers": {
    "web_reader": {
      "command": "npx",
      "args": ["-y", "web-reader-mcp"]
    }
  }
}
```

抓取公开网页内容并转为 Markdown，自动过滤广告。仅能访问公开页面。

#### 🎨 figma-developer-mcp — Figma 设计稿读取

```json
{
  "mcpServers": {
    "figma-developer-mcp": {
      "command": "npx",
      "args": ["-y", "figma-developer-mcp", "--stdio"],
      "env": {
        "FIGMA_API_KEY": "你的 Figma Personal Access Token"
      }
    }
  }
}
```

自动提取颜色、字体、尺寸等设计规范，生成 HTML/CSS 代码。需 Figma API Key。

#### 🎨 supercharged-figma — Figma 实时编辑

```json
{
  "mcpServers": {
    "supercharged-figma": {
      "command": "npx",
      "args": ["-y", "supercharged-figma-mcp", "--local", "--relay-host", "127.0.0.1", "--relay-port", "8888"]
    }
  }
}
```

可在 Figma 画布上实时创建、删除、修改图层，无需 API Key。需配合 Figma 插件使用。

#### 🖼️ 4_5v_mcp — AI 图片分析

```json
{
  "mcpServers": {
    "4_5v_mcp": {
      "command": "npx",
      "args": ["-y", "4_5v_mcp"]
    }
  }
}
```

分析图片内容、识别 UI 组件、描述图片场景。部分版本可能需要 API Key。

---

### 3.3 API Key 汇总表

| MCP 服务器 | 需要 API Key？ | 备注 |
|---|---|---|
| neural-memory | ❌ | 纯本地 |
| playwright | ❌ | 纯本地 |
| filesystem | ❌ | 纯本地 |
| sequential-thinking | ❌ | 纯本地 |
| web-reader | ❌ | 纯本地 |
| figma-developer-mcp | ✅ | Figma Token |
| supercharged-figma | ❌ | 需 Figma 插件 |
| 4_5v_mcp | ⚠️ | 视实现而定 |

---

## 四、亲测踩坑避坑指南

### Skills 安装与使用高频坑

1. **安装超时/失败**：确保网络稳定，能正常访问 npm 官方源
2. **安装了不显示**：必须加 `-g` 参数，安装后重启 Claude Code
3. **技能不自动触发**：检查提问关键词是否匹配，或手动输入 `/` + 技能名
4. **Claude 响应变慢**：不要一次性装超过 20 个冗余技能，按需安装即可
5. **技能更新失败**：更新前先关闭 Claude Code，避免文件占用

### MCP 配置与生效高频坑

1. **配置完不生效**：先检查 JSON 格式是否正确，然后重启 Claude Code
2. **文件系统访问失败**：仅指定工作区目录，检查目录读写权限
3. **Figma MCP 连不上**：确认 Token 权限、网络可访问 Figma
4. **工具调用报错**：检查 MCP 依赖是否提前安装完成
5. **Windows 路径问题**：路径分隔符用 `\`，不要用 `/`

### 安全红线

- 文件系统 MCP **严禁开放系统根目录**
- 第三方工具优先选择官方、社区热门的开源项目
- API Key 不要提交到 Git 仓库
- 不要给 Claude 过高系统权限

---

## 五、总结与新手推荐安装顺序

Skills 和 MCP 是两套互补的扩展机制，搭配使用才能把 Claude Code 的能力发挥到极致。

**新手推荐安装顺序**：

1. 先装 `find-skills` — 相当于装了应用商店
2. 安装新手入门包的 10 个必装技能，先上手体验
3. 配置 `neural-memory` MCP — 解决跨会话记忆丢失的核心痛点
4. 根据自己的开发场景，按需安装对应的技能和 MCP
5. 慢慢探索进阶玩法，开发自己的专属技能和 MCP

> 所有命令和配置均适配 Windows / Mac / Linux 全平台。