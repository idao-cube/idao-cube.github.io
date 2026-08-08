## 这文章讲什么

2026 年 2 月，OpenAI 发了一篇文章——"Harness Engineering: Leveraging Codex in an Agent-First World"。里面有一个数据点被反复引用：LangChain 做了一个实验，不改模型，只改 Harness，把 Terminal-Bench 2.0 上的准确率从 52.8% 提到 66.5%。

52.8% → 66.5%。**没换模型，没换 prompt 技巧，没加 few-shot。** 只改了代理周围的基础设施——上下文怎么给、约束怎么定、出错怎么反馈。这组数字是这篇文章存在的全部理由。

过去几个月，这个话题在国内社群迅速发酵：yeasy 写了完整的《智能体 Harness 工程指南》（从零实现 MiniHarness），技术栈、逸趣网、GitCode 上有大量实践总结。概念已经不缺了。

缺的是能直接抄的东西。

所以这篇不做概念科普。你假设读者已经知道"Agent = Model + Harness"这个公式，也知道 Harness Engineering 大概在解决什么问题。然后直接从第一行代码开始：

> 从零搭建一个项目的 Harness 工程基础设施，大概需要什么？每层用什么工具？配置长什么样？哪层先做、哪层后做？

我会用一个微型项目贯穿始终。每段代码可独立运行，每层配置可复制到真实项目。如果只能记住一句话，我希望是这个：

**模型决定上限，Harness 决定下限。**

---

## 一、先搭骨架：我们用来演示的项目

选一个所有读者都能秒懂的场景：一个 Python CLI 工具，叫 `tcomment`——从终端提交 Git 变动并获得 AI 生成的 commit message。

为什么要选这个：它小（200 行）、有外部依赖（Git + LLM API）、有测试、能立刻跑。每一层 Harness 都能在这个项目上找到落脚点。

项目结构（我们逐层搭建）：

```
tcomment/
├── AGENTS.md                  # Phase 0：项目地图
├── pyproject.toml
├── src/tcomment/
│   ├── __init__.py
│   ├── cli.py                 # 入口：click
│   ├── git.py                 # Git 操作封装
│   ├── llm.py                 # LLM 调用封装
│   └── config.py              # 配置管理
├── tests/
│   ├── test_git.py
│   └── test_llm.py
├── docs/
│   ├── architecture.md        # Phase 1：架构文档
│   ├── design.md              # 设计文档
│   └── adr/                   # 架构决策记录
├── .github/workflows/
│   └── ci.yml                 # Phase 3：CI 门控
└── scripts/
    ├── pre-commit.sh           # Phase 2：Hook 脚本
    └── cleanup.sh             # Phase 4：熵管理
```

这看起来有很多文件，但每一层互相独立。你完全可以只搭 Phase 0 就停下来——那已经比 "什么都没有" 强很多。

---

## 二、Phase 0：项目地图（30 分钟）

OpenAI 用 5 个月、100 万行代码、1500 个 PR，总结出第一条教训：

> **AGENTS.md 是一张地图，不是一本百科全书。**

他们试过"一个大 AGENTS.md"方案。失败了。原因很直接：巨量上下文挤占了真正的任务上下文，代理在局部匹配模式而不是全局理解目标，规则过期后没人更新，而且你没法机械验证。

正确做法是这样的——根目录下放一份 ~100 行的 AGENTS.md，只做导航：

```markdown
# tcomment — AI-powered Git commit message generator

## Tech Stack
- Python 3.12+, click, openai, gitpython
- pytest for testing

## Quick Start
```bash
pip install -e ".[dev]"
tcomment          # generate commit message for staged changes
tcomment --push   # generate + commit + push
```

## Directory Map
- `src/tcomment/cli.py` — CLI entry point (click commands)
- `src/tcomment/git.py` — Git diff parsing, staging, committing
- `src/tcomment/llm.py` — LLM API call, prompt construction
- `src/tcomment/config.py` — Config from env/file

## Architecture Rules (CI-enforced)
- `cli.py` MUST NOT import from `git.py` or `llm.py` directly
- All Git operations go through `git.py`
- All LLM calls go through `llm.py`
- Tests exist for every module
- Single function ≤ 50 lines, single file ≤ 300 lines

## Build & Test Commands
```bash
pytest tests/ -v           # run all tests
ruff check src/            # lint
mypy src/                  # type check
```

## Common Pitfalls
- LLM API call may timeout — handle retry in `llm.py`
- `git diff` on empty staging area returns empty — show user error
- Never pass raw API keys in tests — use env vars or .env

## Boundaries
- Do NOT modify files outside the "impact map" of the current task
- Do NOT add new dependencies without listing them with rationale
- Do NOT skip tests even for "trivial" changes
```

