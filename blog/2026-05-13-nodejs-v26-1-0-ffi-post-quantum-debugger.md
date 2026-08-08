> Node.js v26.1.0 发布于 2026 年 5 月 7 日，由 Antoine du Hamel 发布。这是 Node.js 26 发布线进入 Current 阶段后的第一个次要版本。它不是一个"大版本"，但 `node:ffi` 模块的加入——让 JavaScript 直接调用 C 动态库——为这个已经 17 年的运行时打开了一扇全新的门。

---

## 一、`node:ffi`：Node.js 的 FFI 大门打开了

v26.1.0 最引人注目的新特性是 `node:ffi` 模块。FFI（Foreign Function Interface）不是什么新技术——Python 有 `ctypes`、LuaJIT 有 FFI 库、Rust 通过 `extern "C"` 天然支持。但在 Node.js 中，调用原生 C 库一直只能通过 **N-API 原生插件**的路径——需要写 C++ 代码、用 `node-gyp` 编译、处理跨平台 ABI 兼容性。门槛高，迭代周期长。

`node:ffi` 改变了这一点：**在 JavaScript 中直接加载和调用动态库中的符号，不需要编译任何 C++ 代码。**

```javascript
import { dlopen, defineFFI } from 'node:ffi';

// 定义函数签名
const ffi = defineFFI({
  'hello': { 
    result: 'void', 
    args: [], 
    library: 'libhello' 
  },
  'add': { 
    result: 'int', 
    args: ['int', 'int'], 
    library: 'libhello' 
  }
});

// 直接调用
ffi.hello();               // 调用 C 函数
const sum = ffi.add(3, 4); // 返回 7
```

### 安全警告不是闹着玩的

FFI 模块的文档和发布说明反复强调同一个词："inherently unsafe"。这不是客套话：

- **无效指针导致段错误**：传一个野指针给 C 函数，整个进程直接崩
- **错误的函数签名让结果不可预测**：如果 C 函数期望 `double` 但你传了 `int`，返回值是垃圾数据
- **释放后的内存访问（use-after-free）**：C 层释放的内存，JS 侧仍然持有引用——这是经典的安全漏洞

正因如此，`node:ffi` 需要显式启用：

```bash
node --experimental-ffi app.mjs
# 如果启用了 Permission Model，还需要
node --experimental-ffi --allow-ffi app.mjs
```

### FFIFunctionInfo 与 Shared Buffer 优化

从 commit 记录中可以看到，在合并到主分支前，`node:ffi` 的实现经历了多轮优化：

- `FFIFunctionInfo` 被实现为 `BaseObject` 子类（#63071）
- 使用 `unique_ptr` 管理 FFI 内存，避免泄漏（#63071）
- `DynamicLibrary` 支持 `Symbol.dispose`，可以用 `using` 语法自动释放（#62925）
- **Shared Buffer 快速路径**：对数值和指针类型的参数/返回值，避免了不必要的缓冲区分配（#62918）

最后这个优化很关键。FFI 调用中大量场景是"传两个 int，拿一个 int 回来"——纯数值运算。没有 shared buffer 优化，每次调用都得创建一个 ArrayBuffer、写数据、读结果、回收；有了快速路径，数值类型直接在栈上传递，开销接近于零。

### 适用场景

`node:ffi` 不是要替代 N-API——后者更适合复杂、高频、生产级的原生集成。FFI 的定位是"快速黏合"：

- 对接一个只有 C API 的老旧硬件驱动
- 在原型阶段快速验证一个原生算法的可行性
- 调用系统级 API（Linux ioctl、Windows Win32 API）
- 临时集成一个只提供 `.so`/`.dylib`/`.dll` 的闭源库

---

## 二、后量子密码学：ML-KEM 和 SLH-DSA

Node.js 在密码学支持上一直跟进得很快。v26.1.0 中，Filip Skokan 贡献了多个 Web Crypto API 的改进，其中最引人注目的两项是对 **ML-KEM** 和 **SLH-DSA** 的 JWK（JSON Web Key）支持。

### ML-KEM（FIPS 203）

ML-KEM（Module-Lattice-Based Key-Encapsulation Mechanism）是美国国家标准与技术研究院（NIST）选定的后量子密钥封装标准。它在 2024 年 8 月正式定为 FIPS 203 标准。

