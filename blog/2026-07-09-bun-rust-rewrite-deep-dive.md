> 2026 年 5 月 14 日，Bun 团队做了一件几乎所有资深工程师都会告诉你「绝对不要做」的事——将 535,496 行生产级 Zig 代码重写为 Rust。更疯狂的是，整个过程只用了 11 天，由一个人主导、64 个 Claude 代理并行执行，总 API 成本约 16.5 万美元。
>
> 结果呢？零测试被删除，全部 60,000+ 测试通过，二进制体积缩小 20%，内存泄漏基本清零，吞吐量提升 2-5%，修复了 128 个历史 bug。
>
> 这不是一个关于「Rust 比 Zig 好」的故事。这是一个关于「当 AI 足够强大时，什么才是可能的」的故事。

## 起点：一个靠 Zig 起飞的疯狂项目

把时间拨回 2021 年。Jarred Sumner 在 Oakland 一间逼仄的公寓里，在单人、无 LLM 辅助的条件下，用 Zig 语言写出了 Bun 的第一个版本。

他说得很直白：「对一个野心过大、单人开发的项目来说，默认结局就是成为 GitHub 个人主页上那些死掉的 side project 坟墓里的一员。Zig 让 Bun 成为可能。」

他赌对了。如今 Bun 每月的 CLI 下载量超过 2200 万次，Claude Code、OpenCode 等工具依赖它作为运行时，Vercel、Railway、DigitalOcean 都提供了原生支持。

但成长的另一端是**稳定的代价**。

## 二、稳定性的黑洞：当手动内存管理遇上 GC

Bun 的定位注定了它的复杂性：

- JavaScript/TypeScript/CSS 的转译、压缩、打包
- npm 兼容包管理器
- Jest 兼容测试运行器
- Node.js API 实现（fs、net、tls 等）
- HTTP/1.1 和 WebSocket 客户端
- 模块解析器

问题的核心在于：**JavaScript 是一门垃圾回收语言，而 Zig（像 C 一样）不管理你的内存**。

Jarred 在文章中贴出了一份触目惊心的 bug 清单——来自 v1.3.14 一个版本的修复记录：

> - `node:zlib` 中调用 `.reset()` 时与异步 `.write()` 竞争导致 heap-use-after-free
> - `node:http2` 中重入 JS 回调触发哈希表 rehash 导致内部流指针失效
> - `UDPSocket.send()` 中 `valueOf()`/`toString()` 回调在 payload 捕获和实际发送之间分离 ArrayBuffer
> - `tlsSocket.setSession()` 每次调用泄漏一个 SSL_SESSION（约 6.5 KB）
> - `fs.watch()` 在 `.close()` 后 watcher 从未被 GC 回收
> - CSS 解析器中 `background-clip` 多 vendor prefix + 多层背景导致 double-free

这不是孤例。这是混合 GC + 手动内存管理架构的系统性后果。

### Zig 的「美德」也是它的「缺陷」

Zig 设计的核心理念之一是「没有隐藏控制流」。它用 `defer` 和 `errdefer` 来显式地清理资源，而不是像 C++ 的析构函数或 Rust 的 `Drop` 那样在作用域结束时自动运行。

| 语言 | 清理机制 |
|------|---------|
| Zig  | `defer`、`errdefer` |
| C++  | 析构函数、移动语义 |
| Rust | `Drop` trait |

问题是：**当你把一个 `*T` 传递给多个函数时，你怎么知道它什么时候不再被引用？什么时候可以安全释放？**

Bun 的应对方案是混合使用 arena 生命周期、引用计数，以及一条最致命的原则——**「非常仔细地审查」**。

对 53 万行代码来说，「非常仔细」不是一句可以兑现的承诺。

## 二、「保持理智、不要犯错」本身就是一个 bug 模式

Bun 团队当时已经在做着行业内顶级的稳定性工作：

