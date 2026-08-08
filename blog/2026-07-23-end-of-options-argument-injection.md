## 写在前面

2019 年 11 月，git 2.24.0 默默地引入了一个你可能从未听过的标志：`--end-of-options`。直到 2026 年 7 月，一位开发者在修复包管理器 CVE 时偶然发现它，才意识到这个看似晦涩的标志，背后隐藏着一个影响深远的安全问题——参数注入（CWE-88）。

这篇文章从 `--end-of-options` 这个"隐藏的" git 标志出发，深入探讨参数注入如何从 Unix 的 `--` 约定演化到现代包管理器生态的系统性风险。我们会看到：为什么 `--` 在 git 里不是万能的、为什么 19 个主流包管理器中只有 Go 真正理解了这个问题、以及为什么"支持旧版本"这个看似合理的决定，正在让整个软件供应链悬于一线。

---

## 一、`--` 不是万能的：为什么 git 需要 `--end-of-options`

### 1.1 Unix 的 `--` 约定：结束选项解析的传统

在 Unix 世界里，`--` 有一个约定俗成的含义：**结束选项解析**。它的诞生源于一个简单但致命的矛盾：

```bash
# 你想删除一个名叫 -f 的文件
rm -- -f
```

如果没有 `--`，`rm` 会把 `-f` 当作 force 标志，而不是文件名。`--` 告诉 `rm`："从这里开始，所有的参数都是文件名，不是选项。"

这个约定在大多数 Unix 工具中都成立。但 git 打破了这个约定。

### 1.2 git 的 `--` 劫持：从选项终结符到 revision-pathspec 分隔符

git 在早期就把 `--` 劫持了——它不再是"结束选项解析"的标志，而是**revision 与 pathspec 的分隔符**：

```bash
# 显示 main 分支上对 README.md 的所有提交
git log main -- README.md

# 这里的 -- 告诉 git：main 是 revision，README.md 是 pathspec
```

为什么需要这个分隔符？因为 `git log foo` 本身是有歧义的：`foo` 可能是一个分支名，也可能是一个文件名。`--` 消除了这种歧义。

但这带来了一个严重的问题：**revision 位置再也没有终结符**。如果一个脚本运行 `git log "$rev"`，而 `$rev` 以 `-` 开头，git 就会把它当作一个选项来解析。

### 1.3 `--end-of-options` 的诞生：git 的"第二条生命"

git 2.24.0（2019 年 11 月）引入 `--end-of-options`，它的作用是**真正结束选项解析**，而 `--` 在 git 里已经被 repurposed 了。

```bash
# 安全的用法：--end-of-options 结束选项，-- 分离 revision 和 pathspec
git log --end-of-options "$rev" -- "$path"
```

这两个标志在 git 里**不是同义的**：

| 标志 | 作用 | 适用场景 |
|------|------|----------|
| `--` | 分离 revision 和 pathspec | `git log main -- README.md` |
| `--end-of-options` | 结束选项解析 | `git log --end-of-options "$rev"` |

把它们当作同义词是一种常见但危险的误解。`git clone -- "$url"` 能工作，是因为 `clone` 遵循 POSIX 约定；但 `git checkout "$ref" --` 只能保证 `$ref` 被当作 revision，而不能防止它被解析为选项。

### 1.4 子命令支持的不一致：git 的"渐进式安全"

`--end-of-options` 的支持是**逐子命令推出**的，而不是一次性完成的：

- **git 2.24.0**（2019 年 11 月）：初始引入，部分子命令支持
- **git 2.30.0**（2021 年 1 月）：`git rev-parse` 支持 — 因为它有自己的手工参数解析器
- **git 2.43.1**（2024 年 2 月）：`git checkout` 和 `git reset` 支持 — 它们自己的 `--` 解析器曾经拒绝这个标志

这个渐进式推出反映了 git 的架构复杂性：**不同的子命令有不同的参数解析器**。一些子命令使用共享的解析器，一些有自己的手工实现。这种不一致性直接导致了安全漏洞的逭生。

---

## 二、参数注入（CWE-88）：没有 shell 的攻击

### 2.1 CWE-88 vs. 命令注入：区别在哪里

参数注入（CWE-88）与命令注入（CWE-78）经常被混淆，但它们有着根本的区别：

- **命令注入**：攻击者通过 shell 元字符（`;`、`|`、`&`）插入额外的命令
- **参数注入**：攻击者通过以 `-` 开头的参数，欺骗程序将其解析为选项

关键区别在于：**参数注入不需要 shell**。攻击者构建一个 argv 数组，调用 `exec` 直接执行，正如每个"不要使用 `system()`" 指南所推荐的那样。数组完整到达 git，git 然后把一个以 `-` 开头的参数解析为选项。

### 2.2 攻击原语：git、hg、ssh 如何把"选项"变成"命令执行"

