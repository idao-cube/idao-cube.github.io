> 项目地址：[github.com/Open-Dev-Society/OpenStock](https://github.com/Open-Dev-Society/OpenStock) | 11.2K Stars | AGPL-3.0 协议
>
> 在线体验：[openstock-ods.vercel.app](https://openstock-ods.vercel.app)

## 一、OpenStock 是什么？

**OpenStock** 是一个开源的股票市场追踪平台，由 **Open Dev Society** 社区维护，定位为**付费股票工具的免费开源替代品**。

它提供实时行情追踪、自定义监控、公司基本面分析等功能——而且完全免费。

> 核心理念："技术应该属于每个人。"

### 核心亮点速览

| 特性 | 说明 |
|---|---|
| **开源免费** | AGPL-3.0 协议，代码完全开放 |
| **实时行情** | 集成 Finnhub API + TradingView 图表 |
| **智能监控** | 自选股监控、签到提醒 |
| **AI 日报** | 每日邮件推送，基于 Gemini 生成摘要 |
| **个性签到** | 首次注册 AI 生成欢迎邮件 |
| **市场概览** | 热力图、报价、头条新闻 |
| **全局搜索** | Cmd+K 命令面板，快速查找股票 |
| **技术栈** | Next.js 15 + React 19 + TypeScript + MongoDB |

---

## 二、技术栈

| 层级 | 技术选型 |
|---|---|
| **框架** | Next.js 15（App Router） |
| **UI 库** | React 19 |
| **语言** | TypeScript（91.7%） |
| **样式** | Tailwind CSS v4 + shadcn/ui + Radix UI |
| **图标** | Lucide |
| **数据库** | MongoDB + Mongoose |
| **认证** | Better Auth（邮箱/密码 + MongoDB 适配器） |
| **行情数据** | Finnhub API |
| **图表** | TradingView 嵌入式组件 |
| **后台任务** | Inngest（cron + AI 推理） |
| **AI** | Google Gemini API（欢迎邮件 + 日报摘要） |
| **邮件** | Nodemailer（Gmail） |
| **容器** | Docker + docker-compose |

---

## 三、核心功能

### 3.1 用户认证

- 邮箱/密码注册和登录（Better Auth）
- 受保护路由通过 Next.js 中间件实现
- 签到页面收集用户信息：国家、投资目标、风险承受能力、偏好行业

### 3.2 全局搜索

- `Cmd+K` / `Ctrl+K` 唤起命令面板
- 基于 Finnhub 的股票搜索
- 空闲时显示热门股票推荐
- 支持防抖查询

### 3.3 自选股监控

- 每个用户可以创建自选股列表
- 数据存储在 MongoDB（每个用户每只股票唯一）
- 支持添加和移除

### 3.4 股票详情页

每只股票提供完整的交易视图：

- **TradingView 组件**：实时报价、K 线图、基线指标、技术指标
- **公司信息**：公司简介、财务数据
- **情绪分析**（可选）：通过 Adanos 整合 Reddit、X（Twitter）、新闻、Polymarket 的跨源情绪数据

### 3.5 市场概览

- **热力图**：市场整体表现一目了然
- **实时报价**：精选股票的最新价格
- **头条新闻**：市场相关的 Top Stories

### 3.6 AI 邮件自动化

OpenStock 的两大 AI 邮件功能是它的突出亮点：

**AI 个性化欢迎邮件**
- 用户创建时自动触发
- 使用 Gemini 生成个性化的欢迎介绍
- 通过 Inngest 后台任务执行

**每日新闻摘要邮件**
- Cron 定时：每天 UTC 12:00 执行
- 根据用户自选股列表个性化生成
- 同样由 Gemini + Inngest 驱动

---

## 四、快速安装

### 前置依赖

- Node.js 20+
- pnpm 或 npm
- MongoDB 连接字符串（Atlas 或本地 Docker）
- Finnhub API Key
- Gmail 账号（或其他 SMTP）
- 可选：Google Gemini API Key

### 安装步骤

```bash
# 克隆
git clone https://github.com/Open-Dev-Society/OpenStock.git
cd OpenStock

# 安装依赖
pnpm install

# 配置环境变量（参考下方说明）
cp .env.example .env

# 验证数据库连接
pnpm test:db

# 启动开发服务器
pnpm dev

# 启动后台任务（另一终端）
npx inngest-cli@latest dev
```

访问 `http://localhost:3000`。

### Docker 部署

```yaml
# docker-compose.yml 包含两个服务
# - openstock（应用）
# - mongodb（MongoDB 7 + 持久化卷）

# 启动
docker compose up -d mongodb
docker compose up -d --build
```

本地连接字符串：`mongodb://root:example@mongodb:27017/openstock?authSource=admin`

---

## 五、配置说明

### 必需环境变量

| 变量 | 说明 |
|---|---|
| `NODE_ENV` | 通常设为 "development" |
| `MONGODB_URI` | Atlas 或本地 Docker URI |
| `BETTER_AUTH_SECRET` | Better Auth 密钥 |
| `BETTER_AUTH_URL` | 如 `http://localhost:3000` |
| `NEXT_PUBLIC_FINNHUB_API_KEY` | Finnhub API 密钥（公开到浏览器） |
| `FINNHUB_BASE_URL` | `https://finnhub.io/api/v1` |
| `NODEMAILER_EMAIL` | Gmail 地址 |
| `NODEMAILER_PASSWORD` | Gmail 应用密码 |

### 可选环境变量

| 变量 | 用途 |
|---|---|
| `ADANOS_API_KEY` | 市场情绪分析 |
| `GEMINI_API_KEY` | AI 欢迎邮件生成 |
| `AI_PROVIDER` | "gemini"（默认）、"minimax" 或 "siray" |
| `INNGEST_SIGNING_KEY` | Vercel 部署必需 |

---

## 六、项目架构

```
openstock/
├── app/
│   ├── (auth)/          # 登录/注册页面
│   ├── (root)/          # 主页面 + stock/[symbol] 详情
│   └── api/inngest/     # Inngest 路由处理器
├── components/
│   ├── ui/              # shadcn/Radix 基础组件
│   ├── forms/           # 表单组件
│   ├── Header.tsx       # 页面头部
│   ├── Footer.tsx       # 页面底部
│   ├── SearchCommand.tsx # 全局搜索（Cmd+K）
│   └── WatchlistButton.tsx # 自选股按钮
├── database/
│   ├── models/          # Mongoose Schema
│   └── mongoose.ts      # 数据库连接
├── lib/
│   ├── actions/         # Server Actions
│   ├── better-auth/     # 认证配置
│   ├── inngest/         # 后台任务 + AI Prompts
│   └── nodemailer/      # 邮件发送
├── scripts/             # 工具脚本
└── types/               # 类型定义
```

---

## 七、数据来源与集成

| 服务 | 用途 |
|---|---|
| **Finnhub** | 股票搜索、公司档案、市场新闻。免费版返回延迟数据 |
| **TradingView** | 嵌入式图表、热力图、报价、时间线 |
| **Better Auth** | 邮箱/密码认证 + MongoDB 会话管理 |
| **Inngest** | 用户创建事件（欢迎邮件）、每日 cron（日报邮件） |
| **Google Gemini** | AI 生成个性化内容 |
| **Nodemailer** | Gmail 邮件发送（可配置其他 SMTP） |
| **Adanos**（可选） | Reddit / X / 新闻 / Polymarket 情绪分析 |

---

## 八、与其他股票追踪工具对比

| 维度 | OpenStock | Yahoo Finance | TradingView | 同花顺 |
|---|---|---|---|---|
| **开源** | ✅ AGPL-3.0 | ❌ 闭源 | ❌ 闭源 | ❌ 闭源 |
| **免费** | ✅ 完全免费 | ✅ 基础免费 | ⚠️ 付费版 | ✅ 基础免费 |
| **AI 日报** | ✅ Gemini + 自选股个性化 | ❌ | ❌ | ❌ |
| **自托管** | ✅ 可自部署 | ❌ | ❌ | ❌ |
| **隐私** | ✅ 数据自控 | ❌ | ❌ | ❌ |
| **技术栈** | Next.js 15 + React 19 | — | — | — |
| **Stars** | 11.2K | — | — | — |
| **协议** | AGPL-3.0 | 闭源 | 闭源 | 闭源 |

---

## 九、适用场景

### 个人投资者

- **免费替代**：不想为 TradingView/Yahoo Finance 付费的高级功能付费
- **自托管隐私**：不想把自选股数据交给第三方平台
- **AI 辅助**：通过 Gemini 日报了解市场动向

### 开发者

- **学习参考**：Next.js 15 + React 19 + shadcn/ui 的最佳实践项目
- **二次开发**：基于 AGPL-3.0 协议扩展自己的金融工具
- **架构参考**：Better Auth + Inngest 后台任务 + AI 集成的完整示例

### 开源社区

- 11.2K Stars 的开源金融项目，社区活跃
- 完整的 CI/CD、Docker 部署、环境变量管理
- 欢迎贡献和 fork

---

## 十、Open Dev Society 理念

OpenStock 背后的 **Open Dev Society** 遵循一套明确的宣言：

- 技术和工具应面向专业人士和学生，不应设障碍
- 知识平台应永远保持免费
- 社区应引导而非评判初学者
- 通过透明度、捐赠和社区力量获得资金

> "Nothing here is financial advice." —— 项目明确声明不构成投资建议。

---

## 十一、总结

OpenStock 是一个功能完整的开源股票追踪平台。它用现代技术栈（Next.js 15 + React 19 + TypeScript + MongoDB）实现了付费股票工具的核心功能——实时行情、自选股监控、公司分析——同时通过 Gemini AI 提供了个性化的邮件日报。

对于不想依赖付费平台的个人投资者，或者想学习完整 Next.js 全栈项目的开发者来说，OpenStock 都值得关注。

**快速开始：**

```bash
git clone https://github.com/Open-Dev-Society/OpenStock.git
cd OpenStock
pnpm install
# 配置 .env 后
pnpm dev
```

> 技术栈：Next.js 15 + React 19 + TypeScript 92% + MongoDB + Tailwind CSS v4 | 协议：AGPL-3.0
>
> 在线体验：[openstock-ods.vercel.app](https://openstock-ods.vercel.app)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)