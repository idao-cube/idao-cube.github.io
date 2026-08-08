## 产品与定位

开源终端编程助手，通过模型协作编辑本地代码仓库。基于 GPT-4o/Claude 等模型构建，支持多文件编辑、Git 集成和结对编程工作流。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 多模型支持 | GPT-4o、Claude、DeepSeek 等 |
| 多文件编辑 | 一次对话修改多个文件 |
| Git 追踪 | 自动 commit，清晰的更改历史 |
| 终端集成 | 配合任意编辑器使用 |
| 代码审查 | 自动分析代码问题 |
| 测试生成 | 生成单元测试和集成测试 |

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `aider` | 启动交互会话 |
| `aider --model gpt-4o` | 指定模型 |
| `aider --stream-output` | 流式输出 |
| `/commit` | 提交 Git commit |
| `/diff` | 查看当前更改 |

## 调用与兼容性

```bash
# 安装
pip install aider-chat

# 使用
aider main.py

# 指定模型和 API
export OPENAI_API_KEY=sk-xxx
aider --model gpt-4o main.py
```

## 版本与更新注意

活跃维护，持续支持新模型和改进功能。支持 Gemini、DeepSeek 等新模型。

## 选型建议

终端重度用户、Vim/Neovim 用户首选 Aider；需要 Git 深度集成和代码审查能力时优先评估。