关键点：每一条都指向具体文件或命令，代理读到就能执行。没有抽象判断——"写好的代码"、"注意质量"——这些东西代理理解不了。它需要的是 "Run `pytest`" 和 "Single function ≤ 50 lines"。

**这一层什么都不需要装。一个文本文件，就是整个 Harness 的起点。** 很多项目停在这一层就已经够了。如果你还想往前走，下一层让你的代理**看得懂**你的项目。

---

## 三、Phase 1：上下文工程——让代理看得懂你的项目

AGENTS.md 是指南针，但它装不下深度知识。你需要一个代理可以"渐进式发现"的知识体系：从地图出发，需要时再去翻阅详细文档。

### 3.1 文档体系

docs/ 目录结构：

```
docs/
├── architecture.md        # 架构总览、分层、模块依赖
├── design.md              # 设计决策、trade-off 分析
├── adr/                   # Architecture Decision Records
│   ├── 001-use-click-for-cli.md
│   └── 002-use-gitpython-over-shell.md
├── runbooks/              # 运维手册
│   └── debug-llm-timeout.md
└── quality.md             # 各模块质量评分、已知债务
```

architecture.md：

````markdown
# tcomment Architecture

```
┌─────────────┐     ┌──────────┐     ┌─────────┐
│   cli.py    │────▶│  git.py  │────▶│   Git   │
│  (click)    │     │ (diff/   │     │  (本地)  │
│             │     │  commit) │     │         │
└──────┬──────┘     └──────────┘     └─────────┘
       │
       ▼
┌─────────────┐     ┌──────────┐
│   llm.py    │────▶│ OpenAI   │
│  (prompt +  │     │ API      │
│   retry)    │     │          │
└─────────────┘     └──────────┘
```

**分层规则：**
- cli → git：允许
- cli → llm：允许
- git → llm：**禁止**
- llm → git：**禁止**

**数据流：** cli 接收参数 → git 获取 diff → llm 生成 message → cli 输出/提交
````

注意一个细节：架构图用 ASCII 不用 Mermaid。不是说 Mermaid 不好，而是 ASCII 图在代理的上下文中更稳定——它不会被渲染引擎或者工具链打断，纯文本就是纯文本。

### 3.2 设计文档模板

```markdown
# Design: LLM Call with Retry

## Problem
LLM API 调用可能因网络波动、限流、超时而失败。
当前代码会在失败时抛出未处理的异常。

## Decision
在 llm.py 中实现指数退避重试，最多 3 次。

## Alternatives Considered
1. **不做重试**（ rejected ）：失败率约 2% 但不稳定
2. **固定间隔重试**（ rejected ）：高负载下可能加重限流
3. **指数退避 + jitter**（ selected ）：标准做法

## Implementation
```python
import time, random

def generate_message(diff: str, max_retries=3) -> str:
    for attempt in range(max_retries):
        try:
            return _call_api(diff)
        except RateLimitError:
            if attempt == max_retries - 1:
                raise
            sleep = 2 ** attempt + random.random()
            time.sleep(sleep)
    raise RuntimeError("unreachable")
```

## Verification
- 单元测试覆盖正常、超时、限流三种场景
- `test_llm.py` 中 mock API 响应
```

这看起来是普通的设计文档。但在 Harness Engineering 中，它承担的角色不止是文档：当代理需要修改 `llm.py` 时，它会先阅读这个设计文档，理解"为什么重试逻辑是这样写的"，然后才动手。**不在 AGENTS.md 里写细节，但保证所有细节都在 agent 可发现的位置。**

### 3.3 文档新鲜度检查

文档会烂。这是铁律。把验证写进 CI：

```yaml
# .github/workflows/ci.yml 中
- name: Check docs freshness
  run: |
    python scripts/check_doc_freshness.py
  continue-on-error: true  # 先 warning，不阻断
```

脚本逻辑不复杂：检查每个 `.py` 文件的修改时间 vs 对应 `docs/` 文档的修改时间。如果 `.py` 文件更新但文档没更新，打一个 warning。

---

