## 写在前面

前端框架之争吵了快十年。但坦白说，大多数争论卡在"React 好用还是 Vue 好用"的层面，很少有人真正追问：**这两个框架为什么从根上就是两套东西？** 它们的差异不是 API 设计喜好不同，而是对"UI 的本质应该怎么抽象"这个问题给出了完全不同的回答。

这篇文章从架构层面拆这两套方案：React 为什么选择在运行时重新实现一个操作系统调度器，Vue 为什么选择让编译器替你做大部分苦力。如果你只想快速上手哪个框架，这篇文章可能帮不上忙。但如果你想理解自己每天用的工具到底在做什么，它可能值得读。

---

## 一、分歧的起点——UI 的本质是什么

一切分歧从一个问题开始：**用户界面到底是什么？**

### React 的回答：UI 是状态的函数

React 团队的公式很简洁：

```
UI = f(state)
```

意思很直白：给定相同的状态，你应该得到完全相同的 UI。这是一个纯粹的数学映射。它的好处在于通用——框架不需要知道你的数据怎么变、变哪里，只需要在状态变化时重新执行整个函数，然后找出前后两次输出的差异。

但这个等式藏了一个代价：**你不知道状态从哪里变、变多少，所以每次更新都得从头算一遍**。React 的解法是虚拟 DOM diff——在全量计算和最小 DOM 操作之间找平衡。Fiber 架构是这个思路的极端体现：既然我不知道哪些数据变了，我就把"重新计算"做到足够快，快到用户感知不到。

### Vue 的回答：UI 是数据到 DOM 的绑定

尤雨溪选了另一条路。在他看来，`{{ message }}` 这个模板插值已经定义了"数据 `message` → 这个文本节点"的映射关系。编译器在构建时就能静态分析出这些绑定，运行时要做的事情不是"算出差异"，而是"精准推送"。

```
message → 追踪依赖 → 精确更新 DOM 节点
```

这里的优势是：**你不需要全量 diff，因为框架从一开始就知道谁依赖谁**。代价是：你得接受一套声明式的模板语法（或者遵守 Composition API 的使用规则），让编译器有迹可循。

### 这条分歧的后果

这两个回答直接决定了后续所有的架构决策：

- React 押注**运行时的通用调度**——既然不知道哪里变了，就把"找变化"这个过程做得足够快、可中断、可分优先级
- Vue 押注**编译时的静态分析**——既然我能提前知道绑定关系，那运行时只需要做最精准的更新

这不是技术能力的差异，而是对同一个问题的不同**取舍**。理解了这个前提，后面的技术细节就都能串起来了。

---

## 二、React Fiber——运行时的调度野心

### 2.1 Stack Reconciler 是怎么把自己逼到墙角的

React 16 之前，Reconciliation 的过程是递归调用的：

```
function renderComponent(component) {
  const vnode = component.render()
  vnode.children.forEach(child => renderComponent(child))
  patchDOM(vnode)
}
```

你调用 `setState`，它从根节点开始深度优先遍历整棵树，递归调用，一次性返回。调用栈像这样：

```
App.render → Header.render → Nav.render → NavItem.render → ...
→ ... 层层返回 → patch DOM
```

问题在于：**JavaScript 的调用栈是后进先出的，一旦进入递归，没有任何机制可以中断它**。如果你的组件树有 2000 个节点，一次 `setState` 可能要阻塞主线程 50~100ms。在 60fps 下，每一帧只有约 16ms 的处理时间。一旦 reconciliation 超了这个预算，卡顿就来了。

更要命的是，**所有更新的优先级是平等的**。用户在输入框中打字（需要即时反馈）和后台数据更新（可以延迟）触发的是同一套流程，前者会被后者阻塞。这个问题在 Stack Reconciler 下无解——你没有机制说"这个更新先执行，那个等一下"。

### 2.2 Fiber——自己管调用栈

React 团队的核心洞察是：**问题的根源不是虚拟 DOM，而是递归调用不可中断**。他们的方案也很大胆：如果浏览器不给我中断递归的能力，那我自己实现一个调用栈。

Fiber 的数据结构长这样（高度简化）：

