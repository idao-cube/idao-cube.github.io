> 2026 年 6 月 18 日，TypeScript 团队发布了 7.0 RC。这不是一个普通的 semver major 版本——TypeScript 编译器的核心从 TypeScript/JavaScript 完全移植到了 **Go**。结果是在大型代码库上 **常出现 10 倍的性能提升**。
>
> 这是微软继 2025 年底官宣 Go 移植计划之后，交出的第一份正式答卷。

## 一、这不是「另一个版本」，这是一次材料替换

要理解 TypeScript 7.0 的意义，需要先理解 TypeScript 编译器面临的根本性困境。

TypeScript 编译器（tsc）是用 TypeScript 本身编写的——这很常见：自举编译器在编程语言世界里并不罕见。但随着 TypeScript 代码库的增长，问题逐渐暴露出来：

1. **单线程** —— JavaScript 天生单线程（Worker 线程有共享内存的限制）
2. **大内存开销** —— 对大型 monorepo 来说，tsc 的峰值内存使用经常以 GB 计
3. **增量构建的先天劣势** —— 每次 `--watch` 模式的文件变更都需要重新处理大量 AST

TypeScript 团队在 2023-2025 年间做了大量优化（5.x 版本的 `--build` 模式优化、declaration map、isolatedDeclarations 等），但这些都是在 JavaScript 引擎的「天花板」下进行的修补。

**Go 重写从根本上改变了这个格局。**

共享内存并行、原生编译速度、无 GC 停顿——这些不是 JavaScript 运行时可以提供的特性。TypeScript 7.0 保留了与 6.0 **结构一致的类型检查逻辑**（不是重写逻辑，而是「直译」了实现），然后在 Go 的并发模型上重新部署整个管道。

Daniel Rosenwasser（TypeScript PM）的原话：

> 「新代码库是从现有实现方法式地移植而来，而非从头重写，其类型检查逻辑在结构上与 TypeScript 6.0 相同。这种架构同构性确保编译器继续执行你已依赖的完全相同语义。」

## 二、性能：10 倍从哪里来？

### 2.1 解析和发射的天然并行

TypeScript 构建管道中的某些步骤——解析（parsing）和发射（emitting）——在不同文件之间几乎完全独立。在 Go 中，这些步骤可以无摩擦地并行化。

### 2.2 类型检查器的共享内存并行

类型检查是最复杂的部分。一个文件的类型信息依赖于它所导入的其他文件——你不能简单地把每个文件分给不同线程独立检查。

TypeScript 7.0 的解决方案：**创建一个固定数量的类型检查器 worker**（默认 4 个），每个 worker 有自己的「世界观」。它们可能会重复一些公共工作（比如检查全局类型声明），但由于输入相同、划分策略一致，**结果总是确定的**。

```bash
# 调整并行度
npx tsc --checkers 8   # 大型代码库增加并行度
npx tsc --checkers 2   # CI 环境减少内存占用
```

新增的 `--checkers` 标志允许用户配置 worker 数量。需要留意——增加 worker 数通常意味着更高的内存使用。

### 2.3 项目引用构建器的并行化

对于 monorepo 场景，TypeScript 7.0 还引入了 `--builders` 标志，控制同时构建的项目引用数量。

```bash
npx tsc --build --builders 4
```

`--checkers` 和 `--builders` 有乘法效应——`--checkers 4 --builders 4` 允许最多 16 个类型检查器同时运行。找到适合你机器和代码库的平衡点很重要。

### 2.4 单线程模式

为了调试、对比性能或资源受限环境，新增了 `--singleThreaded` 标志，它会强制所有工作在单个线程中执行。

## 三、`--watch` 模式的重建：Parcel 的 Go 移植

TypeScript 6 及更早版本的 `--watch` 模式一直存在性能问题——在大项目中，尤其是 `node_modules` 繁多的 monorepo 中，文件监听器（watcher）的开销非常显著。

Go 标准库没有提供内置的文件系统监听 API。团队尝试了第三方库，发现存在稳定性、性能和跨平台支持方面的问题。他们构建了基于轮询的解决方案——**但在大规模项目中，纯轮询的计算开销太高。**

灵感来自 VS Code 多年来使用的 `@parcel/watcher`。Parcel 的 watcher 是用 C++ 编写的——依赖完整的 C++ 工具链来构建，这是 TypeScript 团队不想引入的。

于是他们做了一个「疯狂的工程决定」：**将 Parcel watcher 从 C++ 移植到 Go**，只保留极少量的汇编 shim。

结果是明确的成功——移植版通过了原有的测试套件，并进一步「Go 化」（从直译逐步重构为更符合 Go 习惯的实现），同时保持了跨平台的高效文件名变更检测。

Devon Govett（Parcel 作者）在致谢中被特别提及。这是一个跨越 C++ → Go → TypeScript 的生态级反馈循环。