git、Mercurial（hg）和 ssh 都提供了选项，**其文档明确表示这些选项会运行调用者命名的命令**：

```bash
# git clone 的 --upload-pack 选项：指定服务器端二进制
git clone --upload-pack=/tmp/evil.git origin repo

# git 的 -c core.sshCommand 选项：覆盖连接方式
git -c core.sshCommand="ssh -o ProxyCommand=/tmp/evil" clone origin repo

# hg 的 --config 选项：将子命令重新定义为 shell 脚本
hg --config alias.clone="!sh /tmp/evil" clone

# ssh 的 -oProxyCommand 选项：指定代理命令
ssh -o ProxyCommand="/tmp/evil" host
```

这些是**文档化的功能**。但当一个包装程序将不可信字符串传入参数列表时，它们就会变成攻击原语。

### 2.3 CVE-2019-13139：Docker Build 的经典案例

Docker Build 的 CVE-2019-13139 是参数注入的一个教科书案例：

1. Go 的 `os/exec` 包构建 argv 数组，**没有 shell**
2. git-context URL 的 `#ref:dir` fragment 最终作为 `--upload-pack=<cmd>` 传入 `git fetch`
3. git 解析这个参数为选项，执行攻击者指定的命令

整个攻击链中**没有任何 shell 调用**，但攻击者仍然获得了命令执行。这正是参数注入的邪恶之处：它利用了"正确的"编程实践（使用 argv 数组而非 shell）来实现攻击。

---

## 三、2017 年的集体警醒：四个 VCS 系统同一天爆出漏洞

2017 年 8 月，一天之内，四个版本控制系统爆出参数注入漏洞：

| CVE | 系统 | 根因 |
|-----|------|------|
| CVE-2017-1000117 | Git | URL hostname 传入 ssh 作为参数 |
| CVE-2017-1000116 | Mercurial | URL hostname 传入 ssh 作为参数 |
| CVE-2017-9800 | Subversion | URL hostname 传入 ssh 作为参数 |
| CVE-2017-12836 | CVS | URL hostname 传入 ssh 作为参数 |

### 3.1 相同的攻击模式：hostname → ssh → -oProxyCommand=

所有四个漏洞都有一个共同的模式：

1. URL 的 hostname 被提取出来
2. hostname 作为参数传入 `ssh`
3. 如果 hostname 以 `-oProxyCommand=` 开头，ssh 会把它解析为选项

```bash
# 攻击 URL
git://-oProxyCommand=/tmp/evil/repo.git

# git 提取 hostname: -oProxyCommand=/tmp/evil
# ssh 解析为: ssh -o ProxyCommand=/tmp/evil host
```

### 3.2 不同的修复策略：`--` vs. 格式校验

Phabricator 的事后分析[^1]指出了一个重要现象：**只有 Subversion 真正添加了 `--` 在 hostname 之前**：

- **Subversion**：添加 `--` 在 hostname 之前 — `ssh -- "$hostname"`
- **Git**：校验 hostname 格式 — 拒绝以 `-` 开头的 hostname
- **Mercurial**：校验 hostname 格式 — 拒绝以 `-` 开头的 hostname

为什么 Git 和 Mercurial 没有使用 `--`？部分原因是**并非所有 ssh 实现都支持 `--`**。这揭示了一个深层次的问题：`--` 机制本身被称为"默认不安全"（unsafe by default），因为没有 `--` 的代码看起来正确且工作良好，直到某个参数以 `-` 开头。

### 3.3 事后分析的启示："unsafe by default"

Phabricator 的分析得出了一个重要结论：`--` 机制本身是"默认不安全的"。为什么？

- 代码没有 `--` 看起来正确，运行起来也没问题
- 只有当参数以 `-` 开头时，问题才暴露
- 这意味着漏洞是**潜伏性的** — 在正常使用中无法被发现

---

## 四、包管理器：参数注入的主要战场

### 4.1 为什么包管理器是目标

包管理器的一个核心功能是：**从 git URL 或 ref 拉取依赖**。这些 URL 或 ref 来自：

- 清单文件（Gemfile、package.json、pyproject.toml 等）
- lockfile
- 传递性依赖的元数据

这些数据通常是**不可信的** — 它们可能来自第三方包，可能被篡改。

### 4.2 19 个包管理器的调查：只有 Go 真正理解

作者对 19 个包管理器进行了调查（截至 2026 年 7 月）：