简单说，ML-KEM 解决的是"如果量子计算机能破解 RSA 和 ECDH，我们怎么安全地交换密钥"这个问题。它基于格密码学（lattice-based cryptography），目前没有已知的量子攻击算法能有效破解它。

### SLH-DSA（FIPS 205）

SLH-DSA（Stateless Hash-Based Digital Signature Algorithm，原名 SPHINCS+）是无状态哈希签名算法，FIPS 205 标准。它的安全性依赖于哈希函数的抗碰撞性——这是一个经过数十年密码分析检验的安全性假设。

### JWK 集成

Node.js 在 v26.1.0 中为这两种算法增加了 JWK 格式的导入/导出支持：

```javascript
const publicKey = await crypto.subtle.importKey(
  'jwk',
  jwkData,
  { name: 'ML-KEM-768' },
  true,
  ['wrapKey']
);
```

这意味着 JavaScript 生态的应用可以用标准的 JWK 格式交换后量子密钥，而不需要自己设计序列化格式。对于合规性要求高、生命周期长的系统（PKI、代码签名、长期存储加密）来说，这是开始**为量子计算时代做准备**的实用基础设施。

---

## 三、调试器：不再需要编辑代码的运行时探针

`node inspect` 调试器在 v26.1.0 中获得了一个 Joyee Cheung 贡献的新能力：**编辑无关的运行时表达式求值**。

### 为什么重要

传统的调试流程是：发现问题 → 修改代码（加 `console.log`）→ 重启应用 → 复现问题。这个过程在大规模、有状态的应用（如数据库连接池已经建立的服务）中代价很高——重启意味着丢失运行时上下文。

新引入的 ProbeInspectorSession 模式允许调试器在运行时动态注入表达式，而不需要修改源文件。它通过 Chrome DevTools Protocol 的 `Runtime.evaluate` 能力实现，但做了更友好的 CLI 封装：

```bash
node inspect app.mjs
# 在断点处：
debug> .probe "userSession.getCurrentUser()"
# Node.js 在当前上下文中执行表达式，返回结果
```

这个功能在后端调试场景中的价值明显——不需要部署新代码就能探查运行时状态。生产环境谨慎使用，但开发阶段的调试效率会明显提升。

---

## 四、测试框架走向成熟

Node.js 内置的 `node:test` 在 v26.1.0 中获得多项改进，使它越来越接近一个独立的测试框架，而不是"那个顺便附带的测试库"。

### 测试顺序随机化

```javascript
// 让测试以随机顺序执行，检测顺序依赖
node --test --test-randomize
```

这个由 Pietro Marchini 贡献的特性（#61747）对测试质量有直接改善。测试之间的顺序依赖是一种很难 debug 的 bug——当测试 A 修改了全局状态而测试 B 依赖于 A 执行后的状态，测试顺序每次固定的情况下可能从未暴露问题。随机化执行顺序就是为了捕获这类测试。

### Mock Timers 与 AbortSignal

```javascript
import { mock, describe, it } from 'node:test';

describe('timeout handling', () => {
  it('should timeout after 5s', { concurrency: true }, async (t) => {
    // 模拟计时器
    t.mock.timers.enable();
  
    const controller = new AbortController();
    const timeout = AbortSignal.timeout(5000);
  
    // 计时器模拟覆盖 AbortSignal.timeout
    t.mock.timers.tick(5000);
  
    // 断言
  });
});
```

mock timers 现在能覆盖 `AbortSignal.timeout()`——这是 `node:test` 测试异步超时场景的关键能力。此前 mock timers 只能模拟 `setTimeout`/`setInterval`，但 `AbortSignal.timeout` 使用独立的内部计时器，无法被 mock 捕获。

### Mock Timeout API 对齐

```javascript
// 设置 mock 调用验证的超时时间
const fn = t.mock.fn();
fn.mock.timeout(1000);  // 等待 1 秒后如果未调用则超时
```

---

## 五、基础设施升级：一个"常规"版本的依赖更新密度

v26.1.0 的 deps 更新列表相当密集：