```typescript
interface FiberNode {
  return: FiberNode | null   // 父节点
  child: FiberNode | null    // 第一个子节点
  sibling: FiberNode | null  // 下一个兄弟节点

  pendingProps: any
  memoizedProps: any
  memoizedState: any

  flags: Flags
  nextEffect: FiberNode | null
  alternate: FiberNode | null  // 双缓冲
}
```

你可以把 Fiber 树理解成一个**可遍历的链表**。React 用 `while` 循环替代了递归：

```typescript
function workLoop(deadline: IdleDeadline) {
  let shouldYield = false
  while (nextUnitOfWork !== null && !shouldYield) {
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork)
    shouldYield = deadline.timeRemaining() < 1
  }
  if (nextUnitOfWork !== null) {
    requestIdleCallback(workLoop)
  }
}
```

每次处理一个 Fiber 节点后，检查当前帧剩余时间。不够了，保存 `nextUnitOfWork` 指针，把控制权还给浏览器。下一帧回调中，从这个指针处继续。

关键点是：**暂停和恢复不需要保存额外的上下文**，因为所有的状态都在 Fiber 节点的属性中（`pendingProps`、`memoizedState` 等）。这和操作系统线程切换保存寄存器是一个逻辑，只不过 React 的"线程"在用户空间实现。

Andrew Clark 的原话是："Fiber 是一个专门为 React 组件设计的虚拟栈帧。"区别在于，这些栈帧存在堆里，开发者可以控制它们的执行顺序。

### 2.3 双缓冲：永远不让用户看到半成品

Fiber 同时维护了两棵树：

- **`current`**：当前屏幕上显示的内容对应的 Fiber 树
- **`workInProgress`**：正在计算中的"草稿"树

更新触发时，React 从 `current` 克隆出一棵 `workInProgress`。所有的 reconciliation 都在 `workInProgress` 上操作，用户看到的界面不受任何影响——即使渲染到一半被中断，或者发现这次更新不需要了，直接丢弃 `workInProgress` 即可。

`workInProgress` 完成后，React 进入 **Commit 阶段**：

1. 把副作用（DOM 增删改、生命周期、effect 调度）一次性应用到真实 DOM
2. 原子性地把 `current` 指针切换到 `workInProgress`

Commit 阶段必须是同步且不可中断的。原因很简单：你在修改用户看到的东西，任何不一致都会导致视觉问题。

这个两阶段模型是 Fiber 的基石：

```
Render Phase（可中断）：
  状态更新 → 构建 workInProgress → 收集 Effect List

Commit Phase（不可中断）：
  应用 DOM 变更 → 切换 current 指针 → 调度 useEffect
```

### 2.4 Lane：优先级怎么管

React 18 的 Lane 模型用 31 位二进制数表示优先级，每一位是一条"通道"：

```typescript
const InputContinuousLane =  0b0000000000000000000000000000010
const DefaultLane =          0b0000000000000000000000000000100
const TransitionLane1 =      0b0000000000000000000000000010000
```

用户输入、点击事件分配高 Lane，数据加载、过渡动画分配低 Lane。高 Lane 更新进来后，React 可以抢占当前正在进行的低 Lane 工作。

更巧妙的是 **Lane 纠缠**机制：如果高优先级更新依赖了低优先级更新的数据（比如一个过渡动画中的状态被用户输入读取），React 会自动合并到一次渲染中，避免视觉不一致。

这套系统的复杂度也很高。我见过不少 React 项目因为并发模式下的更新时序问题，出现"先来的数据被后来的覆盖"之类诡异的 bug。Lane 模型的强大是以运行时复杂度为代价的——这是 React 的取舍。

---

## 三、Vue 响应式系统——编译器才是真正的王牌

### 3.1 从 defineProperty 到 Proxy

Vue 2 的响应式系统通过 `Object.defineProperty` 实现：

```javascript
function defineReactive(obj, key, val) {
  const dep = new Dep()
  Object.defineProperty(obj, key, {
    get() {
      dep.depend()
      return val
    },
    set(newVal) {
      val = newVal
      dep.notify()
    }
  })
}
```

这个方案有三个硬伤：

1. **无法检测新增属性**——这就是为什么 Vue 2 需要 `Vue.set()` 和 `Vue.delete()`
2. **无法检测数组索引赋值和 length 修改**——这就是为什么 Vue 2 要 hack 数组的 `push`、`pop` 等方法
3. **必须深度递归遍历**——初始化时就把每一层的每个属性都加上 getter/setter，不管你用不用