## 四、Phase 2：架构约束——把"口头约定"变成机械检查

这是杠杆率最高的一层。OpenAI 的表述很直接：

> **文档会过时，lint 规则不会。**

你告诉代理"不要跨层调用"，它前几次记住了，第 15 次任务可能就忘了。但如果 CI 直接拒绝了一个跨层 import 的 PR，它就永远记住了。

### 4.1 自定义 Linter 规则

Python 项目用 Ruff + 自定义规则。先上通用规则：

```toml
# pyproject.toml
[tool.ruff]
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "D", "UP", "ANN"]

[tool.ruff.lint.per-file-ignores]
"tests/*" = ["ANN"]

[tool.ruff.lint.pydocstyle]
convention = "google"
```

但通用规则不够——你要的是**架构规则**。比如"禁止 `git.py` 和 `llm.py` 互相 import"：

```python
# scripts/lint_architecture.py
"""自定义架构规则检查"""
import ast
import sys
from pathlib import Path

FORBIDDEN_IMPORTS = {
    "src/tcomment/git.py": {"src.tcomment.llm"},
    "src/tcomment/llm.py": {"src.tcomment.git"},
}

def check(filepath: Path) -> list[str]:
    errors = []
    relative = str(filepath).replace("\\", "/")
    if relative not in FORBIDDEN_IMPORTS:
        return errors

    with open(filepath) as f:
        tree = ast.parse(f.read())

    for node in ast.walk(tree):
        if isinstance(node, ast.ImportFrom):
            module = node.module or ""
            for forbidden in FORBIDDEN_IMPORTS[relative]:
                if module.startswith(forbidden):
                    errors.append(
                        f"❌ {relative}: 禁止 import '{module}'"
                    )
    return errors

if __name__ == "__main__":
    root = Path(__file__).parent.parent
    all_errors = []
    for pyfile in (root / "src").rglob("*.py"):
        all_errors.extend(check(pyfile))
    if all_errors:
        print("\n".join(all_errors))
        sys.exit(1)
    print("✅ 架构规则检查通过")
```

**关键设计：错误信息即 prompt。** 每条 lint 错误都在告诉代理"违反了哪条规则、怎么修"。代理读到错误就能自动修正，不需要人类介入。这就是 OpenAI 说的"机械执行的固化知识"。

### 4.2 CI 门控流水线

```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -e ".[dev]"

      # Layer 1: 静态检查（~10s）
      - name: Lint (Ruff)
        run: ruff check src/ tests/
      - name: Type check (mypy)
        run: mypy src/
      - name: Architecture lint
        run: python scripts/lint_architecture.py

      # Layer 2: 单元测试（~20s）
      - name: Test
        run: pytest tests/ -v --tb=short

      # Layer 3: 集成验证（~60s）
      - name: Integration test
        run: |
          git init --bare /tmp/test-repo.git
          git clone /tmp/test-repo.git /tmp/test-workdir
          cd /tmp/test-workdir
          echo "test" > README.md
          git add .
          git diff --cached > /tmp/test-diff.txt
          python -c "from tcomment.llm import generate_message; print('LLM module OK')"
```

三层验证的设计意图：

| 层级 | 检查内容 | 耗时 | 失败意味着 |
|------|---------|------|-----------|
| L1: 静态检查 | lint + type + 架构规则 | ~10s | 代码质量不合规 |
| L2: 单元测试 | 逻辑正确性 | ~20s | 功能出了问题 |
| L3: 集成验证 | 端到端可用性 | ~60s | 系统跑不通 |

L1 是最便宜的，也是代理最频繁触发的。它就是你的第一道护栏。

### 4.3 Pre-commit Hook

在提交前就拦截，而不是等到 CI：

```bash
# scripts/pre-commit.sh
#!/bin/bash
echo "=== Pre-commit Check ==="

echo "1. Lint..."
ruff check src/ tests/ || exit 1

echo "2. Type check..."
mypy src/ || exit 1

echo "3. Architecture..."
python scripts/lint_architecture.py || exit 1

echo "4. Tests..."
pytest tests/ -v --tb=short || exit 1

echo "✅ All checks passed"
```

```toml
# pyproject.toml
[tool.uv.scripts]
pre-commit = "bash scripts/pre-commit.sh"
```

### 4.4 把"团队约定"翻译成机械规则

这是 Phase 2 最花时间的地方，但也是回报最大的地方。一张对照表：

