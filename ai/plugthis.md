## 产品与定位

PlugThis 是"Lovable，但专为 Chrome 扩展而生"的 AI 构建器：聊天描述需求，约 2 分钟生成一个可安装的 Manifest V3 Chrome 扩展（popup、background service worker、content scripts 自动接线），内置真实 Supabase 后端（Postgres、鉴权、Edge Functions）与 OpenAI/Anthropic/Gemini 模型接入，可直接上架 Chrome Web Store。代码 100% 归你所有，随时导出 ZIP。

与通用 Web 应用构建器的区别：它理解 Chrome 扩展的目录结构与规范（manifest.json、内容脚本注入、后台服务），这是 Lovable/Bolt/v0 做不到的。适合想做浏览器扩展的独立开发者、营销团队与代理机构。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 专用扩展生成 | 生成完整 Manifest V3 结构，两分钟出可用构建 |
| 真实后端 | Supabase（Postgres + Auth + Edge Functions）随附，支持账号与数据 |
| 模型集成 | 扩展可用任意 BYOK 模型（OpenAI/Anthropic/Gemini） |
| 代码所有权 | 100% 源码归你，可导出 ZIP、取消订阅仍保留 |
| 上架协助 | 直接打包发布 Chrome Web Store |
| 人工支持 | Builder 档提供卡壳时的一对一通话协助 |

## 定价

| 套餐 | 价格 | 说明 |
| --- | --- | --- |
| Starter | $9.99/月 | 1 个扩展、无限聊天迭代、上架 Chrome Web Store、代码全拥有 |
| Builder | $29.99/月 | 3 个扩展、全栈（Supabase + LLM）、优先支持；PH 首发价 $9.99/月 × 3 个月 |

注意：AI 模型走 BYOK，OpenAI/Anthropic/Gemini 的推理费用另计，不包含在订阅内。

## 调用与兼容性

```bash
# 浏览器聊天构建；生成后导出 ZIP 或直接发布
# 本地加载开发版扩展
chrome://extensions → 开发者模式 → 加载已解压的扩展程序
```

- 平台：浏览器（聊天式构建）
- 引擎：Gemini 3.1 Pro（长上下文代码生成可靠）
- 兼容：Chrome / Edge（Chromium，Manifest V3）；Firefox 在路线图中但尚未支持

## 版本与更新注意

- 2026-07 上线 PH（当日 #3），被定位为 vibe-coding 分类的扩展生成细分冠军。
- 不支持 MCP（无法从 MCP 宿主驱动），也暂不支持 Firefox/Safari 打包。
- 扩展数量按套餐限制（Starter 1 个 / Builder 3 个），代理机构多客户场景消耗快。
- 月构建量随账单周期重置，超出需升级。

## 选型建议

- 目标明确是"Chrome 扩展"（含企业内部分发）：PlugThis 是当前唯一专注此场景的 AI 构建器，比通用工具快一个量级。
- 通用 Web 应用需求请回到 Lovable / Bolt.new / v0，它们不产出扩展结构。
- 在意成本：订阅便宜但模型推理另算（BYOK），估算 API 费用后再入。
- 需要跨浏览器（Firefox/Safari）覆盖的团队：暂不满足，等 Firefox 支持或走传统开发。