Vue 3 切换到 Proxy 后，三个问题一次性解决：

```javascript
function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key)
      return Reflect.get(target, key, receiver)
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver)
      trigger(target, key)
      return result
    }
  })
}
```

Proxy 的代理是"懒"的——只有当你访问到某个嵌套对象时，才递归创建代理。这不仅解决了 Vue 2 的各种边界问题，还让 Vue 3 天然支持 Map、Set、WeakMap。

但 Proxy 也带来了一个小代价：**响应式对象的引用变了**。`reactive(obj) !== obj`，因为 Proxy 返回的是代理对象。在某些边缘场景下会有问题——比如你传了一个响应式对象给不接受 Proxy 的第三方库。

### 3.2 依赖追踪的三层结构

Vue 3 内部维护了一个三层映射结构：

```
targetMap (WeakMap)
  └─ key → depsMap (Map)
       └─ key → dep (Set<ReactiveEffect>)
```

- **`targetMap`**：WeakMap 关联响应式对象和它的依赖映射。对象被 GC 时，依赖信息自动释放，不会内存泄漏
- **`depsMap`**：Map，以属性名为 key，映射到依赖集合
- **`dep`**：Set，收集所有依赖某个属性的 `ReactiveEffect`

`ReactiveEffect` 是"副作用"的抽象。组件的渲染函数、`computed` 的计算函数、`watch` 的回调，本质上都是 `ReactiveEffect` 的实例：

```typescript
class ReactiveEffect {
  deps: Dep[] = []
  active: boolean = true

  run() {
    activeEffect = this
    return this.fn()  // 触发 getter → track()
  }
}
```

关键设计是 **双向记录**：访问属性时，`track()` 把当前 `activeEffect` 添加到 `dep` 集合；同时 effect 自己也记录它属于哪些 `dep`。这样在清理或 effect 失效时，可以精确地从依赖集合中把自己移除。

这种设计的核心优势在于 **更新复杂度是 O(m)**，其中 m 是实际变化影响的节点数，与组件树的总规模 n 无关。一个 1000 个组件的页面中，如果只有一个文本节点需要更新，Vue 只需要做一次 `textContent` 赋值。而在没有 Compiler 优化的 React 中，可能需要走整棵组件树的 reconciliation。

### 3.3 编译时优化的"三板斧"

Vue 3 的编译器做了一系列运行时难以复现的优化。这里说三个最关键的。

**第一板斧：静态提升**

```vue
<template>
  <div>
    <span>静态文字</span>
    <span>{{ dynamic }}</span>
  </div>
</template>
```

编译后：

```javascript
// 静态节点提到外部，只创建一次
const _hoisted_1 = createVNode('span', null, '静态文字')

function render(_ctx) {
  return createVNode('div', null, [
    _hoisted_1,
    createVNode('span', null, _ctx.dynamic)
  ])
}
```

静态节点只创建一次，后续更新完全跳过。这是最直观的优化——但能这么做的前提是编译器能静态判断哪些节点是纯静态的。

**第二板斧：Patch Flags**

```vue
<template>
  <div :class="active" :id="fixedId">
    {{ text }}
  </div>
</template>
```

编译后，每个动态节点会被打上一个优化标记：

```javascript
createVNode('div', { class: _ctx.active, id: 'fixedId' }, _ctx.text, 
  PatchFlags.CLASS | PatchFlags.TEXT
)
```

运行时看到 `PatchFlags.CLASS | PatchFlags.TEXT`，就知道只需要比较 `class` 和 `textContent`，不需要检查 `id`（因为它是静态的），也不需要检查 `style`、`onClick` 等属性。在大型列表更新时，这种精确跳过可以省掉大量的属性遍历开销。

**第三板斧：树扁平化**

传统的虚拟 DOM diff 是递归的——父节点 diff 完，递归 diff 子节点。Vue 3 的编译器会把所有动态节点收集到一个扁平数组中：

```javascript
const dynamicNodes = [node1, node2, node3, ...]

function patchChildren(prev, next) {
  for (let i = 0; i < dynamicNodes.length; i++) {
    patch(dynamicNodes[i].el, dynamicNodes[i].newVNode)
  }
}
```