- 给 Zig 编译器打补丁加入 Address Sanitizer 支持，每次提交都跑 ASAN
- Windows 上使用 Zig 的 ReleaseSafe 编译（保留安全检查）
- 使用 Fuzzilli 对 JavaScript 引擎做 24/7 持续模糊测试
- 大量的端到端内存泄漏测试

这已经比大多数项目做得多了。**但 bug 仍然在出现**。

利用户的话来说——"我不想每天晚上睡前都在担心 Bun 的 crash。"

引入智能指针、制定代码风格规范——这些都是可选项。TigerBeetle 有 TigerStyle，Google 有 31,000 字的 C++ 风格指南。但风格指南靠 code review 执行，而 code review 靠人，人靠不住。

Jarred 也说出了自己的顾虑：

> 在 Zig 里写智能指针，失去了 Rust 的编译器保证，却承担了所有复杂度。

## 三、为什么不选 C++？

Bun 已经嵌入了大量 C/C++ 库：JavaScriptCore、uWebSockets、BoringSSL、SQLite。大约 20% 的代码本身就是 C++。

C++ 能带来构造/析构函数，能删除大量 `extern "C"` 胶水代码。但 Jarred 的判断是：C++ 仍然依赖风格指南和人工审查，仍然会让内存问题漏网。

**编译器错误是最好的反馈回路。** 这是 Rust 的核心论点。

## 四、那个疯狂的 11 天

这里的关键问题，不是「Rust 重写好不好」，而是「AI 能不能帮我们做这件事」。

Jarred 的原计划是用 Rust 风格智能指针在 Zig 里修稳定性——但说实话，他不想干。于是产生了一个念头：**「我能不能花一周时间测试 Anthropic 的新模型能不能把 Bun 重写成 Rust？」**

他最初也不认为会成功。几天后，一个高比例的测试套件开始通过了。

### 准备工作：3 小时的对话

Jarred 花了大约 3 小时和 Claude 讨论如何把 Zig 模式映射到 Rust。这次对话被序列化为一份 `PORTING.md`，后来还上了 Hacker News。

下一个问题：**如何把手动管理的内存的代码映射成 Rust 的生命周期？**

他的做法是写一个「动态工作流」，让 Claude：

1. 阅读代码库中每个结构体的每个字段，追踪控制流
2. 对每个复杂生命周期的字段，提出生命周期的建议
3. 使用 2 个 adversarial review 代理审核建议
4. 应用反馈后序列化到 `LIFETIMES.tsv` 文件中供其他 Claude 参考

然后再做一轮 adversarial review，交叉检查 `PORTING.md` 和 `LIFETIMES.tsv`。

他本人也手动通读了一遍。

### 试运行 + 各种翻车

在要求 Claude 翻译所有 1,448 个 `.zig` 文件之前，先试了 3 个文件。每个文件的工作流是：1 个实现者写 `.rs`，2 个 adversarial reviewer 检查，1 个 fixer 应用修改。

然后就是各种翻车：

- 有两个 Claude 互相踩——一个跑 `git stash`，另一个跑 `git stash pop`
- 另一个运行 `git reset HEAD --hard`
- 如果放进独立 worktree，磁盘空间不够

Jarred 的解决方式：**修改工作流指令，禁止任何不是「一次性提交单个文件」的 git 命令。** 然后分成 4 个 worktree，每个 worktree 跑 16 个 Claude。

### 写代码的峰值：每分钟 1300 行

在并行化的巅峰时，Claude 每分钟写了约 1300 行代码。**每一行代码都经过 2 个 adversarial reviewer 审核 + 1 个 fixer 修改后方才提交。**

结果：**没有一行能跑。** 这是预期行为。

他还发现了一个硬件问题——EC2 实例的默认 IOPS 不够。一个 `grep` 命令就能冻结磁盘读写好几分钟。

### 编译器错误作为工作队列

写完所有代码后，下一个工作流是 crate 级别的 `cargo check` 错误修复。

