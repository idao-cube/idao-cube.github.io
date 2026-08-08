## 产品与定位

Emdash（General Action 公司，YC W26，开源 Apache-2.0）是新一代 **Agentic Development Environment（ADE）**：一个桌面应用，让你并行运行多个编码 agent，每个任务各占一个独立的 Git worktree，互不干扰。它不绑定任何单一模型或厂商，而是做"agent 的调度台"——自动检测你本机已安装的各种 agent CLI（Claude Code、Codex、Cursor、OpenCode、Amp、Devin、Qwen Code、Droid、GitHub Copilot 等 25+），让它们在同一工作区里协作。

团队视角看，Emdash 把"人-工单-代码"串起来：可以从 Linear、GitHub Issues、Jira、GitLab、Asana、Monday 等直接把 issue 派给 agent，统一审查所有 agent 的 diff、一键建 PR、跟踪 CI、合并代码。数据本地优先存储，遥测可 opt-out，适合重视隐私与自主可控的开发者。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 并行多 agent | 多任务同时推进，每个任务独立 Git worktree，互不污染 |
| 25+ agent 支持 | 自动检测已安装的 Claude Code/Codex/Cursor/OpenCode/Amp/Devin/Copilot 等 CLI |
| 工单驱动 | 从 Linear/GitHub/Jira/GitLab/Asana/Monday 派发 issue 给 agent |
| 统一审查 | 集中查看各 agent 的 diff，批准后建 PR、查 CI、合并 |
| 远程运行 | 支持本地与 SSH 远程机器执行 agent |
| 浏览器预览 | 内置预览窗口查看 agent 产出的前端效果 |
| 调度任务 | 定时/重复执行 agent 任务 |
| 资源管理 | 集中管理 prompts、skills 与 MCP tools |

## 计费方式

| 方案 | 价格 | 说明 |
| --- | --- | --- |
| 开源版 | 免费 | 全功能，Apache-2.0 协议，macOS/Windows/Linux |

Emdash 本身不收取费用，agent 的模型推理成本由各 agent 自身的订阅/API 承担。

## 调用与兼容性

```bash
# 安装 Emdash 桌面应用后，它会自动扫描并接入本机已安装的 agent CLI
# 在项目中启动多个任务，Emdash 为每个任务创建独立 worktree
# 将 issue 拖入应用即可派发给指定 agent；完成后统一 review diff 并提 PR
```

支持 macOS / Windows / Linux；本地优先存储，遥测可关闭。

## 版本与更新注意

- 2026-05-20 在 Product Hunt 上线获当日第 3，仍处于早期（5.3K+ GitHub stars，2025-08 创建）。
- 依赖你本机已装好的 agent CLI——它自身不内置模型，价值在编排与工作区管理。
- 多 worktree 并行模式适合多任务开发者，单一顺序任务场景收益不明显。
- 开源且免费，适合想摆脱"单 IDE 绑定"、自己组装工具链的开发者。

## 选型建议

- 同时维护多个仓库/分支、想让多个 agent 并行开工的开发者。
- 团队希望"工单→agent→PR→CI→合并"全流程可视化的场景。
- 喜欢开源、本地优先、不想被单一厂商锁定的工程师。
- 已有固定 IDE + 单 agent 工作流、任务量不大的用户：价值有限，可先观望。