| 依赖               | 版本        | 亮点                                    |
| ------------------ | ----------- | --------------------------------------- |
| **OpenSSL**  | 3.5.6       | 安全修复，性能改进                      |
| **V8**       | 14.6.202.34 | Chromium 134 基线，ASM/WebAssembly 改进 |
| **npm**      | 11.13.0     | 包管理器持续更新                        |
| **undici**   | 8.2.0       | HTTP 客户端，连接管理改进               |
| **nghttp2**  | 1.69.0      | H2 协议栈更新                           |
| **llhttp**   | 9.4.1       | HTTP 解析器                             |
| **SQLite**   | 3.53.0      | 同上个版本 Bun 也升级到的版本           |
| **simdjson** | 4.6.1       | JSON 解析加速                           |
| **ngtcp2**   | 1.22.0      | QUIC 传输层                             |
| **corepack** | 0.34.7      | 包管理器管理器                          |
| **timezone** | 2026b       | 夏令时和时区更新                        |

### perfetto：性能追踪的新基础

值得单独提及的是 perfetto 的集成（#62397）。perfetto 是 Google 开发的性能追踪框架，被 Android 和 Chrome 广泛使用。Node.js 现在集成了 perfetto 构建文件和依赖（54.0 版本），这意味着未来 Node.js 的原生性能追踪能力——目前通过 `--trace-event-enabled` 或 `perf_hooks` 暴露——可能会走 perfetto 管道。

对于需要深度性能分析的场景（火焰图、跟踪事件流、GC 分析），perfetto 集成意味着更丰富的追踪数据和更好的工具链兼容性。

---

## 六、SQLite：序列化与列名优化

`node:sqlite` 模块在上个版本引入后，v26.1.0 带来了两项重要改进：

### serialize() / deserialize()

```javascript
import { DatabaseSync } from 'node:sqlite';

const db = new DatabaseSync(':memory:');
db.exec(`CREATE TABLE data (key TEXT, value TEXT)`);
db.prepare(`INSERT INTO data VALUES (?, ?)`).run('hello', 'world');

// 将整个数据库序列化为 Buffer
const buffer = db.serialize();
// buffer 包含完整的数据库状态——schema + 数据

// 从 Buffer 还原
const db2 = new DatabaseSync(':memory:');
db2.deserialize(buffer);
const row = db2.prepare(`SELECT * FROM data`).all();
// [{ key: 'hello', value: 'world' }]
```

这个功能在数据传输、缓存、快照场景中很实用——比如将内存数据库的内容序列化后发送到另一个进程。

### 列名内化

Ali Hassan 贡献的优化（#61954）将 ASCII 类型列的列名使用 `OneByte` 字符串内化（internalize）。在 SQLite 查询大量列时，这个优化减少了字符串比较和哈希的开销——对高吞吐的数据库查询有意义。

---

## 七、HTTP 与安全相关

### ClientRequest options merge 强化

Matteo Collina 修复了 `ClientRequest` 构造时的 options 合并逻辑（#63082）。此前在某些边缘情况下，用户提供的 options 可能会与默认值产生非预期的合并行为——特别是 `headers` 对象可能被就地修改而不是被拷贝。

### req.signal 加入 IncomingMessage

```javascript
const server = http.createServer((req, res) => {
  // req.signal 在请求被中止时触发
  req.signal.addEventListener('abort', () => {
    console.log('请求被客户端取消');
    cleanupExpensiveOperation();
  });
});
```

`IncomingMessage` 现在有 `signal` 属性（AbortSignal），当请求被客户端断开时触发。这对需要清理资源的场景很有用——比如用户取消上传后停止处理。

### no_proxy 后缀匹配修复

HTTP 代理的 `no_proxy` 配置现在正确支持前缀带点号的域名匹配（#62333）。`no_proxy=.example.com` 现在可以正确匹配 `sub.example.com`——此前实现只做了简单的前缀匹配。

---

## 八、QUIC：连接迁移的基础设施

Node.js 的 QUIC 实现（`node:quic`）在本版本获得了多项底层改进：