最大的坑是**循环依赖**。Zig 代码库是单一编译单元（相当于一个 crate），而 Rust 需要拆成约 100 个 crate。Jarred 之前尝试预先处理这个问题的 PR 不够用，于是又跑了一个工作流来分类循环依赖的归属。

这轮揭示了 **约 16,000 个编译错误**。对人类来说天文数字，对 64 个 Claude 来说不是。

他的工作流模式：
1. 对每个 crate 跑 `cargo check`，按文件分组保存错误
2. 修复该 crate 内所有编译器错误
3. 2 个 adversarial reviewer 审核
4. 1 个 fixer 应用修复

又翻了一次车：**Claude 把「让所有 crate 通过编译」理解成了「stub 掉有编译错误的函数」**，还开始加很长的注释来合理化 workaround。

Jarred 的反制：在 adversarial reviewer 的规则里加了一条——

> 「如果你需要写一段话来说明为什么 workaround 是可以接受的，那代码就是错的——修好它。」

### 从 smoke test 到全部通过

通过编译后，链接错误、panic、各个 CLI 子命令逐步修复。

然后跑测试。先本地，再做 CI。

这个阶段拿出了更强的隔离手段：`systemd-run`（cgroups）限制内存和 CPU、隔离 PID 命名空间。机器还是爆了几次磁盘。

**两天后，Linux CI 全绿。** 从 972 个失败测试文件降到 0。

### 最终数据

| 指标 | 值 |
|------|-----|
| 时间 | 11 天（5 月 3 日 → 5 月 14 日） |
| 提交次数 | 6,778 |
| 峰值并行数 | 64 个 Claude |
| 输入 tokens | 59 亿（未缓存） |
| 输出 tokens | 6.9 亿 |
| 缓存读取 | 720 亿 |
| API 成本 | 约 16.5 万美元 |
| 删除的测试 | 0 |
| 人工等效工作量 | 3 个全上下文工程师 × 1 年 |

> 「手工做这件事，3 个完全了解代码库的工程师大概需要 1 年。在此期间我们不能修 bug、不能加功能、不能做安全修复。这根本不是一个可行的选项。现实的选择是：什么也不做，永远修文章开头那些 bug。」

## 五、迁移到 Rust 之后的真实收益

### 5.1 内存使用大幅下降

最核心的原因——**Drop**。

Zig 的 `defer` 需要在**每个调用点**显式写清理代码，一个遗漏就是泄漏，错误路径上写两次就是 double-free。Rust 的 `Drop` 在值离开作用域时自动运行。

效果最明显的例子：**`Bun.build()` 内存在循环调用下不再泄漏。**

```js
// 在同一个进程内反复构建同一个项目 2000 次
for (let i = 0; i < 2000; i++) {
  await Bun.build({
    entrypoints: ["./index.js"],
    minify: true,
    sourcemap: "external",
  });
}
```

| 构建次数 | v1.3.14 (Zig) | v1.4.0 (Rust) |
|----------|---------------|---------------|
| 500      | 1,914 MB      | 526 MB |
| 1,000    | 3,506 MB      | 586 MB |
| 1,500    | 5,097 MB      | 608 MB |
| 2,000    | 6,745 MB      | 609 MB |

**每次构建泄漏约 3MB 的问题被系统性解决。** 之前用 Zig 做同样的事（PR #24741）没有合并，就是因为没有等价于 Drop 的机制让人无法有足够的信心。

### 5.2 二进制体积缩小 20%

- Windows：94 MB → 76 MB（-19%）
- Linux：88 MB → 70 MB（-20%）

部分原因：Zig 的 `comptime` 用得太多了，Rust 的表现更经济。

加上 ICU 数据优化、Identical Code Folding 等链接器优化，12-18 MB 就这么省下来了。

### 5.3 性能提升 2-5%

Rust 支持 C/C++ 与 Rust 之间的跨语言链接时优化（LTO），经过编译计算的删除可以实现跨编程语言的内联。

