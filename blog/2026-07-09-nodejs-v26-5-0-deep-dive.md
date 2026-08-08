> 2026 年 7 月 8 日，Node.js 发布 v26.5.0（Current 版本）。在 v26 生命周期过半的这个节点，这个版本呈现出一种「温和但深刻」的气质——有几个一眼就能看到价值的新特性，但真正有意思的东西埋在提交日志的细节里。

## Notable Changes 概览

本次发布包含 5 个 SEMVER-MINOR（向后兼容的新功能）：

| 特性 | 模块 | PR |
|------|------|----|
| `blob.textStream()` | buffer | [#64036](https://github.com/nodejs/node/pull/64036) |
| `--experimental-import-text` | esm | [#62300](https://github.com/nodejs/node/pull/62300) |
| 逐轮询采样延迟 | perf_hooks | [#62935](https://github.com/nodejs/node/pull/62935) |
| 暴露 ReadableStreamTee | stream | [#64195](https://github.com/nodejs/node/pull/64195) |
| 报告协商的 TLS 组 | tls | [#64119](https://github.com/nodejs/node/pull/64119) |

外加一条值得单独提及的变更：**Addons import 支持默认启用**（[#64221](https://github.com/nodejs/node/pull/64221)）。

---

## 一、`--experimental-import-text`：文本文件的一等公民导入

```js
import text from "./README.md?text" with { type: "text" };
```

这是本次发布中最具「DX 改善」意义的新特性。在此之前，导入非 JS 资源必须走 `fs.readFileSync` + 手动缓存，或者借助加载器钩子。`--experimental-import-text` 允许以 `?text` 查询参数直接导入文本文件，返回值是 **包含文本内容的字符串**。

这个特性的本质是在 ESM 模块加载器的环节中插入一条新管道——当命中 `?text` 查询参数时，跳过 JavaScript 解析，直接将文件内容作为字符串默认导出。

使用方式：

```bash
node --experimental-import-text app.mjs
```

对比现有的同类机制：

| 机制 | 适用场景 | 原理 |
|------|---------|------|
| `fs.readFileSync` | 任意位置 | 运行时同步 I/O |
| 自定义 Loader | 任意资源类型 | 需要额外加载器脚本 |
| `?text` import | ESM 模块 | 编译时由加载器处理 |
| `?raw` (Vite/Rsbuild) | 构建工具 | 构建时处理 |

**为什么重要？** 它降低了 ESM 中嵌入文本资源的心智成本——配置文件、SQL 查询、模板字符串、GraphQL 查询语句——这些场景不再需要额外封装。虽然 Vite 等构建工具早已通过 `?raw` 做了类似的事，但 Node.js 运行时原生支持意味着 **不依赖构建工具的项目也能获得相同的开发体验**。

需要注意：该特性仍处于 experimental 阶段，后续 API 可能变化。

---

## 二、`ReadableStreamTee` 正式暴露：流的分叉能力

Matteo Collina（Node.js 流模块的核心维护者、Fastify 创始人）提交了 [#64195](https://github.com/nodejs/node/pull/64195)，将 `ReadableStreamTee` 暴露给用户。

**Tee 是什么？** 它是一个底层操作——将一个 `ReadableStream` 分叉（fork）成两个独立的流。每个分支独立消费，互不影响。

```js
const { ReadableStreamTee } = await import("stream/web");
// 或
const { ReadableStreamTee } = require("stream/web");

const [branch1, branch2] = ReadableStreamTee(originalStream);
```

**真实场景：**
- 同一个 HTTP 响应需要同时写入文件 + 发送给客户端
- 日志流需要同时写入磁盘 + 实时分析管道
- 视频/音频转码需要同时喂给多个编码器

之前实现这个需求必须自己维护缓冲队列或使用第三方库。现在 Node.js 原生支持，意味着 **零拷贝、基于底层流机制的分叉**——效率远高于用户空间实现的版本。

---

## 三、perf_hooks：逐轮询事件循环延迟采样

Pablo Erhard 提交的 [#62935](https://github.com/nodejs/node/pull/62935) 在 `perf_hooks` 中加入了一个新能力：**在每个事件循环迭代中采样延迟**。

```js
const { monitorEventLoopDelay } = require("perf_hooks");
const h = monitorEventLoopDelay({ samplePerIteration: true });
h.enable();
// ... 一段时间后
console.log(h.mean);   // 平均延迟
console.log(h.p99);    // P99 延迟
```

这个改动看似微小，但对 **生产环境中 Node.js 性能观测的精确度** 有本质影响。

之前 `monitorEventLoopDelay` 的实现基于**定时器轮询**——固定间隔检查事件循环是否繁忙。这在低负载下足够准确，但在高负载下会丢失精度，因为轮询间隔本身就可能被延迟。

逐轮询（per-iteration）采样将检测点放在 **事件循环每次从头开始迭代的时刻**。这意味着：

1. **更精确的高负载数据**——每次事件循环 tick 都会被记录
2. **更早检测到慢路径**——不再依赖于固定间隔的「抽样」
3. **更低的开销**——轮询本身不再消耗额外 CPU

对于 APM 工具和 Node.js 性能监控平台来说，这意味着可以更早、更准确地检测到事件循环阻塞——比如一个 `JSON.parse` 阻塞了 50ms 的事件循环，之前的轮询机制可能只能「大概感觉到」，现在可以精确量化。

---

## 四、TLS 协商组报告：加密透明度的提升

Filip Skokan 提交的 [#64119](https://github.com/nodejs/node/pull/64119) 让 TLS 连接可以报告**实际协商使用的加密组**。

```js
const server = tls.createServer({
  key: fs.readFileSync("server-key.pem"),
  cert: fs.readFileSync("server-cert.pem"),
}, (socket) => {
  console.log(socket.getNegotiatedGroups());
  // 例如: { keyExchange: "X25519", cipher: "TLS_AES_256_GCM_SHA384" }
});
```

这本质上是暴露了 TLS 握手完成后协商的加密参数。在安全审计、合规性检查和零信任架构的场景中，这很有价值：

- **审计**：验证是否所有连接都使用了预期的加密套件
- **合规**：PCI DSS、GDPR 等标准要求记录加密参数
- **调试**：快速确认客户端与服务器之间的握手结果

菲利普·斯科坎是 Node.js 安全与加密模块的核心贡献者，近几个版本中他持续在加密透明度上做工作（之前的有 EdDSA 验证、crypto typings 等）。

---

## 五、Addons import 支持默认启用

Chengzhong Wu 提交的 [#64221](https://github.com/nodejs/node/pull/64221) 是一个容易被忽略的「基础设施级」改进：**Node.js 原生 addons（.node 文件）现在默认支持通过 `import` 加载。**

```js
import nativeAddon from "./addon.node";
// 之前只能用 require("./addon.node")
```

这意味着混合使用 C++ 原生模块的 Node.js 项目可以 **完全过渡到 ESM**，不再需要一个 `createRequire` 的 hack。对 napi-rs、node-gyp 生态来说，这是一个长期需要的拼图。

---

## 六、流模块的大修——不只是 ReadableStreamTee

v26.5.0 中 stream 模块的变化远不止一个 Tee 暴露。Matteo Collina 和 Trivikram Kamat 在流模块上做了一轮密集的清理和优化：

### 6.1 降低 WHATWG 流每块开销

Matteo Collina 的 [#64252](https://github.com/nodejs/node/pull/64252) 值得注意的是用词——"cut per-chunk overhead"。WHATWG Streams 规范在设计上偏向正确性而非性能，导致每一块数据流过时都有不小的函数调用和 promise 创建开销。这次优化针对热点路径做减法。

### 6.2 abort 信号的全面集成

Trivikram Kamat 在这个版本中提交了**一连串**关于 abort 信号正确处理的修复：

- [#63997](https://github.com/nodejs/node/pull/63997) — 迭代消费者尊重 abort 信号
- [#64066](https://github.com/nodejs/node/pull/64066) — 在 abort 时拒绝迭代消费者
- [#64015](https://github.com/nodejs/node/pull/64015) — `pipeTo` 等待源时观测 abort
- [#64013](https://github.com/nodejs/node/pull/64013) — 修复待处理源的 merge abort

它们共同揭示了一个趋势：**AbortController/AbortSignal 正在渗透 Node.js 流式编程的每一个角落。** 当用户中断一个操作时，应该尽快释放资源，而不是继续无意义地拉取数据。

### 6.3 半开双工流的异步迭代

Efe 的 [#64275](https://github.com/nodejs/node/pull/64275) 修复了半开（half-open）模式下双工流在 `for await...of` 中的行为——流在一个方向关闭后，另一个方向应继续可用。

---

## 七、隐藏的安全加固浪潮

这个版本有几处安全相关的改动值得一提：

### 7.1 拒绝小阶 EdDSA 点

Filip Skokan 在 [#64026](https://github.com/nodejs/node/pull/64026) 中为 EdDSA（Ed25519/Ed448）验证过程增加了小阶点检查。

这是一个密码学层面的安全加固。小阶点攻击是一种针对椭圆曲线实现的侧信道——攻击者可以构造位于低阶子群上的点，诱导验证者接受无效签名。主流密码学库（如 libsodium）已经内置这种防护，但 Node.js 的加密实现需要显式添加。

### 7.2 QUIC 丢弃超大 CID 的版本协商包

Mohamed Sayed 在 [#64228](https://github.com/nodejs/node/pull/64228) 中修复了 QUIC 实现处理超大 Connection ID 时的行为。从安全角度看，这属于**输入验证**——攻击者可以发送畸形版本协商包，触发未定义行为。

### 7.3 大 DH 生成器验证

Tobias Nießen 在 [#64092](https://github.com/nodejs/node/pull/64092) 中修复了 Diffie-Hellman 参数验证中一个涉及大生成器的边缘情况。这属于密码学参数验证的完整性改进。

### 7.4 大 RSA 指数处理

同样来自 Tobias Nießen 的 [#64093](https://github.com/nodejs/node/pull/64093) 处理了 X.509 证书中异常大的 RSA 指数——这一般不会在正常证书中出现，但恶意构造的证书可以导致拒绝服务。

### 7.5 inspector crash 修复

ympark2011 的 [#64209](https://github.com/nodejs/node/pull/64209) 修复了向已关闭的 inspector socket 写入导致 crash 的问题。这属于**典型的竞态条件**——调试器断开连接时，试图发送消息的代码路径没有做状态检查。

---

## 八、底层依赖更新

| 依赖 | 版本 | PR |
|------|------|----|
| undici | 8.7.0 | [#64282](https://github.com/nodejs/node/pull/64282) |
| nghttp3 | 1.17.0 | [#64182](https://github.com/nodejs/node/pull/64182) |
| SQLite | 3.53.3 | [#64180](https://github.com/nodejs/node/pull/64180) |
| c-ares | cherry-pick 补丁 | [#64110](https://github.com/nodejs/node/pull/64110) |
| V8 | 3 个 backport | [#64101](https://github.com/nodejs/node/pull/64101) |

其中 undici 8.7.0 值得关注——作为 Node.js 内置的 HTTP 客户端库，undici 的每次大版本更新都直接影响到 `fetch()` 的行为和性能。

另外，V8 的三个 backport 来自 Kevin Gibbons——他是 V8 团队中负责 ECMAScript 规范兼容性的核心成员。这些 backport 通常涉及语言规范边缘情况的修复。

---

## 九、VFS（虚拟文件系统）持续成熟

v26.5.0 包含多个 VFS 相关修复：

- [#64285](https://github.com/nodejs/node/pull/64285) — 拒绝重命名为后代目录（防止逻辑错误）
- [#64163](https://github.com/nodejs/node/pull/64163) — 内存文件中当前位置哨兵的处理
- [#64165](https://github.com/nodejs/node/pull/64165) — 支持 `writeFileSync` 与虚拟 fd
- [#64168](https://github.com/nodejs/node/pull/64168) — 避免递归 readdir 符号链接循环
- [#64104](https://github.com/nodejs/node/pull/64104) — 从已打开 fd 读取 RealFSProvider 文件

VFS 是 Node.js 在 v22 时代引入的实验性特性，提供了一套可插拔的文件系统抽象层。到这个版本为止，VFS 的 API 覆盖度已经相当完整——读、写、目录遍历、符号链接处理、fd 抽象——基本的生产场景都已经覆盖。

它最大的想象空间在于：**测试中的 mock 文件系统、Serverless 环境中的内存文件系统、沙箱中的受限文件视图**。随着 API 的稳定，它可能会成为 Node.js 权限模型的重要组成部分。

---

## 十、其他值得注意的改动

### TextEncoder.encode 零拷贝

Yagiz Nizipli（Node.js 性能组核心贡献者）在 [#63897](https://github.com/nodejs/node/pull/63897) 中让 `TextEncoder.encode()` 避免了源字符串的复制。这属于**典型的底层性能优化——减少一次不必要的内存分配**。对于频繁调用 `TextEncoder` 的应用（如 Web 服务器序列化响应），累积效应可观。

### dgram 跳过 DNS 解析

Ruben Bridgewater 在 [#64133](https://github.com/nodejs/node/pull/64133) 中让 `dgram` 模块在面对纯 IP 地址时跳过了 `dns.lookup()` 调用。这也是一次**减法优化**——不要做不需要做的事。

### REPL 懒加载 acorn

Daijiro Wachi 的 [#63879](https://github.com/nodejs/node/pull/63879) 延迟了 REPL 的 acorn 解析器加载和 VM 上下文创建。对于 Node.js REPL 启动时间来说，这是一个立竿见影的优化——不需要解析器的场景不再支付解析器的加载成本。

### macOS x64 的 T2 支持即将结束

Antoine du Hamel 在 [#63931](https://github.com/nodejs/node/pull/63931) 中宣布：**Node.js 即将结束对 macOS x64 的 Tier 2 支持**。对于仍然在 Intel Mac 上开发的人来说，这是一个提前预警——未来的版本可能不再提供 x64 macOS 的官方二进制文件。

### Stress test 工作流

Joyee Cheung 在 [#64118](https://github.com/nodejs/node/pull/64118) 中添加了一个手动触发的 stress-test GHA 工作流。这体现了 Node.js 项目在测试基础设施上的持续投入——**压力测试不应该只在发布前手动跑，而是应该作为 CI 的可选项随时触发。**

---

## 十一、从 v26.5.0 看 Node.js 的发展方向

纵观本次发布的约 90 次提交，几个清晰的趋势浮现出来：

### 趋势 1：ESM 基础设施持续补全

从 `--experimental-import-text`、Addons import 默认启用，到之前版本的 `--experimental-require-esm`，Node.js 在 ESM/CJS 互操作这座大山上稳步掘进。每一小步都在降低 ESM 的摩擦系数。

### 趋势 2：Stream 进入深度优化期

Matteo Collina 和 Trivikram Kamat 在 stream 模块上的密集工作是本轮发布中最突出的模式。WHATWG Streams 的实现正在从「能工作」走向「高效率」——per-chunk 开销削减、abort 信号集成、半开流行为修复。这是流式 I/O 在 Node.js 中日益重要的自然反映。

### 趋势 3：安全是常量，不是特例

五六个密码学和安全相关的修复分散在版本中——没有哪一个单独看来惊天动地，但合在一起构成了一个清晰的信号：**Node.js 的安全加固不再是「发现问题才修」，而是持续进行的工程实践。**

### 趋势 4：可观测性精细化

逐轮询事件循环延迟、TLS 协商组报告——这些不是日常开发会用到的东西，但它们服务于一个更广泛的趋势：**Node.js 正在成为更好的可观测性平台**。

---

## 结语

v26.5.0 不是一个大版本。它没有引入运行时的范式转变，没有破坏性的 API 变更。但它体现了 Node.js 作为 Current 版本应有的节奏——稳步推进、持续打磨、不放过边缘情况。

对于正在使用 Node.js v26 的开发者，**`--experimental-import-text` 是最值得立即尝试的新东西**。对于维护生产服务的团队，**stream 和 perf_hooks 的改进值得关注**。对于安全敏感的系统，**加密层面的修复应该触发一次依赖升级**。

Node.js v26 的生命周期还有约 6 个月才进入 LTS。按照这个节奏，接下来的几个 Current 版本值得持续关注。

> 发布原文：[Node.js v26.5.0 Release Notes](https://nodejs.org/en/blog/release/v26.5.0/)

> 原创技术博客 · 开源项目架构深潜 · [idao.fun](https://idao.fun)