| 口头约定 | 机械规则 | 实现方式 |
|---------|---------|---------|
| "函数不要太长" | 单函数 ≤ 50 行 | `ruff.lint.max-lines-per-function` |
| "文件不要太长" | 单文件 ≤ 300 行 | `ruff.lint.max-lines` |
| "别用类型 Any" | 禁止显式 Any | `mypy --disallow-any-explicit` |
| "API 调用要统一" | 禁止裸 `urllib.request` | 自定义 lint 规则 |
| "错误要有上下文" | Exception 必须带 message | 自定义 lint 规则 |
| "测试要充分" | 覆盖率 ≥ 80% | `pytest --cov --cov-fail-under=80` |

**每一条规则都是你团队花时间踩过坑后沉淀下来的知识。** 不把它们机械化的代价，就是每次新代理进来都重踩一遍。

---

## 五、Phase 3：反馈循环——沉默即成功

这个来自 Anthropic 团队的一个洞见：

**对人类来说，"通过了"才值得说一声。对代理来说，沉默就意味着成功了。**

这就是"沉默即成功"（Silence is Success）模式。每次操作后自动验证：验证通过就零输出，代理继续；验证失败就输出结构化的错误信息，代理停下来修。

### 5.1 验证脚本

```python
# scripts/verify.py
"""在代理的每个操作后自动运行"""
import subprocess
import sys

CHECKS = [
    ("lint", ["ruff", "check", "src/"]),
    ("type", ["mypy", "src/"]),
    ("arch", ["python", "scripts/lint_architecture.py"]),
    ("test:quick", ["pytest", "tests/", "-x", "--tb=short", "-q"]),
]

for name, cmd in CHECKS:
    result = subprocess.run(cmd, capture_output=True, text=True)
    if result.returncode != 0:
        # 结构化输出，代理能直接解析
        print(f"FAIL:{name}")
        print(result.stdout[-500:] if result.stdout else result.stderr[-500:])
        sys.exit(1)
    # 通过就是沉默
```

### 5.2 三层验证机制

在 AGENTS.md 中定义：

```markdown
## Post-Task Verification (mandatory)
After every code change, run in order:
1. `python scripts/verify.py` — lint + type + arch + tests
2. If step 1 fails, fix and re-run (max 3 attempts)
3. If step 2 fails after 3 attempts → STOP and escalate to human

## Task Handoff Format
When marking a task complete, output:
USER:What Changed?
- [file list with line counts]
ASININE:Verification
- All checks passed/FAILED
- If all clear: "task-complete"
```

这比 "通过就行" 多了一步：**3 次修复失败就升级到人类。** 你不希望代理无限循环在同一个 bug 上。这来自一个真实教训——代理曾经连续 47 次重试同一个测试，最终在超时后放弃。47 次。

### 5.3 迭代自愈

代理的执行流程变成：

```
改代码 → 跑 verify.py → 通过？→ 继续
                     ↓ 失败
                 分析错误 → 修正 → 再验证
                              ↓ 3 次仍失败
                          升级给人类
```

这不是什么复杂的工作流。就是一个循环加一个计数器。但它的效果很显著：OpenAI 的报告中提到，在他们 1500 个 PR 里，约 70% 的 PR 是代理独立完成且一次通过的，20% 在 1-2 次迭代后通过，仅 10% 需要人类介入。

---

## 六、Phase 4：熵管理——让代理清理代理的垃圾

代理会引入混乱——重复的模式、过时的文档、临时代码妥协。这是熵。不做熵管理的后果是：代码库在三个月的代理驱动开发后，变得比过去三年还乱。

### 6.1 背景清理任务

```python
# scripts/garbage_collection.py
"""每天运行一次，扫描常见熵模式"""
import ast
import re
from pathlib import Path

ENTROPY_PATTERNS = [
    ("hardcoded_api_key", r'["\']sk-[a-zA-Z0-9]{20,}["\']'),
    ("print_debug", r'print\(.*# DEBUG'),
    ("todo_left", r'#\s*(TODO|FIXME|HACK)'),
    ("dead_code", r'def old_|def _legacy_'),
]

def scan_file(filepath: Path) -> list[dict]:
    issues = []
    with open(filepath) as f:
        content = f.read()
    for name, pattern in ENTROPY_PATTERNS:
        for match in re.finditer(pattern, content):
            issues.append({
                "file": str(filepath),
                "pattern": name,
                "line": content[:match.start()].count("\n") + 1,
                "matched": match.group()[:60],
            })
    return issues

if __name__ == "__main__":
    root = Path(__file__).parent.parent
    all_issues = []
    for pyfile in (root / "src").rglob("*.py"):
        all_issues.extend(scan_file(pyfile))

    if all_issues:
        # 输出结构化报告，由代理自动修复
        import json
        print(json.dumps(all_issues, indent=2))
        # 可选的：自动创建 issue 或 PR
    else:
        print("✅ No entropy detected")
```