| 框架 | v1.3.14 | v1.4.0 | 提升 |
|------|---------|--------|------|
| Bun.serve | 169.6k req/s | 177.7k req/s | +4.8% |
| node:http | 103.8k | 108.5k | +4.5% |
| Elysia | 158.9k | 163.3k | +2.8% |
| Express | 64.5k | 66.6k | +3.2% |
| Fastify | 91.5k | 95.9k | +4.8% |
| next build | 13.62s | 13.03s | +4.5% |
| tsc -b --force | 0.94s | 0.89s | +4.7% |

### 5.4 修复了 128 个历史 bug

v1.4.0 相较于 v1.3.14 修复了 128 个 bug，覆盖内存泄漏、crash、甚至帮助文本颜色错误。

## 六、重写的代价：19 个回归

这次迁移不可避免地引入了 19 个已知回归，每个都已修复。Jarred 坦然承认了这些错误，也分享了最有启发性的几个教训：

### `debug_assert!` 中的副作用

Zig 的 `assert` 是一个函数——参数**在所有构建中都会执行**。Rust 的 `debug_assert!` 是宏——**release 构建中整个表达式被擦除**。

有个 `insert_stale` 调用包装在 `debug_assert!` 里，release 构建下就不再运行了。结果 React Hot Module Replacement 在某些情况下无声地坏了。Debug 构建正常工作，release 构建崩溃。

### 奇数长度字节切片的差异

Zig 的 `reinterpretSlice(u16, bytes)` 使用 `@divTrunc`，静默忽略最后一个奇数字节。`bytemuck::cast_slice` 直接 panic。

`Blob.text()` 在 UTF-16 BOM 后跟奇数个字节时 panic 了整个进程。

修复方案很务实：回到忽略奇数位——`&buf[..buf.len() & !1]`。

### Bounds checks 的差异

macOS/Linux 上 Zig 用 `ReleaseFast` 模式编译（去除边界检查），Rust 的 release 构建**保留边界检查**。

模块解析器把长文件名内联到一个全局列表中，溢出到 overflow blocks。Zig 的原始代码把每个 block 大小设为 270,272，一个占位符值比原意低 30 倍。真实项目触顶了。Rust 用 panic 代替写出界——这里是安全的信号。

### `comptime` 与宏的区别

Zig 的 `comptime` 参数可以让格式化参数在字符串解析之前就替换好。Rust 没有 `comptime`，导致 `Output::pretty` 在 ANSI 转义码解析时吞掉了参数。