| 包管理器 | 默认使用 git 二进制 | 使用 `--end-of-options` |
|----------|-------------------|----------------------|
| Bundler | ✅ | ❌ |
| Cargo | ❌ (libgit2) | N/A |
| CocoaPods | ✅ | ❌ |
| Composer | ✅ | ❌ |
| Conan | ✅ | ❌ |
| **Go** | ✅ | ✅ |
| Helm | ✅ | ❌ |
| Homebrew | ✅ | ❌ |
| Mix | ✅ | ❌ |
| Nix | ✅ | ❌ |
| npm | ✅ | ❌ |
| pip | ✅ | ❌ |
| pnpm | ✅ | ❌ |
| Poetry | ❌ (dulwich) | N/A |
| Pub | ✅ | ❌ |
| SwiftPM | ✅ | ❌ |
| uv | ✅ | ❌ |
| vcpkg | ✅ | ❌ |
| Yarn | ✅ | ❌ |

**惊人的结果**：19 个包管理器中，只有 Go 的 `cmd/go` 使用了 `--end-of-options`。

### 4.3 已公开的 CVE：参数注入的记录

以下是已公开的包管理器参数注入 CVE：

- **CVE-2021-43809** (Bundler)
- **CVE-2021-29472** (Composer)
- **CVE-2022-24828** (Composer)
- **CVE-2022-36069** (Poetry)
- **CVE-2023-5752** (pip)
- **CVE-2022-21223** (CocoaPods)
- **CVE-2022-24440** (CocoaPods)
- **CVE-2025-68119** (Go)

这些 CVE 跨越了 5 年的时间，涉及 Ruby、PHP、Python、Go 等多个语言生态。

### 4.4 Go 的演进：`--` 到 `--end-of-options`

Go 的 `cmd/go` 在参数注入防护上经历了三个阶段：

1. **2019 年 6 月**：添加 `--` 在仓库 URL 之前 — 作为一般性的安全强化
2. **2026 年 1 月**：CVE-2025-68119 爆出 — `--` 不足以防护
3. **2026 年 1 月**：全面添加 `--end-of-options`，并引入 `HGPLAIN=+strictflags`

Go 的 commit message 透露了一个真理：

> "We should probably follow up with a more structured change to make it harder to accidentally re-introduce these issues in the future, but for now this addresses the issue at hand."

这句话道出了所有维护者的心声：**当前的修复只是权宜之计**。

---

## 五、最小 git 版本的困境：安全 vs. 兼容性

### 5.1 `--end-of-options` 的最低版本要求

`--end-of-options` 不是在所有 git 版本中都可用的。它的支持取决于子命令：

| 子命令 | 最低版本 | 发布时间 |
|--------|----------|----------|
| 大多数子命令 | 2.24.0 | 2019 年 11 月 |
| `git rev-parse` | 2.30.0 | 2021 年 1 月 |
| `git checkout` / `reset` | 2.43.1 | 2024 年 2 月 |

这意味着使用 `--end-of-options` 需要**提高最低 git 版本**：

- 大多数子命令：2.24.0
- `rev-parse`：2.30.0
- `checkout`/`reset`：2.43.1

### 5.2 分发版的现实：老旧 git 的广泛存在

许多发行版仍在使用老旧的 git 版本：

| 发行版 | git 版本 | 支持状态 |
|--------|----------|----------|
| Amazon Linux 2 | 2.14.3 | 已 EOL（2026 年 6 月） |
| Ubuntu 18.04 | 2.17.0 | 扩展支持至 2028 年 |
| Ubuntu 20.04 | 2.25.1 | 扩展支持至 2030 年 |

Ubuntu 20.04 的 git 2.25.1 足够新，可以接受 `--end-of-options` 在 `git fetch` 上，但对于 `git rev-parse` 却太旧。

### 5.3 包管理器的选择：为什么不用 `--end-of-options`

Composer 的 CVE-2022-24828 通告解释了为什么几乎没有包管理器使用 `--end-of-options`：

> Composer 支持的 git 版本早于 `--end-of-options` 引入，因此补丁拒绝以 `-` 开头的分支名。

vcpkg 的 git 集成代码中有一个注释，声明最低版本为 git 2.7.4。Homebrew 的 `HOMEBREW_MINIMUM_GIT_VERSION` 是 2.7.0（自 2018 年设置）。

这些最低版本标准反映了一个现实：**支持旧版本是商业上的必要**。但这也意味着安全修复只能通过**输入校验**（拒绝以 `-` 开头的参数）来实现，而不是通过**结构性的防护**（`--end-of-options`）。

### 5.4 Homebrew 的尝试：提高最低版本

作者向 Homebrew 提交了一个 PR，将最低 git 版本提高到 2.30.0，并在 `clone`、`remote set-url` 和 `ls-remote` 中添加 `--end-of-options`。

但 `checkout` 和 `reset` 调用被留下不做处理，因为覆盖它们需要最低版本 2.43.1（2024 年 2 月发布），仍然领先于多个支持的发行版。

这个 PR 展示了一个普遍的难题：**安全修复的速度，永远赶不上漏洞的发现速度**。

---

## 六、库 vs. 二进制 fork：架构上的权衡

### 6.1 库的优势：没有 argv 边界