- **`QuicEndpoint.listening` 和 `QuicStream.destroy()`**：暴露连接状态和流的生命周期控制（#62648）
- **多 ALPN 协商**：服务器可以在同一个端点监听多个应用层协议（#62620）
- **TLS 上下文改进 + SNI 支持**：基于服务器名称指示选择不同的 TLS 证书（#62620）
- **Token 验证修复**：处理超时 token 的边界情况（#62620）
- **Arena 分配**：数据包使用 arena 内存池分配，减少碎片和分配开销（#62589）
- **Rapidhash**：替换 xxHash，用于快速哈希计算（#62620）

这些改进指向一个方向：Node.js 的 QUIC 正在从"能用"走向"适合生产"。特别值得关注的是 QUIC 作为 HTTP/3（`node:http3` 尚在开发中）的传输层——QUIC + HTTP/3 的全栈支持对实时通信和移动场景会有显著改善。

---

## 九、其他值得关注的变更

| 变更                                        | PR     | 作者              | 说明                                  |
| ------------------------------------------- | ------ | ----------------- | ------------------------------------- |
| `randomUUIDv7()`                          | #62553 | nabeel378         | UUID v7 格式                          |
| `fs.stat()` 支持 `signal`               | #57775 | Mert Can Altin    | 支持 AbortSignal 取消                 |
| `statfs` 暴露 `frsize`                  | #62277 | Jinho Jang        | 文件系统片段大小                      |
| `buffer.indexOf/lastIndexOf` `end` 参数 | #62390 | Robert Nagy       | 限定搜索范围                          |
| `util.styleText` 支持十六进制颜色         | #61556 | Guilherme Araújo | `styleText('color:#ff0000', 'red')` |
| `duplexPair` 传播销毁                     | #61098 | Ahmed Elhor       | 解决背压+销毁的竞态                   |
| `--enable-all-experimentals`              | #62755 | Paolo Insogna     | 一键启用所有实验特性                  |
| 空 `--experimental-config-file`           | #61610 | Marco Ippolito    | 配置文件的灵活处理                    |
| `ERR_REQUIRE_ESM_RACE_CONDITION`          | #62462 | Antoine du Hamel  | 检测混合模块加载竞态                  |
| `glob()` 支持 `followSymlinks`          | #62695 | Matteo Collina    | 控制符号链接行为                      |
| Win x64 PGO                                 | #62761 | Stefan Stojanovic | Windows x64 的 PGO 优化               |
| `napi_create_external_sharedarraybuffer`  | #62623 | Ben Noordhuis     | N-API 扩展                            |

---

## 十、这个版本意味着什么

回顾 v26.1.0 的全部变更，它不是一个"头条特性"驱动的版本。FFI 模块是唯一的大新闻，但真正有意思的是那些基础设施层面的信号：

第一，**Node.js 正在认真对待后量子密码学。** ML-KEM 和 SLH-DSA 的 JWK 支持不是什么"实验性标志"下的玩具——它们是在真实的 Web Crypto API 标准接口中实现的。对于安全敏感的应用，开始接触和测试后量子算法不再需要额外安装依赖。

第二，**内置测试框架在加速成熟。** 测试随机化、mock timers 对 AbortSignal.timeout 的覆盖——这些功能表明 Node.js 团队正在严肃地把测试基础设施当作一等公民，而不是需要"第三方框架补课"的薄弱环节。

第三，**perfetto 的引入暗示着性能工具的进化。** V8 已经有了内置的 tracing 支持，Node.js 过去通过 `--trace-event-enabled` 暴露一部分，但 perfetto 级别的集成意味着更丰富的 trace 事件、更好的工具链兼容性。这是 Node.js 在高性能服务端场景（游戏、实时通信、高频交易）中竞争的基础设施准备。

第四，**QUIC/HTTP3 的工程投入没有减速。** 多 ALPN、SNI 支持、arena 分配——这些都是生产级 QUIC 实现需要的工程细节。当 `node:http3` 模块最终稳定时，Node.js 可能成为第一个"原生支持 HTTP/3 的服务端运行时"，而不需要前面放一个 Caddy 或 Nginx。

v26.1.0 是一个"积累型"版本。没有突破性的语法变更，没有框架级的新功能。但 FFI 打开了原生调用的通路，后量子密码学为未来做好准备，QUIC 在继续深入——这些方向叠加起来，指向的是一幅更大的图景。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)