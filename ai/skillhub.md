## 工具与定位

SkillHub（skillhub.cn）是腾讯云推出的专为中国用户优化的 AI Skills 社区平台，帮助个人和企业发现、安装、发布和商业化 AI 能力。平台收录 **2.2 万+ 技能**（含 ClawHub 生态资源），通过国内高速镜像实现秒速安装，采用三线并行安全审核保障技能安全。个人开发者可将经验沉淀为可复用 Skill 分享给社区；企业可在专属团队空间中沉淀内部能力，或将付费服务封装为 **Pay Skill** 面向 Agent 生态售卖。首发 **TRACE 评测体系**帮助识别高质量 Skill。适合国内开发者、团队与希望商业化技能的机构。

## 核心能力

| 能力 | 说明 |
| --- | --- |
| 技能市场 | 2.2 万+ Skills，精选 Top 50 榜单（自动化办公/智能编程/数据分析等） |
| 高速下载 | 国内镜像秒装，本土化加速，中文搜索优化 |
| 安全审核 | 三线并行安全审核机制 |
| 团队空间 | 企业认证后私有/公开 Skill 管理、企业专区与品牌展示页 |
| SkillPay | 付费 Skill 按次计费，微信支付 Agent Pay X402 协议结算 |
| TRACE 评测 | 首发评测体系识别高质量 Skill |
| 技能分类 | AI 智能 / 开发工具 / 效率提升 / 数据分析 / 内容创作 / 安全合规 / 通讯协作 |

## 平台兼容

| 平台 | 说明 |
| --- | --- |
| WorkBuddy | 腾讯云个人 AI 助手，SkillHub CLI 安装管理，直接调用已装 Skills |
| QClaw | 腾讯云 AI 智能体框架，对接 ClawHub 生态，兼容开源 Skills 与 MCP Server 协议 |
| ima | 腾讯 AI 知识管理平台，通过知识号发布和发现 Skill |
| Claude Code | CLI 默认安装到 ~/.claude/skills/ 目录 |
| Cursor | 安装到 ~/.cursor/skills/，斜杠命令调用 |

## 使用方式

| 方式 | 说明 |
| --- | --- |
| 终端安装 CLI | `curl -fsSL https://skillhub-1251783334.cos.ap-guangzhou.myqcloud.com/install/install.sh | bash` |
| Agent 提示安装 | 发送"根据 https://skillhub.cn/install/skillhub.md 安装 SkillHub 商店"给 Agent |
| 网页浏览 | 精选 Top 50、分类、全部技能页检索 |

## 版本与更新注意

SkillHub 由腾讯云运营，Skills 数量（早期 1.3 万 ClawHub 口径 → 现 2.2 万+）持续增长；Pay Skill 商业化需完成企业认证、商户入驻与微信商户号绑定；技能来源包括社区与第三方，安装前建议查看安全审核与评测信息。

## 选型建议

中文团队与企业场景首选 SkillHub：腾讯云生态集成（WorkBuddy/QClaw/ima）、高速镜像与 SkillPay 商业化是差异化优势；个人技能探索可配合 CocoLoop Hub、SkillsMP 互补覆盖。