libgit2、gitoxide、go-git、JGit 和 dulwich 都实现了足够的 git 线协议，可以在进程内克隆和获取，**没有 argv 边界，也没有参数列表可以注入**。

Jujutsu 使用 gitoxide 进行 git 互操作，没有发布过参数注入类 CVE。它的两个 CVE 都是路径遍历和缺失的 SHA-1 碰撞检查，继承自库本身。

### 6.2 库的劣势：跟踪上游的安全补丁

但库也有代价：**必须跟踪上游 git 发送的所有 checkout-safety 修复**。libgit2 和 JGit 都经历过这样的补丁轮次。

这是一个**具体的补丁流**，而不是一个需要在每个调用点永远记住的检查。从某种意义上说，库的安全模型更容易推理：

- **二进制 fork**：需要在每个调用点记住 `--` 或 `--end-of-options`
- **库**：只需要应用上游的补丁

### 6.3 go-git 的例外：file:// 传输仍然 fork git

go-git 有一个 CVE（CVE-2025-21613），但它专门在 `file://` 传输上，这个代码路径**仍然 fork git 二进制**。

这揭示了一个深层次的真理：**只要有一条代码路径 fork git，你就有参数注入的风险**。

### 6.4 Nix 的混合策略

Nix 使用 libgit2 读取本地仓库，但 fork git 进行获取，**因为 libgit2 缺乏 git-credential helper 支持**。

这种混合策略反映了一个现实：库和二进制在功能上并不完全等价。

---

## 七、更广远的启示：软件供应链的脆弱性

### 7.1 参数注入的普遍性

参数注入不仅存在于 git、ssh，还存在于任何接受外部输入并将其作为选项传递给子进程的软件：

- **包管理器**：从清单文件读取 URL/ref → fork git
- **CI/CD 系统**：从配置文件读取命令 → fork 构建工具
- **容器构建**：从 Dockerfile 读取参数 → fork git/ssh
- **IDE 插件**：从项目配置读取 URL → fork VCS 工具

### 7.2 "unsafe by default" 的根本原因

`--` 机制之所以"默认不安全"，是因为：

1. **代码看起来正确**：没有 `--` 的代码在正常情况下工作良好
2. **漏洞潜伏**：只有当参数以 `-` 开头时，问题才暴露
3. **难以测试**：正常的测试用例不会触发这个漏洞

### 7.3 结构性防护 vs. 输入校验

当前的防护策略分为两种：

| 策略 | 示例 | 优点 | 缺点 |
|------|------|------|------|
| 结构性防护 | `--end-of-options` | 从根本上防止解析为选项 | 需要较高的最低版本 |
| 输入校验 | 拒绝以 `-` 开头的参数 | 兼容性好 | 容易遗漏调用点 |

结构性防护更可靠，但需要提高最低版本；输入校验更兼容，但容易出错。

### 7.4 未来的方向

从 Go 的 commit message 可以看出，业界正在寻求更结构性的解决方案：

> "We should probably follow up with a more structured change to make it harder to accidentally re-introduce these issues in the future."

可能的方向包括：

1. **参数化的 API**：提供接受结构化参数而非字符串的 API
2. **类型安全的 argv**：在编译时防止参数被解析为选项
3. **沙箱化**：限制子进程的能力，使参数注入无法导致命令执行

---

## 八、结论：从 `--end-of-options` 到供应链安全

`--end-of-options` 这个看似晦涩的 git 标志，背后隐藏着一个关于软件供应链安全的深刻真理：

> **安全不是一个特性，而是一个架构决策。**

每一个将不可信字符串传入参数列表的调用，都是一个潜在的攻击面。`--` 约定在 Unix 中工作了几十年，但 git 的 repurposing 告诉我们：**约定可以被打破，安全不能依赖约定**。

对于包管理器维护者来说，这个问题没有简单的解决方案：

- 使用 `--end-of-options` 需要提高最低 git 版本，可能失去用户
- 使用输入校验容易遗漏调用点，可能引入漏洞
- 使用库避免了 argv 边界，但需要跟踪上游的安全补丁

但有一个真理是清晰的：**"默认不安全"的代码注定会出问题**。安全必须是默认的、结构性的、无法绕过的。

正如 Go 的 commit message 所说：

> "We should probably follow up with a more structured change..."

这不仅是对 Go 的期望，更是对整个软件行业的呼唤。

---

## 相关资源

- [gitcli(7) — Git 文档](https://git-scm.com/docs/gitcli)
- [CWE-88: 参数注入](https://cwe.mitre.org/data/definitions/88.html)
- [SonarSource 参数注入向量目录](https://sonarsource.github.io/argument-injection-vectors/)
- [Snyk: 参数注入与 git/mercurial 的使用](https://snyk.io/blog/argument-injection-when-using-git-and-mercurial/)




> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)