## 四、从 5.x 到 7.0 的配置断层

TypeScript 7.0 继承了 6.0 的新默认值，并对废弃配置提供**硬错误**。这意味着如果项目从 5.x 直接跳到 7.0，将面临相当大的配置冲击。

新默认值一览：

| 配置项                           | 默认值                           |
| -------------------------------- | -------------------------------- |
| `strict`                       | `true`                         |
| `module`                       | `esnext`                       |
| `target`                       | 当前稳定 ECMAScript 版本         |
| `noUncheckedSideEffectImports` | `true`                         |
| `libReplacement`               | `false`                        |
| `stableTypeOrdering`           | `true`（不可关闭）             |
| `rootDir`                      | `./`（需显式设置源目录）       |
| `types`                        | `[]`（旧行为需设为 `["*"]`） |

其中 `rootDir` 和 `types` 是最容易造成「惊喜」的变更：

```json
// 如果 tsconfig.json 在项目根而非 src/ 内
{
  "compilerOptions": {
    "rootDir": "./src"  // 必须显式指定
  },
  "include": ["./src"]
}
```

`types` 从「自动加载所有 `@types/*`」变为「空列表」，意味着原来隐式可用的全局声明（如 `node`、`jest`、`bun`、`mocha`）现在需要显式声明：

```json
{
  "compilerOptions": {
    "types": ["node", "jest"]
  }
}
```

**已变为硬错误的废弃项：**

- `target: es5` — 完全移除
- `downlevelIteration` — 不再支持
- `moduleResolution: node/node10` — 推荐 `nodenext` 或 `bundler`
- `module: amd/umd/systemjs/none` — 推荐 `esnext` 或 `preserve`
- `baseUrl` — 不再支持（`paths` 改为相对于项目根）
- `esModuleInterop` 和 `allowSyntheticDefaultImports` — 不可设为 `false`
- `alwaysStrict` — 始终为 `true`
- `module` 关键字不可在 namespace 声明中使用
- `asserts` 关键字不可在 import 上使用（须用 `with`）

TypeScript 团队的建议很坦诚：**先升级到 6.0，再迁移到 7.0。** 6.0 本身已经引入了同样的破坏性变更，但 7.0 将它们从不推荐（deprecation）升级到了强制执行。

## 五、模板字面量类型的 Unicode 代码点感知

这是 7.0 中一个有趣的有意破坏性变更。

```typescript
type HeadTail<S> = S extends `${infer Head}${infer Tail}` ? [Head, Tail] : never;

type Result = HeadTail<"😀abc">;
// 7.0:   ["😀", "abc"]
// 6.0:   ["\ud83d", "\ude00abc"]
```

之前 TypeScript 遵循 JavaScript 的 UTF-16 索引行为——将 `"😀"` 拆成两个半代理对（`\ud83d` 和 `\ude00`）。技术上与 JavaScript 的 `"😀abc"[0]` 行为一致，但几乎不是开发者的真实意图。

TypeScript 7.0 改为 Unicode 代码点感知——`for...of` 和 `[...str]` 的行为。这是有道理的：大多数人是 **think in code points**，不是 UTF-16 代码单元。

这会破坏一些在类型层面做字符串操作的工具库——比如那些用 template literal 实现字符串 `Length` 类型的。在实践中，我们预计新行为更有用，也**更少意外**。

## 六、JavaScript 支持的重构

TypeScript 的 JS 支持（基于 JSDoc 的类型推断）在 7.0 中也经历了重构，以更接近 `.ts` 文件的类型分析方式：

**不再支持的 JSDoc 模式：**

- 在类型位置使用值——必须写 `typeof someValue`
- `@enum` — 改用 `@typedef` 加 `keyof typeof`
- 独立的 `?` 作为类型——改用 `any`
- `@class` — 改用 `class` 声明
- 后缀 `!` — 直接用 `T`
- Closure 风格的函数类型语法 `function(string): void` — 改用 `(s: string) => void`
- 类型名必须定义在 `@typedef` 标签内，而非紧贴在标识符旁

