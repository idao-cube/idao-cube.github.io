> 你有没有想过一个问题：为什么 Canvas 里的文字永远那么丑？为什么游戏里的 UI 只能用 Canvas API 手画，而不能直接写个 `<div>` 上去？为什么每次做图表都要在 `ctx.fillText` 和 CSS 字体之间反复拉扯？今天，一个正在 Chromium 中孵化的 WICG 提案，要彻底终结这个延续了二十年的困局。

## 📋 目录

- [📋 目录](#-目录)
- [背景：Canvas 二十年的「盲人」困境](#背景canvas-二十年的盲人困境)
- [前传：为什么 Canvas 一直渲染不了 HTML](#前传为什么-canvas-一直渲染不了-html)
- [核心原语一：layoutsubtree——一纸「委任状」](#核心原语一layoutsubtree一纸委任状)
- [核心原语二：drawElementImage——画布上的「复印机」](#核心原语二drawelementimage画布上的复印机)
- [核心原语三：paint 事件——智能触发器](#核心原语三paint-事件智能触发器)
- [Bonus：captureElementImage——通往 Worker 的传送门](#bonuscaptureelementimage通往-worker-的传送门)
- [深水区：事件循环中的时序博弈](#深水区事件循环中的时序博弈)
  - [Option A：在 ResizeObserver 时机触发（带循环）](#option-a在-resizeobserver-时机触发带循环)
  - [Option B：紧接在 Paint 步骤之后触发（带循环）](#option-b紧接在-paint-步骤之后触发带循环)
  - [Option C：紧接在 Paint 步骤之后触发（**不**循环）—— **被选中的方案**](#option-c紧接在-paint-步骤之后触发不循环-被选中的方案)
- [同步公式：CSS Transform 背后的线性代数](#同步公式css-transform-背后的线性代数)
- [隐私保护：看不见的边界](#隐私保护看不见的边界)
- [生态地图：谁已经上车了](#生态地图谁已经上车了)
  - [three.js — 原生 WebGL 纹理集成](#threejs--原生-webgl-纹理集成)
  - [PlayCanvas — 3D 产品配置器 + HtmlSync](#playcanvas--3d-产品配置器--htmlsync)
  - [VFX-JS — 视觉特效框架](#vfx-js--视觉特效框架)
  - [three-html-render — 纯 JS Polyfill](#three-html-render--纯-js-polyfill)
- [未解之谜与未来方向](#未解之谜与未来方向)
  - [Open Issues 选读](#open-issues-选读)
  - [未来：自动更新 Canvas](#未来自动更新-canvas)
  - [标准化进程](#标准化进程)
- [总结](#总结)

## 背景：Canvas 二十年的「盲人」困境

2004 年，Apple 在 Safari 中引入了 `<canvas>` 元素，随后被 WHATWG 和 W3C 标准化。二十多年来，Canvas 成为 Web 上最强大的 2D/3D 图形基元——游戏、图表、数据可视化、创意工具、图像编辑……几乎一切"像素级别的操作"都跑在 Canvas 上。

但它有一个致命的短板：**渲染不了真正的 HTML 内容**。

这听起来像是一个不应该存在的问题——我明明有一个 `<div>`，为什么不能把它「画」到 Canvas 上？但现实是，开发者们二十年来的解决方案只有这些：

| 方案 | 原理 | 问题 |
|------|------|------|
| `ctx.fillText()` | 手写文字排版 | 不支持复杂文本、RTL、国际化排版 |
| `html2canvas` / dom-to-image | 用 JS 重绘整个渲染树 | 慢（JS 模拟渲染引擎）、不完整、不全准 |
| SVG `<foreignObject>` | 在 SVG 中嵌入 HTML | 无法与 Canvas 2D API 交互、不支持 WebGL/WebGPU |
| 截图上传（你没看错） | 手动截图当纹理 | 根本不能算方案 |

这些问题带来的连锁反应非常具体：

- **可访问性灾难**：Canvas fallback 内容和实际画出来的像素，本质上没有约束关系。开发者写了一个披着 `aria-label` 外衣的 `<canvas>`，但里面到底画了什么，屏幕阅读器和实际视觉内容完全是两套。
- **国际化短板**：`ctx.fillText` 不处理 RTL（阿拉伯语/希伯来语）、竖排文字、复杂脚本连字。如果你的图表需要显示阿拉伯语标签，要么自己实现排版引擎，要么放弃。
- **游戏 UI 的割裂**：3D 场景中的 2D 界面（菜单、对话气泡、HUD），要么用 3D 引擎的内置 UI 系统（学习成本高），要么用 Canvas 手绘（质量差），要么用 DOM 覆盖层（无法与 3D 场景融合）。

HTML-in-Canvas 提案的目标就是：**让开发者能把真正的 DOM 元素渲染到 Canvas 上，用浏览器的原生排版引擎干活**。

## 前传：为什么 Canvas 一直渲染不了 HTML

在深入 API 之前，先搞明白一个核心问题：**为什么这件事以前做不到？**

浏览器的渲染流水线大致是这样的：

```
JS → Style → Layout → Paint → Composite
```

- **Style**：计算 CSS 规则
- **Layout**：计算盒模型位置
- **Paint**：生成绘制指令列表（display list）
- **Composite**：合成图层

Canvas 的渲染是**脱离这个流水线的**。Canvas 的内容通过 JS 调用 Canvas API（`fillRect`、`drawImage` 等）写入一个位图缓冲区，然后直接作为一个纹理交给 GPU。浏览器的渲染引擎（Paint/Composite）对 Canvas 内部发生了什么一无所知。

而 HTML 元素的渲染走的是完整的 Style → Layout → Paint → Composite 管道。

所以，要把 HTML 渲染到 Canvas 上，本质上是**要让浏览器的渲染管道和 Canvas 的像素缓冲区之间建立一座桥**——而且这座桥不能破坏安全模型，不能引入性能问题，还要支持可访问性。

这比听起来难得多。直到 WICG/html-in-canvas 提出了一套优雅的解决方案。

## 核心原语一：layoutsubtree——一纸「委任状」

提案的第一个原语是一个 HTML 属性——`layoutsubtree`。

```html
<canvas id="myCanvas" layoutsubtree>
  <div id="myContent">
    <h2>Hello Canvas!</h2>
    <p>我可以在 Canvas 里用 HTML 渲染了！</p>
  </div>
</canvas>
```

加上这个属性的瞬间，发生了三件关键的事情：

1. **Canvas 的子元素**获得了 stacking context，成为了其后代元素的 containing block
2. **Canvas 的子元素**拥有了 paint containment（绘制包含）
3. **Canvas 的子元素**参与了正常布局和 hit testing

翻译成人话：**Canvas 的孩子虽然还在 DOM 树里，但它们的视觉渲染被「截胡」了——浏览器的 Paint 阶段不会再把这些孩子渲染到屏幕上，而是把它们的绘制结果存起来，等着开发者用 API 取走。** 同时，它们仍然参与布局、可访问性树和事件命中测试。

这个设计有一个非常精妙的双重角色：**同一个元素既是视觉内容（被绘制到 Canvas 中），也是可访问性内容（作为 Canvas fallback）**。不像现在的 `<canvas>` fallback——画出来的东西和 fallback 内容是两套，永远有不同步的风险。在 HTML-in-Canvas 中，它们就是同一个东西。

> `layoutsubtree` 就像是一纸「委任状」：告诉浏览器「这些孩子交给我来画，但请帮我把它们的布局和绘制结果准备好。」

## 核心原语二：drawElementImage——画布上的「复印机」

有了 `layoutsubtree` 把布局和可访问性安排好，下一步就是把子元素「印」到 Canvas 上。这就是 `drawElementImage()`：

```javascript
const ctx = canvas.getContext('2d');

canvas.onpaint = () => {
  ctx.reset();
  // 把 form_element 画到 Canvas 的 (100, 0) 位置
  const transform = ctx.drawElementImage(form_element, 100, 0);
  // 同步 DOM 位置
  form_element.style.transform = transform.toString();
};
```

**核心行为**：

- **只接受 Canvas 的直接子元素**（这就是 `layoutsubtree` 标记的那些孩子）
- 调用时，返回一个 `DOMMatrix`（CSS transform 矩阵），你需要把这个矩阵应用到元素的 `style.transform` 上，让 DOM 位置和画上去的位置保持一致
- Canvas 的当前变换矩阵（CTM，Current Transformation Matrix）会作用于绘制——也就是说，你可以在画布上 `ctx.rotate(45)` 然后 `drawElementImage`，元素就会旋转
- 子元素的 CSS transform **被忽略**（原因见下文——如果不忽略会导致双重变换）
- 溢出内容被裁切到元素的 border box

**destination rect 参数**（和 `drawImage` 一模一样）：

```javascript
// 最简形式：在 (x, y) 处以原始尺寸绘制
ctx.drawElementImage(element, x, y);

// 指定目标尺寸
ctx.drawElementImage(element, x, y, width, height);

// 带 source rect 裁剪
ctx.drawElementImage(element, sx, sy, sw, sh, dx, dy, dw, dh);
```

**WebGL 版本的接口**是 `texElementImage2D`，把元素渲染到纹理：

```javascript
// 当你需要把 HTML 内容作为 3D 纹理时
gl.texElementImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, myElement);
```

**WebGPU 版本的接口**是 `copyElementImageToTexture`：

```javascript
queue.copyElementImageToTexture(myElement, destination);
```

一个 API，覆盖 2D Canvas、WebGL、WebGPU 三大图形上下文。

> 形象一点理解：`drawElementImage` 就像是把浏览器的渲染引擎当作一台复印机，你传一个 DOM 元素进去，它返回一页「复印件」——而且附带一个坐标映射表（DOMMatrix），告诉你怎么把这页「复印件」在画布上的位置同步给 DOM。

## 核心原语三：paint 事件——智能触发器

`drawElementImage` 画的是**快照**。问题来了：当元素的内容发生变化时（比如输入框里有文字输入），开发者怎么知道需要重新绘制？

这就是 `paint` 事件的用武之地：

```javascript
canvas.addEventListener('paint', (event) => {
  // event.changedElements 包含渲染发生了变化的子元素
  ctx.reset();
  for (const el of event.changedElements) {
    const t = ctx.drawElementImage(el, 0, 0);
    el.style.transform = t.toString();
  }
});
```

**关键特性**：

1. **智能触发**：只有当子元素的**视觉渲染**真正发生变化时才会触发，而不是 60fps 无脑循环。省电、省 CPU。
2. **时机**：在浏览器每一帧渲染管线的 `update-the-rendering` 阶段中，紧跟在 intersection observer 步骤之后、Paint 步骤之前触发。

```
... → IntersectionObserver → paint event → Paint → Composite → ...
```

3. **CSS transform 变化不触发**：因为 transform 影响的是位置而非渲染内容，改变 transform 不会重新生成 paint 指令，所以不触发 `paint` 事件。
4. **paint 内的 DOM 改动推迟到下一帧**：你在 `paint` 回调里改了元素的 class/文本，这一帧不会生效，下一帧才会。
5. **requestPaint()**：如果你需要每帧都重绘（类似游戏循环），可以调用 `canvas.requestPaint()` 强制触发 paint 事件，行为和 `requestAnimationFrame` 类似。

## Bonus：captureElementImage——通往 Worker 的传送门

有一个问题：上面的所有 API 都依赖 DOM 元素引用，但 **Web Worker 中无法访问 DOM**。

解决方案是 `captureElementImage()`：

```javascript
// 主线程：捕获快照并传送到 Worker
canvas.onpaint = () => {
  const elementImage = canvas.captureElementImage(form_element);
  worker.postMessage({ elementImage }, [elementImage]);  // Transferable！
};

// Worker：直接绘制
self.onmessage = (e) => {
  if (e.data.elementImage) {
    ctx.drawElementImage(e.data.elementImage, 100, 0);
  }
};
```

`ElementImage` 是一个 **Transferable** 对象，和 `ImageBitmap`、`ArrayBuffer` 一样，支持零拷贝传输。这为 **OffscreenCanvas 在 Worker 中高性能渲染 HTML 内容**铺平了道路。

整个对象只有三个方法/属性：
- `width` / `height`：快照的尺寸
- `close()`：释放资源

轻量、简洁、高效。

## 深水区：事件循环中的时序博弈

如果说前面的 API 是"皮毛"，那下面这部分才是 HTML-in-Canvas 最深的设计决策——**`paint` 事件到底应该在哪一刻触发？**

规范文档中记录了三种方案，我们逐一分析：

### Option A：在 ResizeObserver 时机触发（带循环）

位置：在 `update-the-rendering` 流程的第 16.2.6 步（Deliver resize observations），如果 paint 事件中又修改了样式，就循环回到第 16.2.1 步（Recalculate styles and update layout）。

**问题**：
- 需要在这个时间点**同步执行 Paint 步骤**来生成子元素的绘制快照。Paint 本身很消耗性能，还要可能跑多次。
- Gecko（Firefox 的渲染引擎）的架构导致了这里实现困难——某些引擎在这个时间点根本拿不到完整的绘制结果。
- 最致命的问题：**WebGL**。WebGL 的 `gl.getError()`、`gl.getParameter()` 等 API 需要触发 GPU 命令缓冲区刷新（flush），如果在 Paint 完成之前调用，会导致死锁或不一致的渲染状态。

### Option B：紧接在 Paint 步骤之后触发（带循环）

位置：在浏览器的 Paint 步骤完成后立即触发。

**优势**：不需要上面那种"同步 Paint Canvas 子元素"的操作——因为 Paint 已经跑完了，每个元素的绘制结果是可用的。

**问题**：仍然需要循环。如果 paint event 中改动了 DOM，又得回退到 style recalc → layout → paint 的完整循环，可能在一次帧中跑多次，十分昂贵。

### Option C：紧接在 Paint 步骤之后触发（**不**循环）—— **被选中的方案**

**核心思想**：paint event 在一帧中**只跑一次**。

如果开发者在 paint event 中修改了 DOM？改了就改了，但**这一帧已经锁死了**——DOM 修改的效果留到**下一帧**的渲染管线去处理。

这带来一个非常有趣的对称性：浏览器的 Paint 步骤也是不可循环的——你无法在一个帧内让浏览器画两次。paint event 的行为和浏览器原生的 Paint 步骤完全对齐。

```
方案  |  循环  |  是否需要同步 Paint  |  兼容 WebGL  |  复杂度
A     |  是    |  是                  |  否（死锁）  |  高
B     |  是    |  否                  |  是          |  高
C     |  否    |  否                  |  是          |  低 ✅
```

这个决策过程是 HTML-in-Canvas 提案中最精妙的设计之一。它不强求"开发者改了我就立刻刷新"，而是承认**一帧内做到绝对实时是不现实的**，通过"延迟到下一帧"来换取架构的简洁性和跨浏览器兼容性。

> 就像 React 的虚拟 DOM 不追求"每次修改立刻更新真实 DOM"一样，HTML-in-Canvas 也不追求"每个 CSS 变化都立刻刷新 Canvas 绘制"。延迟带来一致性。

## 同步公式：CSS Transform 背后的线性代数

前面提到，`drawElementImage()` 返回一个 `DOMMatrix`，需要设置到元素的 `style.transform` 上。为什么要这么做？

因为**浏览器的 hit testing（点击命中测试）、intersection observer、可访问性功能都依赖元素的 DOM 位置**。如果你把一个 `<div>` 画到了 Canvas 的 (100, 200) 位置，但它在 DOM 树中还在原始位置，点击 (100, 200) 就命中不了这个元素。

解决方案是：**把 DOM 元素通过 CSS transform 移动到与绘制位置匹配。**

`drawElementImage()` 返回的 `DOMMatrix` 就是按如下公式计算的：

```
T_sync = T_origin⁻¹ · S_css→grid⁻¹ · T_draw · S_css→grid · T_origin
```

其中：
- **`T_draw`**：绘制到 Canvas 上的变换矩阵，等于 `CTM · T(x, y) · S(destScale)`（CTM + 位置偏移 + 缩放）
- **`T_origin`**：元素的 `transform-origin` 矩阵
- **`S_css→grid`**：CSS 像素到 Canvas 网格像素的缩放矩阵

直观理解：这个公式做的事情就是**把"在 Canvas 网格坐标系中的绘制位置"反向映射回"DOM 中的 CSS 像素位置"**。

对于 WebGL/WebGPU 中的 3D 场景，还有一个辅助方法 `canvas.getElementTransform(element, drawTransform)`，让你传入任意变换矩阵并计算出对应的 CSS transform。

```javascript
// 2D Canvas 直接返回
const transform = ctx.drawElementImage(element, x, y);

// WebGL/WebGPU 需要手动计算
const drawTransform = new DOMMatrix([...]); // 你自定义的 3D 变换
const cssTransform = canvas.getElementTransform(element, drawTransform);
element.style.transform = cssTransform.toString();
```

**重要提醒**：CSS transform 的变化不会触发 `paint` 事件——因为 transform 只影响位置，不影响绘制内容，所以 `paint` event 不会因为你在同步 transform 而反复触发。这避免了死循环。

## 隐私保护：看不见的边界

`drawElementImage()` 能让 Canvas 读取 DOM 元素的像素，这就带来了一个安全问题：**如果 Canvas 能读取任何元素的内容，那跨域保护怎么办？**

提案的隐私模型遵循一个核心理念：**`drawElementImage` 不会暴露任何 JavaScript 当前不可访问的信息。** 换句话说，它不会打开新的攻击面。

被排除在绘制之外的敏感内容：

| 排除项 | 原因 |
|--------|------|
| 跨域 iframe、跨域图片 | 同 Canvas `drawImage` 的跨域保护一致 |
| CSS `url()` 引用的跨域资源（如 `background-image`） | 同上 |
| 系统颜色/主题/偏好 | 否则可通过像素读取猜出系统主题 |
| 拼写/语法检查标记 | 可能暴露用户的拼写习惯 |
| 已访问链接的颜色 | 经典的隐私泄露向量 |
| 自动填充（autofill）预览内容 | 包含敏感个人信息 |
| 次像素抗锯齿 | 可用作浏览器指纹 |

不被视为敏感（允许绘制）的内容：

| 保留项 | 理由 |
|--------|------|
| 页面查找（Find in Page）高亮 | 低安全性影响 |
| 滚动条和表单控件外观 | 已可通过 SVG `foreignObject` 检测 |
| 光标闪烁频率 | 低熵信息 |
| `forced-colors` 模式 | 已可通过 CSS media query 获取 |

注意：这是**预防性设计**——在提案还处于 WICG 孵化阶段就考虑了完整的安全模型。这与 W3C TAG 审查（issue #1204）和 WHATWG 标准化讨论中的安全关注点保持一致。

## 生态地图：谁已经上车了

虽然提案还在孵化中，浏览器端只有 Chrome Canary 和 Brave Stable (Chromium 147+) 通过 flag 支持，但开源社区已经在积极适配：

### three.js — 原生 WebGL 纹理集成

mrdoob/three.js 已经在 WebGL 和 WebGPU 两个渲染后端中集成了 HTML-in-Canvas：

```javascript
// three.js 内部实现（简化）
if ('texElementImage2D' in gl) {
  const canvas = gl.canvas;
  if (!canvas.hasAttribute('layoutsubtree')) {
    canvas.setAttribute('layoutsubtree', 'true');
  }
  // HTML 元素直接作为纹理源
  gl.texElementImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, htmlElement);
}
```

这意味你可以把任意的 HTML 内容作为 three.js 的纹理，直接贴到 3D 模型上。

相关 PR: [mrdoob/three.js#31233](https://github.com/mrdoob/three.js/pull/31233)

### PlayCanvas — 3D 产品配置器 + HtmlSync

PlayCanvas 引擎甚至在官方示例中完整实现了基于 HTML-in-Canvas 的交互式 3D 产品配置器。一个 HTML 面板被渲染为 WebGL 纹理，用户点击 3D 场景中的 HTML 按钮时的 hit testing 完全由浏览器的原生 DOM 事件处理——通过 `getElementTransform` 同步位置。

关键助手类 `HtmlSync` 被设计为可复用的工具类，处理 canvas ↔ 3D 平面的坐标映射。

### VFX-JS — 视觉特效框架

[fand/vfx-js](https://github.com/fand/vfx-js) 提供了一个优雅的 `addHTML()` 方法：

```javascript
const vfx = new VFX();
await vfx.addHTML(element, { shader: 'liquidGlass' });
```

它内部先检查 `supportsHtmlInCanvas()`，如果可用就使用原生 API，否则优雅降级到传统的 `dom-to-canvas` 方案——渐进增强的最佳实践。

### three-html-render — 纯 JS Polyfill

最令人兴奋的生态项目之一是 [repalash/three-html-render](https://github.com/repalash/three-html-render)——一个在浏览器不支持原生 API 时的 Polyfill。它通过 CSS `matrix3d()` 变换和 `iframe` / `embed` 技术模拟了 `drawElementImage` 的核心行为。

即使你的用户没有启用 `chrome://flags/#canvas-draw-element`，这个 Polyfill 也能工作。这是一个很聪明的策略——用 Polyfill 降低采用门槛，让框架生产环境可用。

## 未解之谜与未来方向

提案仍处于活跃讨论中（仓库中有 16 个 open issues），以下几个话题值得关注：

### Open Issues 选读

| Issue | 核心问题 |
|-------|---------|
| [#94](https://github.com/WICG/html-in-canvas/issues/94) — Hit testing and layer ordering | draw 多个元素时，z-index 如何与 hit testing 协调？ |
| [#85](https://github.com/WICG/html-in-canvas/issues/85) — `removedElements` | 当子元素被删除，paint 事件是否需要提供单独的 `removedElements` 列表？ |
| [#82](https://github.com/WICG/html-in-canvas/issues/82) — 新的指纹向量 | `onpaint` 事件即使不读取像素，也能通过监听事件频率来获取指纹信息（如光标闪烁频率） |
| [#31](https://github.com/WICG/html-in-canvas/issues/31) — 动图/视频支持 | GIF、WebP 动画、视频元素如何支持？ |
| [#47](https://github.com/WICG/html-in-canvas/issues/47) — `mix-blend-mode` 与 `backdrop-filter` | 效果在 Canvas 中未正确反映 |

### 未来：自动更新 Canvas

规范文档中提到了一个令人兴奋的未来方向——**auto-updating canvas**。

目前的模型是：你在 `paint` 事件中调用 `drawElementImage`，浏览器绘制快照。但如果支持了「自动更新模式」，`drawElementImage` 会在 Canvas 的命令缓冲区中记录一个"占位符"，浏览器可以在滚动或动画更新时**自动重新执行绘制，无需阻塞 JS 主线程**。

这意味着 Canvas 中的 HTML 内容可以和原生滚动完美同步，不再受 JS 事件循环的延迟影响。这个模式对 2D Canvas 已可行，对 WebGPU 也只需少量 API 扩展。

### 标准化进程

提案正处于标准化流程的以下位置：

```
WICG 孵化（当前）→ WHATWG Stage 2 → WHATWG Standard → 浏览器默认启用
```

- WHATWG Spec PR: [#11588](https://github.com/whatwg/html/pull/11588)
- W3C TAG 早期审查: [#1204](https://github.com/w3ctag/design-reviews/issues/1204)（2026年3月启动）
- 跨浏览器共识：Chromium / Gecko / WebKit 已在设计上达成一致（`paint` 事件 Option C 时序）

## 总结

HTML-in-Canvas 不只是 Canvas 的一个新功能——它是 Web 图形平台二十年来最重要的一次基础能力补全。

它的核心贡献不是加了几个 API，而是**在浏览器的渲染流水线和 Canvas 的像素缓冲区之间，架起了一座精心设计的桥梁**：

- `layoutsubtree` 用属性声明边界
- `drawElementImage` 用返回值解决同步
- `paint` 用精妙的时序设计避免死循环和性能灾难
- `captureElementImage` 用 Transferable 搞定 Worker 并行

Three primitives + one helper，四个接口把"把 DOM 渲染到 Canvas"从不可能变成了可能——而且是在不破坏现有安全模型、不影响性能、保持可访问性的前提下。

**三个核心判断：**

1. **技术设计质量很高**：从事件时序的选择（Option C）到隐私模型的预防性设计，到 `drawElementImage` 的返回值用作 `style.transform`，每个决策都有清晰的权衡分析。这不是一个"先上线再说"的功能。

2. **生态已经开始拥抱**：three.js、PlayCanvas、VFX-JS 等知名图形项目的积极适配远超预期。尤其在 3D 游戏和可视化领域，需求非常强烈。

3. **还有一段路要走**：目前只在 Chromium flag 后可用，Firefox 和 Safari 还没有明确的实现计划。标准化进程仍在 WICG 阶段。

如果你是图形/可视化方向的开发者，建议立刻打开 Chrome Canary，启用 `chrome://flags/#canvas-draw-element`，跑一下[官方 Demo](https://wicg.github.io/html-in-canvas/Examples/complex-text.html)。虽然它还不是正式标准，但方向已经明确——而且这个方向，可能改变前端图形生态的底层逻辑。

---

**参考链接**

- [WICG/html-in-canvas GitHub (2.9k stars)](https://github.com/WICG/html-in-canvas) — 验证: ✅
- [WHATWG Spec PR #11588](https://github.com/whatwg/html/pull/11588) — 验证: ✅
- [W3C TAG Review #1204](https://github.com/w3ctag/design-reviews/issues/1204) — 验证: ✅
- [html-in-canvas.dev — 第三方文档站](https://html-in-canvas.dev/) — 验证: ✅
- [three.js HTML Texture PR #31233](https://github.com/mrdoob/three.js/pull/31233) — 验证: ✅
- [PlayCanvas HTML Texture 示例](https://github.com/playcanvas/engine/blob/main/examples/src/examples/misc/html-texture.example.mjs) — 验证: ✅
- [VFX-JS 框架 (fand/vfx-js)](https://github.com/fand/vfx-js) — 验证: ✅
- [three-html-render Polyfill](https://github.com/repalash/three-html-render) — 验证: ✅
- [SnapDOM — html-in-canvas 插件](https://github.com/zumerlab/snapdom) — 验证: ✅
- [Spec: update-the-rendering](https://html.spec.whatwg.org/#update-the-rendering) — 验证: ✅

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)