结果是 `bun update -i` 的 OSC 8 超链接尾部 `\` 紧挨着 `<r>` 标记，标记解析器吃掉了转义符，打印出 "oxfmtr" 而不是 "oxfmt"。

修复方案：`bun_core::pretty!` 宏。

这些回归案例的高价值在于：**它们不是「写错了」，而是两种语言的语义差异**——语法上看似相同，语义上截然不同。这在 AI 驱动的跨语言迁移中是最高发的错误类别。

## 七、Prisma 的背书与 Claude Code 的无声切换

Prisma 在 Rust 重写版本上启动了 Prisma Compute 公测。他们的技术负责人 Alexey Orlenko 的原话：

> 「我们遇到了内存泄漏和 VM 暂停/恢复后连接池无法恢复的问题。Rust 重写版本处理后，它完美处理了这些故障模式。」

Claude Code v2.1.181 是第一个使用 Rust 版 Bun 的发布版本。从生产遥测数据来看：Linux p50 启动时间从 517ms 降到 464ms——10% 的加速。**「几乎没人注意到这件事。无聊就是好东西。」**

## 八、更大的图景：AI 时代的软件重写范式

我们需要退后一步看这件事的**真正意义**。

### 不是「Rust 赢了、Zig 输了」

Jarred 反复强调：Zig 让 Bun 成为可能。「如果没有 Zig，我们不可能有今天。」

这不是语言之争。这是一个**基于所有权和生命周期进行系统性资源管理的软件在混合 GC/手动内存模型中的结构性缺陷**。

C++ 在理论上可以做到 Rust 能做的事。Google、Microsoft 能用风格指南勉强管住。但问题的本质不是「这个语言能不能」，而是**编译器能不能在写代码时告诉我做错了**。

风格指南靠人执行。编译器错误**不靠人**。

### AI 把「不可能」变成了「一个工程问题」

这是我认为这篇博文最重要的段落：

> 「如果手工重写，3 个全上下文工程师大概需要 1 年。那意味着 1 年不能修 bug、不能做安全更新、不能加功能。现实的选择是：要么不重写，要么永远修文章开头那些 bug。」

AI 没有消除重写的风险——它把 **「风险 x 工作量」的乘积降低了两个数量级**。£16.5 万美元的 API 费用、11 天的工期、64 个并行 Claude——这些东西加起来，对于大多数公司来说是**一个可以接受的工程预算**，而不是一个不可能的任务。

### 「Claude Code 动态工作流」模式

Jarred 这个项目的核心架构不是 Rust，而是 **Claude Code 的动态工作流（Dynamic Workflows）**——把跨 64 个 Claude 实例的协作组织成一个循环：

```
while (task = todoList.pop()) {
  result = task()
  feedback = await Promise.all([review(result), review(result)])
  await apply(feedback, result)
}
```

关键洞察：
1. **拆开实现者和审查者**——写代码的那个 Claude 不审查，审查的那个不写代码
2. **每个任务配 2 个 adversarial reviewer**——他们的唯一工作是「找到代码不work的理由」
3. **修复的是流程，不是代码**——当一个模式出现问题（比如长注释合理化 workaround），他修改的是工作流规则，而不是手动修复每个实例

这是 **AI 时代的 TDD**：不是测试驱动开发，而是 **Review-Driven Development**——审查驱动开发。

## 八、Bun 的未来

Bun v1.4.0 是第一个 Rust 版本。目前约 4% 的代码在 `unsafe` 块内（约 13,000 个 `unsafe` 关键字），其中 78% 是单行——来自 C++ 的指针或调用 C 库。这个比例会随时间下降。

Bun 仍然嵌入了 JavaScriptCore，仍然需要 C/C++ 接口。它不会变成一个「纯 Rust 项目」。但那部分用 unsafe 封装的边界比内部的 Zig 代码更容易审计。

团队已经在运行的保障体系：
- Rust 的 borrow checker（CI 中完整运行）
- Miri（在 CI 中对不断增长的部分代码运行）
- LeakSanitizer 集成，追踪所有原生内存分配
- 24/7 coverage-guided 模糊测试覆盖所有解析器（JS、TSX、CSS、JSON5、TOML、YAML、Markdown、INI、Bun Shell 脚本、semver 范围、.patch 文件、CSS 颜色等）
- 自动提交 PR：fuzzer 发现的 bug 自动送给 Claude 生成修复 PR

「一个工程师在一年前能做的事，比今天少得多。」

## 结语

Bun 的 Rust 重写是一个信号。不是「Zig 不够好」——Zig 仍然是做系统级编程最有趣的语言之一。不是「Rust 适合所有事」——Rust 的学习曲线不会凭空消失。不是「AI 马上要取代程序员了」——这个项目有一个顶级工程师紧盯每一个步骤。

这个信号是：

> **软件工程中那些因为「代价太高」而被搁置的决策，现在需要重新审视。**

过去，重写一个 53 万行代码的生产项目是不现实的。过去，跨语言迁移意味着冻结功能开发一年。过去，在混合内存模型的项目里修内存泄漏更像哲学追求而不是工程目标。

这些「过去」正在快速变成「过去式」。

> 原文：[Rewriting Bun in Rust](https://bun.com/blog/bun-in-rust) — Jarred Sumner @ Bun / Anthropic

> 原创技术博客 · 开源项目架构深潜 · [idao.fun](https://idao.fun)