diff 时直接遍历这个扁平数组，不需要递归，也不需要跳过静态节点。这个优化在组件树很深的时候效果尤其明显。

Vue 的虚拟 DOM 和 React 的虚拟 DOM 虽然名字一样，但实际差异很大——**Vue 的虚拟 DOM 是被编译器优化过的高度特化版本，它比 React 的通用 diff 更"偏科"，但在大多数场景下更快。**

### 3.4 Vapor Mode：扔掉虚拟 DOM

Vue 3.6 的 Vapor Mode 更进一步——在编译时直接生成 DOM 操作代码：

```javascript
// Vapor Mode 编译产物（示意）
function render(_ctx) {
  const div = document.createElement('div')
  const span = document.createElement('span')
  
  effect(() => {
    span.textContent = _ctx.text
  })
  
  div.appendChild(span)
  return div
}
```

没有虚拟 DOM，没有 createVNode，直接在 setup 中创建真实 DOM，通过 `effect` 把数据变化精确绑定到 DOM 操作。和 SolidJS 的思路非常接近了。

Vapor Mode 的意义不只在性能提升，而在于：**Vue 的响应式系统本身就不依赖虚拟 DOM**。虚拟 DOM 只是 Vue 用来兼容模板灵活性的一个实现细节，不是架构的必要组成部分。

---

## 四、架构光谱：其他框架的位置

把主流前端框架放一根轴上的话，大概这样：

```
编译时 ← — — — — — — — — — — — — — — → 运行时

Svelte    Solid    Vue Vapor    Vue 3    React    Angular(Zones)
  |          |          |          |        |           |
  编译掉    编译优化   编译为    编译优化+  通用运行时  运行时脏检查
  运行时    +运行时    DOM操作  运行时追踪  调度系统    （Signal迁移中）
            信号系统
```

**Svelte（最左端）**：编译器即框架。`$state` 声明状态，编译器分析模板引用，生成精确的 DOM 更新代码。运行时包只有 2~3KB gzip。代价是编译器必须处理所有边界情况，调试时看到的代码和写的代码差异很大。

**SolidJS（中左）**：保留 JSX，但编译器把它转为细粒度的 DOM 更新指令，运行时用 Signals（`createSignal`）做依赖追踪。组件函数只执行一次，后续更新通过信号直达对应 DOM 节点。在 js-framework-benchmark 上长期和 vanilla JS 竞争第一。

**Vue（中偏左）**：编译时优化 + 运行时 Proxy 追踪。Vapor Mode 会让它向左移动，但正式发布的 Vue 3 仍是编译优化 + 虚拟 DOM 的组合路线。

**React（中右）**：最纯粹的运行时调度方案。Fiber 的核心是在运行时管理更新优先级和中断恢复。React Compiler（原 React Forget）正在向编译时方向移动。

**Angular（最右端）**：Zone.js 在运行时 monkey-patch 所有异步 API，任何异步操作后触发全局脏检查。正在向 Signals 迁移，16+ 引入 `signal()`、`computed()`、`effect()`，逐步从全局检查走向细粒度追踪。

这个光谱说明一件事：**没有哪个位置是"正确"的，每个框架的位置反映的是它对编译时和运行时这两股力量的权衡。**

---

## 五、趋同：谁在向谁学习

过去两年，框架们在互相学习。

### React 在学编译时

React Compiler 做的事情，本质上就是 Vue 3 编译器一直在做的——在构建时分析组件依赖关系，自动插入 memoization。以前需要手动写的 `useMemo`、`useCallback`、`React.memo`，编译器自动帮你完成：

```javascript
// 编译前
function MyComponent({ items }) {
  const sorted = items.sort((a, b) => a.name.localeCompare(b.name))
  return <List items={sorted} />
}

// 编译后（React Compiler 自动插入 memoization）
function MyComponent($) {
  const sorted = $.memo(items => 
    items.sort((a, b) => a.name.localeCompare(b.name))
  )($[0])
  return <List items={sorted} />
}
```

加上 React Server Components，代表了 React 向"减少运行时工作"方向的双重努力。但和 Vue 不同，React 的 JSX 天然比 Vue 模板更难做静态分析——JSX 就是 JavaScript，你可以写任意逻辑，编译器很难确定哪些是"静态"的。

### Vue 在学细粒度