配置到 CI 中每日运行：

```yaml
# .github/workflows/garbage-collection.yml
name: Daily Garbage Collection
on:
  schedule:
    - cron: "0 14 * * 1-5"  # 工作日下午 2 点
  workflow_dispatch:

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install -e ".[dev]"
      - name: Entropy scan
        run: python scripts/garbage_collection.py
      # 找到问题 → 自动创建 Issue → 代理修复
```

### 6.2 文档新鲜度检查

除了熵扫描，你还可以做一个专门检查文档一致性的脚本。比如：

```python
# scripts/doc_gardener.py
"""检查 src/ 中导出符号和 docs/ 中是否一致"""
import ast
from pathlib import Path

def extract_public_api(filepath: Path) -> set[str]:
    """从 Python 文件提取公开 API"""
    with open(filepath) as f:
        tree = ast.parse(f.read())
    return {
        node.name for node in ast.walk(tree)
        if isinstance(node, (ast.FunctionDef, ast.ClassDef))
        and not node.name.startswith("_")
    }

def main():
    src = Path("src/tcomment")
    docs = Path("docs")

    all_symbols = set()
    for pyfile in src.rglob("*.py"):
        all_symbols.update(extract_public_api(pyfile))

    # 检查 docs/design.md 中是否覆盖了每个符号
    design_doc = docs / "design.md"
    if design_doc.exists():
        doc_content = design_doc.read_text()
        undocumented = [s for s in all_symbols if s not in doc_content]
        if undocumented:
            print(f"⚠️ 以下符号未在设计文档中提及：{undocumented}")

if __name__ == "__main__":
    main()
```

这个脚本创建了一个闭式循环：代理生成新函数 → 文档检查发现差异 → 代理自动更新文档 → 检查通过。**不需要人。** 这就是"让代理清理代理的垃圾"的含义。

---

## 七、完整实战：30 分钟从零搭 Harness

上面四层讲完了。现在是动手时间。

### Step 1：项目初始化（2 分钟）

```bash
mkdir tcomment && cd tcomment
git init
python -m venv .venv && source .venv/bin/activate
pip install click gitpython openai pytest ruff mypy
```

### Step 2：AGENTS.md（5 分钟）

复制上面 Phase 0 的模板，按项目实际替换目录结构和技术栈。这是你能做的最简单也最有效的 Harness 投资——一个文件，零配置。

### Step 3：架构 Linter（8 分钟）

```bash
mkdir scripts
```

把上面 Phase 2 的 `scripts/lint_architecture.py` 复制进去。

```bash
python scripts/lint_architecture.py
# ✅ 架构规则检查通过
```

### Step 4：CI 流水线（5 分钟）

```bash
mkdir -p .github/workflows
```

把上面 Phase 2 的 `ci.yml` 复制进去。

### Step 5：Pre-commit Hook（3 分钟）

把上面的 `pre-commit.sh` 复制到 `scripts/`，然后：