这些变化对纯 JS 项目的类型检查有影响。团队维护了一个 [`CHANGES.md`](https://github.com/microsoft/typescript-go/blob/main/CHANGES.md) 追踪详细差异。

## 七、与 TypeScript 6.0 的共存策略

TypeScript 7.0 的稳定程序化 API 要到 7.1 才能就绪。在这之前，如何让依赖 `typescript` 包的生态工具（如 typescript-eslint）继续工作？

微软的解决方案：**`@typescript/typescript6` 兼容包**。

```bash
# 安装 TS 6.0 兼容包（提供 tsc6 命令）
npm install -D typescript@npm:@typescript/typescript6@^6.0.0

# 同时安装 TS 7.0 RC（提供 tsc 命令）
npm install -D typescript-7@npm:typescript@rc
```

这个方案的核心思路是通过 npm aliases 让 `typescript` 包名指向 6.x，而 7.0 使用独立包名共存。同时，TypeScript 7 的 nightly 构建继续在 `@typescript/native-preview` 下发布，但正式版后将统一到 `typescript` 包。

## 八、编辑器体验与 LSP

TypeScript 7.0 基于语言服务器协议（LSP）构建，可以在任何支持 LSP 的编辑器中使用。针对 VS Code，团队发布了一个专门的 **TypeScript Native Preview 扩展**。

RC 阶段补全了 beta 中缺失的功能：

- 自动导入（Auto-imports）
- 可展开的 Hover 信息
- Inlay Hints
- Code Lenses
- Go-to-source-definition
- JSX Linked Editing 和 Tag Completions
- 语法高亮
- "Sort imports" / "Remove unused imports"

团队重建了测试和诊断基础设施，**对 GitHub 上最热门的 TypeScript 和 JavaScript 代码库进行 fuzz 测试**。数据表明，TypeScript 7 的语言服务器失败命令比 6.0 减少了 **20 倍以上**。

## 九、生态合作与验证

TypeScript 7 已经经过了大范围的实际验证：

- **微软内部** — 多个团队使用超过一年
- **外部合作伙伴** — Bloomberg、Canva、Figma、Google、Lattice、Linear、Miro、Notion、Slack、Vanta、Vercel、VoidZero 等
- **多百万行代码库** — 在多个百万行级的代码库上经过测试

反馈高度一致：**构建时间大幅缩短，编辑体验更轻量更流畅。**

VoidZero（Rolldown/Oxc 背后的公司）作为合作伙伴出现值得关注——他们在 Rust/Go 构建工具的生态位中与 TypeScript Go 移植形成了一个「工具链的南北桥」。

## 十、这是更大的图景：JavaScript 生态的「去 JS 化」

TypeScript 7 加入了一个正在形成的大趋势：**JavaScript 生态的核心基础设施正在离开 JavaScript。**

| 项目             | 原始语言    | 目标语言 | 状态   |
| ---------------- | ----------- | -------- | ------ |
| TypeScript (tsc) | TypeScript  | Go       | 7.0 RC |
| esbuild          | Go          | —       | 生产中 |
| Bun              | Zig → Rust | Rust     | v1.4   |
| Oxc              | —          | Rust     | 生产中 |
| Rspack           | —          | Rust     | 生产中 |
| Rolldown         | —          | Rust     | 开发中 |
| Parcel 2         | JS → Rust  | Rust     | 已发布 |
| Rome → Biome    | TS → Rust  | Rust     | 生产中 |

这个清单越来越长。原因是系统性的：**构建工具、编译器和 linter 的性能天花板在 JavaScript 运行时中已经碰壁了**，而这些工具恰好又是「CPU 密集型 + 高度可并行 + 可缓存」的理想工作负载，天然适合原生语言。

有趣的是，TypeScript 选择的是 Go 而不是 Rust。这与 Go 的简单性、快速的编译时间、以及更容易的跨语言移植有关。TypeScript 团队的 Go 移植保留了与 JS 实现相同的架构逻辑——他们不是重写类型系统，而是「翻译」它。Go 的 goroutine + 共享内存并发模型让并行化更加直接，不需要处理 Rust 的 borrow checker 带来的迁移成本。

## 十一、路线图与展望

当前计划：

- **一个月内发布 TypeScript 7.0 稳定版**
- 此后几个月发布 **TypeScript 7.1**（稳定程序化 API）

短期内最需要关注的两件事：

1. **工具的迁移** — typescript-eslint、ts-jest、Rollup 插件等依赖 `typescript` API 的工具需要适配 7.0
2. **配置迁移** — 从 5.x 直接跳到 7.0 的项目需要处理配置冲击

对于绝大多数 TypeScript 用户，如果已经在用 6.0 并设置了所有新默认值，迁移到 7.0 几乎是透明的——只是变得更快。

## 结语

TypeScript 7.0 不是一次「特性发布」。它是一个 **工程的声明**——用行动表明 JavaScript 生态的发展瓶颈可以被突破，而方法是换一种方式思考基础设施的构建。

我没有用「里程碑」这个词——用「分水岭」更合适。

从 7.0 RC 之后，TypeScript 编译器不再受限于单线程 JavaScript 运行时的性能边界。后续版本的创新速度——新语言特性、更智能的类型推断、更快的增量构建——都不再需要顾虑「这会让 tsc 变得更慢」。

在 Go 的抽象之上，TypeScript 团队获得了他们过去十年不曾拥有的工程自由度。

> RC 发布博客：[Announcing TypeScript 7.0 RC](https://devblogs.microsoft.com/typescript/announcing-typescript-7-0-rc/)
>
> 试用：`npm install -D typescript@rc`
>

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)