Vapor Mode 是对 Svelte/Solid 路线的回应。Vue 的响应式系统（Proxy + effect 追踪）本身就不依赖虚拟 DOM，Vapor Mode 只是把这个事实兑现了。Vue 的路线图和 SolidJS 越来越像——但有一个关键区别：Vue 保留了可选的虚拟 DOM 路径，Vapor Mode 只是一个编译选项，不是替代方案。

### 为什么趋同

我个人觉得，趋同是因为行业对"运行时万能"的信念在减弱。虚拟 DOM 很棒，但它不是免费的。当终端设备从旗舰手机到 IoT 设备越来越多样，运行时体积和执行效率依然是硬约束。编译时优化虽然"硬编码"、不灵活，但它可以给出"最差情况也有保障"的性能。

反过来，运行时调度能力依然不可替代。Vue 3 的异步队列、`nextTick`、`suspense`，本质上也是在运行时做调度。我理想中的框架应该是 React Compiler 和 Vue Vapor 的组合：**编译时做能做的优化，运行时处理那些不能预测的部分。**

---

## 六、对日常开发的实际影响

说这些底层差异在写代码时会带来什么。

### 更新心智模型

看两个效果相似的代码：

```javascript
// React
const [count, setCount] = useState(0)
useEffect(() => {
  document.title = `Count: ${count}`
}, [count])

// Vue
const count = ref(0)
watchEffect(() => {
  document.title = `Count: ${count.value}`
})
```

表面一样，底层差很多：

- React 的 `useEffect` 在提交阶段之后异步调用——它看到的是"快照"值，依赖数组告诉 React "当 count 变化时重新执行"
- Vue 的 `watchEffect` 同步执行——它在执行中自动追踪 `count.value` 的读取，建立依赖，无需声明数组

这就是"显式声明"和"自动追踪"的差异。Vue 写起来更少，但一旦脱离响应式上下文（比如在 `setTimeout` 回调里读 `ref.value`），track 跟不上，就会出现"值变了但视图没更新"的问题。React 的方式更啰嗦，但行为更可预测——你明确知道自己声明了什么。

### 性能调优思路

- React 性能瓶颈通常出在**不必要的重渲染**。优化方向是减少渲染范围（`React.memo`、`useMemo`、`useCallback`），或者在架构层面做组件拆分
- Vue 性能瓶颈通常出在**过大的响应式对象**。优化方向是把大的 `reactive` 拆成小的 `ref`，或用 `shallowReactive` / `shallowRef` 避免深层追踪

没有优劣——但它让你排查性能问题时往完全不同的方向看。

### Debug 体验

React 的 Fiber 调度有个现实问题：`console.log` 在渲染函数中可能输出多次，因为 React 可能多次构建 workInProgress 树后才提交。你不太确定当前看到的是"草稿"阶段的日志还是正式阶段的日志。第一次遇到这个现象时，我困惑了好一会儿。

Vue 的更新路径更直接：数据变化 → track → trigger → effect。大多数情况下，`console.log` 输出次数和 DOM 更新次数是一一对应的。

---

## 七、总结

React Fiber 和 Vue 响应式系统的差异，归根结底是 UI 抽象模型的分歧：

- React 选择了一条靠近操作系统底层的路——重新实现调用栈、用户空间调度器、优先级抢占——以运行时复杂度换取对不可预测更新的掌控力
- Vue 选择了一条靠近应用层面的路——让编译器做尽可能多的静态分析，让数据自己追踪自己的依赖——以编译时约束换取运行时效率

这两个选择没有高下之分。Fiber 证明了在 JavaScript 单线程中实现复杂调度的可行性，Vue 的编译器证明了静态分析在现代 UI 框架中的巨大价值。Svelte 证明了"没有运行时"的可行性，SolidJS 证明了细粒度响应式能跑多快。

这些探索的价值不在谁取代谁，而在**它们共同扩展了前端工程的知识边界**。每一次跨框架的相互启发——Fiber 启发 Vue 的异步队列重构，Vue 的编译器启发 React Compiler 的方向——都在让整个前端技术栈变得更好。

作为开发者，搞清楚这些底层的取舍，比站队吵"哪个框架更好"有用得多。因为最终你选的不是框架，是你对"UI 应该怎么组织"这个问题的回答。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)