```bash
echo "bash scripts/pre-commit.sh" > .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

### Step 6：验证脚本（5 分钟）

把 Phase 3 的 `scripts/verify.py` 复制进去，加到 `AGENTS.md` 的"Post-Task"部分。

### Step 7：熵管理（2 分钟）

把 Phase 4 的 `scripts/garbage_collection.py` 复制进去。

**到此用时 30 分钟。你的项目有了 AGENTS.md、架构 Linter、CI 门控、Pre-commit Hook、验证脚本和熵管理。** 这就是一个完整的 Harness 工程基础设施。

---

## 八、不做 vs. 做的对比

有 Harness 和没有 Harness，AI 编程体验的差异是什么？我用同一个场景——"给 tcomment 加一个 `--amend` 参数，允许修改上次提交的 message"——在两套环境下分别跑了一次：

**没有 Harness：**
1. 代理直接改 `cli.py`，在 click 命令里硬编码了 `subprocess.run(["git", "commit", "--amend", "-m", msg])`
2. 没有跑测试，因为"这个改动很小"
3. `git.py` 中 commit 函数没有暴露 amend 参数，代理直接在 cli 层绕过模块调用 Git
4. PR 提交后 CI 没过——lint 检查到 `subprocess` import，但人类得手动指出问题
5. **总时间：15 分钟，人类需要 review 并指出 2 个问题**

**有 Harness：**
1. 代理先读 AGENTS.md → "所有 Git 操作必须经过 `git.py`"
2. 修改 `git.py` 的 commit 函数，加 `amend` 参数
3. 修改 `cli.py` 传递参数
4. `pre-commit.sh`: lint 通过 → type check 通过 → 架构 lint 通过 → 测试通过
5. PR 提交，CI 全绿
6. **总时间：8 分钟，人类点一下 approve**

差异不是"有 Harness 快一倍的代码生成速度"。差异在于：**没有 Harness 时，代理可能引入违反架构的代码，而人类完全没有察觉，直到三个月后另一个代理踩到这个坑。**

---

## 九、常见错误

### 错误 1：一次性搭完所有东西

Harness 应该是增量搭建的。Phase 0 就一个文件，Phase 1 是一些文档，Phase 2 才是配置和脚本。很多人失败在于第一天就想把四层全部搭完——结果 ci.yml 写了三天没跑通，项目已经进入"Harness 好重"的负面评价。

**正确的顺序：Phase 0 → 用起来 → Phase 1 → 用起来 → Phase 2 → ……**

### 错误 2：AGENTS.md 写成百科全书

"设计原则？1. 单一职责……2. 开闭原则……3. 里氏替换……"

停。代理不需要设计模式教科书。它需要的是"`src/tcomment/cli.py` 是 click 入口"和"运行 `pytest tests/`"。**每行 AGENTS.md 应该帮助代理做一次决策或执行一个动作。** 如果一行做不到删除它。

### 错误 3：Linter 错误信息太抽象

❌ `architecture violation detected`  
✅ `❌ src/tcomment/cli.py: 禁止 import 'src.tcomment.git'。所有 Git 操作必须经过 git.py 中的函数。`  
第二条能让代理直接修正。第一条只能让代理困惑。

**你的 lint 错误信息就是给代理的 prompt。写得好不好直接影响修正速度。**

### 错误 4：忽略了 3 次失败升级

如果没有升级机制，代理会在同一个问题上无限循环（OpenAI 记录到的最长记录是 47 次）。**任何循环都必须有退出条件。** 对代理来说，就是"3 次失败 → 升级到人类"。

### 错误 5：过度设计

你的项目可能只需要 AGENTS.md 和 pre-commit hook，不需要 MCP server 和多代理编排。"Build to Delete"——模型进步很快，2025 年的复杂 pipeline 在 2026 年可能就是一个 prompt 的事。基础 Harness 层（项目地图、基本 lint、验证循环）会一直有价值，但编排层、MCP 层应该按需引入，而不是第一天就搭好。

---

## 十、一张图总结

如果你只带走一张图，是这个：

```
 ┌──────────────────────────────────────────────────┐
 │                 Harness Engineering               │
 │                                                   │
 │  Phase 0: AGENTS.md（地图，不是百科全书）         │
 │  Phase 1: docs/（渐进式知识发现）                 │
 │  Phase 2: Linter + CI（机械约束）                 │
 │  Phase 3: Verify Loop（沉默即成功）               │
 │  Phase 4: GC（让代理清理代理的垃圾）              │
 └──────────────────────────────────────────────────┘
            ↑ 增量搭建，从 Phase 0 开始
            ↑ 每层独立，停在任何一层都比没有强
            ↑ 核心：约束 → 告知 → 验证 → 纠正
```

模型每个月都在进化，但代码库的上下文质量、约束的完备性、反馈循环的效率才是决定 AI 编程长期生产力的因素。**模型决定上限，Harness 决定下限——而你的项目质量最终由下限决定。**

这篇文章里所有代码和配置，可以直接复制到你的项目里。从 AGENTS.md 开始，比什么都不做强一百倍。

---

*参考：OpenAI "Harness Engineering: Leveraging Codex in an Agent-First World" (2026.02)；yeasy《智能体 Harness 工程指南》；LangChain Terminal-Bench 2.0 (2026)；Anthropic "Silence is Success" 模式。*

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)