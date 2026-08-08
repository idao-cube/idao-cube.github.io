> 项目地址：[github.com/playcanvas/supersplat](https://github.com/playcanvas/supersplat) | 7.6K+ Stars | MIT 协议 | 144 Releases (v2.25.1) | 620+ Commits


## 一、SuperSplat 是什么？

2023 年夏天，3D Gaussian Splatting（3DGS）论文在 SIGGRAPH 上炸场之后，整个三维重建领域都变了。它不需要像 NeRF 那样逐射线查询，也不需要像传统网格那样处理繁重的拓扑，而是用数万个微小的椭球体（Gaussian）直接编码场景的颜色、位置和透明度，渲染速度达到实时 30fps+，质量直逼离线渲染。

然后问题来了：大家都用 COLMAP + 3DGS 训练脚本生成 `.ply` 文件，但没有任何工具可以**编辑**这些高斯点云——你不能删掉一片树叶，不能挪动一个物体，不能给特定区域改颜色。SuperSplat 就是填补这个空白的东西。

它是 PlayCanvas 团队出品的一款**运行在浏览器中的 3D 高斯泼溅编辑器**，完全免费、开源（MIT）。你拖一个 `.ply` 或 `.splat` 文件进去，就能像 Photoshop 编辑图片一样编辑三维高斯点云——选中、删除、变换、裁剪、着色、测量，全套操作。

这件事听起来简单，但做到"在浏览器里实时编辑上百万个高斯椭球体"，背后其实塞了整整一套**工业级 3D 渲染管线**。

## 二、架构全景：事件驱动 + 模块拆分的单页应用

SuperSplat 选择了一条"老派但极其可靠"的路线。它没有上 React/Vue/Svelte，没有复杂的包管理，就是纯 TypeScript + 自己造的事件总线。

打开 `src/main.ts`，整个应用的启动流程异常清晰：

```
Events（中央事件总线）
  → EditHistory（异步操作队列，保证 undo/redo 串行化）
  → localizeInit（i18next 多语言初始化）
  → EditorUI（PCUI 界面框架渲染）
  → createGraphicsDevice（PlayCanvas WebGL2 设备）
  → Scene（核心 3D 场景管理）
  → ToolManager（11 个工具注册）
  → 各模块事件注册（timeline/camera-poses/track-manager/...）
  → scene.start()（异步模型加载）
```

这个架构最值得关注的有两点。

**第一是事件总线驱动的模块解耦。** 每个子系统只需要知道自己关心哪些事件，不需要知道谁在监听。比如颜色系统：

```typescript
events.on('setBgClr', (clr: Color) => { setBgClr(clr); });
events.on('bgClr', (clr: Color) => {
    document.body.style.backgroundColor = `rgba(...)`;
});
events.on('selectedClr', () => { scene.forceRender = true; });
```

背景色修改会触发 DOM 更新，选中色/未选中色/锁定色的变化触发场景重渲染。各模块通过 events 通信，谁都不直接依赖谁。

**第二是用 `editQueue` 实现的异步操作串行化。** 3D 编辑中最怕的就是多个异步操作竞争状态——你在旋转时撤回了删除，两个操作同时在 GPU 上跑。SuperSplat 的做法是把所有编辑操作塞进 `EditHistory` 的一条异步队列里，一个操作执行完才进入下一个，同时也保证了 undo/redo 的确定性。

```typescript
events.function('edit.queue', (fn: () => Promise<void>) => editHistory.queue(fn));
```

任何模块只要 `events.invoke('edit.queue', [myEditOp])` 就能安全地把操作加入队列。

## 三、渲染管线：PlayCanvas + 12 种自定义着色器

编辑器的图形后端是 PlayCanvas 引擎——一个老牌的 WebGL2 游戏引擎，但也已经开始支持 WebGPU。PlayCanvas 本身提供场景管理、相机、光照和材质系统，但高斯泼溅需要完全不同的渲染方式。

标准 3D 网格渲染走的是三角形光栅化管线，而 3DGS 走的是**逐高斯椭球的 α-blending 渲染**——每个 Gaussian 投影到屏幕上是一个 2D 椭圆，按深度排序后从后往前混合。SuperSplat 用 12 个自定义着色器实现了这条管线。

| 着色器                                               | 功能                           | 特点                                    |
| ---------------------------------------------------- | ------------------------------ | --------------------------------------- |
| `splat-shader.ts`                                  | 主渲染着色器，高斯椭球前向渲染 | 8.2KB，包含完整的 GS 投影和 α-blending |
| `splat-overlay-shader.ts`                          | 选中高亮覆盖层                 | 6.3KB，单独渲染选中状态                 |
| `bound-shader.ts`                                  | 边界框可视化                   | 用于调试和数据检查                      |
| `intersection-shader.ts`                           | 射线与高斯相交检测             | 实现鼠标拾取的核心                      |
| `position-shader.ts`                               | 高斯中心点渲染                 | 用于点云模式显示                        |
| `infinite-grid-shader.ts`                          | 无限网格地面                   | 5.3KB，给编辑场景提供空间参考           |
| `box-shape-shader.ts` / `sphere-shape-shader.ts` | 盒状/球状选择区域可视化        | 3D 选择工具的后端                       |
| `outline-shader.ts`                                | 选中物体的轮廓描边             | 让选中的高斯明显可见                    |
| `debug-shader.ts`                                  | 调试信息渲染                   | 可视化法线、深度等                      |
| `blit-shader.ts`                                   | 帧缓冲复制                     | 截图导出时用                            |
| `debug-shader.ts`                                  | 调试信息渲染                   | 可视化法线、深度等                      |

最有意思的是 `splat-shader.ts`——这是实际渲染数百万高斯的核心。它处理的核心逻辑包括：

1. **3D 到 2D 投影**：将 3D 协方差矩阵投影到相机平面，转成 2D 椭圆
2. **α 值计算**：基于高斯的不透明度和 2D 投影范围
3. **深度排序**：按相机距离排序，确保从后往前混合正确
4. **自适应大小**：根据相机距离调整椭圆大小，实现 LOD-ish 效果

PlayCanvas 的 `createGraphicsDevice` 被传入了精细的调优参数：`antialias: false, depth: false, stencil: false`。这很有意思——因为高斯泼溅不需要深度测试和模板缓冲，α-blending 本身就是排序混合，关闭它们能节省 GPU 带宽。

## 四、工具生态：11 个编辑工具覆盖全场景

SuperSplat 注册了 11 个工具，分为三大类：

**🔲 6 + 2 种选择工具：**

- `RectSelection`：矩形框选（屏幕空间）
- `BrushSelection`：笔刷选择（像在 3D 场景里"涂抹"选中）
- `FloodSelection`：洪水填充式选择（点击一个高斯，选中周围相似的）
- `PolygonSelection`：多边形框选
- `LassoSelection`：套索选择
- `SphereSelection` / `BoxSelection`：3D 空间球体/立方体选择
- `EyedropperSelection`：颜色吸管

**🔄 3 种变换工具：**

- `MoveTool`：平移选中的高斯
- `RotateTool`：旋转
- `ScaleTool`：缩放

**📐 1 种测量工具：**

- `MeasureTool`：空间距离测量

这些工具的注册方式也说明了架构的扩展性——`ToolManager` 就是一个注册表，任何新工具只要实现统一接口就能挂上去：

```typescript
toolManager.register('sphereSelection', new SphereSelection(events, scene, editorUI.canvasContainer));
toolManager.register('measure', new MeasureTool(events, scene, editorUI.toolsContainer.dom, editorUI.canvasContainer));
```

选择工具通过 `maskCanvas`（一个独立的 2D canvas）进行像素级选区内运算。`FloodSelection` 和 `BrushSelection` 用 `globalCompositeOperation = 'copy'` 的 canvas 上下文做高效的选区遮罩合成——不经过 GPU，避免了渲染到读回的瓶颈。

## 五、文件 I/O：SPLAT、PLY、SOG、PNG、SVG 全格式覆盖

编辑高斯泼溅的第一步是导入数据。SuperSplat 的 IO 层分 `src/io/read/` 和 `src/io/write/` 两个子模块，支持多种格式：

**导入：**

- **`.ply`**：COLMAP + 3DGS 的标准输出格式，包含高斯的位置、协方差矩阵、颜色和不透明度
- **`.splat`**：Anti-Martian 的原始格式，更紧凑
- **`.sog`**：SuperSplat 自研的优化格式（WebP 压缩），基于 `@playcanvas/splat-transform` 库

**导出：**

- **`.ply`**：编辑后重新导出
- **`.sog`**：压缩格式，体积可减少 60-80%
- **`.png`**：截图导出（通过 blit-shader 读取帧缓冲）
- **`.svg`**：矢量导出（主要用于标注/测量结果）

SOG 格式是 SuperSplat 的一个关键技术亮点。它使用 WebP 图像压缩技术来存储高斯数据，`WebPCodec.wasmUrl` 指向编译好的 WebP WASM 库来完成解码。这使得高斯文件的磁盘体积大幅缩小——一个 500MB 的 `.ply` 转为 `.sog` 后可能只有 100MB 左右。

文件导入在 `scene.start()` 后通过事件触发：

```typescript
await events.invoke('import', [{
    filename: 'scene.ply',
    url: 'https://example.com/scene.ply'
}]);
```

SuperSplat 甚至支持 PWA 的文件关联——在支持 `launchQueue` 的浏览器中，双击 `.ply` 或 `.splat` 文件可以直接打开编辑器：

```typescript
if ('launchQueue' in window) {
    window.launchQueue.setConsumer(async (launchParams) => {
        for (const file of launchParams.files) {
            await events.invoke('import', [{ filename: file.name, contents: await file.getFile() }]);
        }
    });
}
```

## 六、UI 系统：PCUI 框架 + 7 个面板 + 完整国际化

SuperSplat 的 UI 没有选择主流 Web 框架，而是用了 PlayCanvas 自己的 **PCUI** 框架（6.1.3）——一个专门为工具类应用设计的 React 替代品，输出原生的 DOM 元素。

编辑器界面被拆成了 **7 个主要面板**：

| 面板                     | 文件                  | 大小  | 功能                                       |
| ------------------------ | --------------------- | ----- | ------------------------------------------ |
| **Data Panel**     | `data-panel.ts`     | 16KB  | 高斯属性编辑（位置、缩放、颜色、不透明度） |
| **View Panel**     | `view-panel.ts`     | 14KB  | 视角控制、背景色、环境光                   |
| **Timeline Panel** | `timeline-panel.ts` | 15KB  | 关键帧动画和序列控制                       |
| **Scene Panel**    | `scene-panel.ts`    | 3.8KB | 场景层级和可见性管理                       |
| **Bottom Toolbar** | `bottom-toolbar.ts` | 10KB  | 工具切换、统计信息                         |
| **Right Toolbar**  | `right-toolbar.ts`  | 6.6KB | 编辑动作、对齐、分割                       |
| **Editor Shell**   | `editor.ts`         | 15KB  | 编辑器主框架、按钮、下拉菜单               |

每个面板都是一个独立的 PCUI 组件模块，通过事件总线与场景通信。例如 `right-toolbar.ts` 提供删除选中高斯、分割高斯集群、重置变换等操作按钮。

**本地化方面**，SuperSplat 使用 `i18next` 实现多语言支持，`localization.ts` 中完成初始化。目前支持的语言覆盖了中、英、日、韩、法、德等主要语言。启动时 `await localizeInit()` 会异步加载翻译资源。

**用户引导**也很完整——`about-popup.ts`（5.9KB）提供版本信息和项目背景，`shortcuts-popup.ts`（9KB）展示所有快捷键，`tooltips.ts`（4.3KB）为所有按钮提供悬浮提示。这些细节加起来，让 SuperSplat 的使用体验不像一个开源玩具，更像一个成熟的产品。

## 七、时间线与动画系统

SuperSplat 在 v2.x 系列中加入了时间线功能。这部分在 `src/timeline.ts`、`track-manager.ts`、`ply-sequence.ts` 中实现。

核心思路是：**高斯泼溅场景的状态随时间变化**。Timeline 记录了每个关键帧时刻的场景快照——高斯的变换矩阵、可见性、不透明度等。播放时在关键帧之间插值，实现场景动画。

`ply-sequence.ts` 支持导入一个序列的 `.ply` 文件（通常是不同时间步的 3DGS 重建结果），在时间线上生成对应的帧序列。这对于展示动态场景（比如一个人说话、一个物体变形）非常实用。

配合 `camera-poses.ts` 和 `camera-pose-gizmos.ts`，用户还可以为相机路径设置关键帧，生成环绕镜头或路径漫游动画。

这套系统的工程实现不算复杂，但它巧妙的地方在于复用了所有已经实现的编辑工具——你在时间线的某一帧上选中高斯、变换、删除，这套操作在另一帧上同样有效。

## 八、发布管线：从编辑器到 PlayCanvas 平台

编辑完的场景怎么用？SuperSplat 在 `publish.ts` 和 `export-popup.ts`（16KB）中实现了一套完整的发布管线。

**三种使用方式：**

1. **导出原始文件**：`.ply` / `.sog` / `.splat`，直接下载到本地
2. **发布到 PlayCanvas**：通过 PlayCanvas 的 API 将编辑后的高斯场景直接上传到 PlayCanvas 项目，在游戏引擎中加载渲染
3. **截图/视频导出**：`image-settings-dialog.ts` 和 `video-settings-dialog.ts` 提供导出图片和高斯场景漫游视频的配置面板，`png-compressor.ts` 负责压缩

特别是发布到 PlayCanvas 这条路径，让 SuperSplat 从"独立编辑器"变成了"PlayCanvas 生态的素材预处理工具"——你在编辑器中完成的修剪、优化、着色，可以直接嵌入到 WebGL/WebGPU 游戏中。

## 九、性能与优化机制

处理数百万个高斯点云，在浏览器中保持 30fps+ 的交互帧率，SuperSplat 做了几件事：

**1. 延迟加载和渐进渲染**：`scene.start()` 创建了异步模型加载管线。场景不会一次性加载所有高斯，而是分批次送入 GPU。

**2. WASM 协处理器**：`@playcanvas/splat-transform`（2.1.0）是一个独立的 WebAssembly 库，负责高斯数据的序列化/反序列化、格式转换和排序。WASM 的运行速度远超 JavaScript 等效实现，尤其是在处理百万级高斯排序时。

**3. 帧缓冲优化**：`createGraphicsDevice` 传入了 `antialias: false, depth: false, stencil: false`——明确告诉 PlayCanvas 你不需要这些功能。高斯渲染走的是自定义着色器管线，不需要标准 3D 管线的帧缓冲附属品。

**4. 强制重渲染标记**：颜色和选择状态变化通过 `scene.forceRender = true` 触发重渲染，而不是每帧渲染。这减少了不必要的 GPU 调用。

**5. 视角相关 LOD**：虽然没有显式的多级 LOD，但高斯投影到屏幕时的椭圆大小与相机距离直接相关——远处的自动变小甚至消失，这是 3DGS 渲染本身自带的"自然 LOD"特性。

## 十、开发者体验：144 个版本的迭代曲线

SuperSplat 从 2023 年末的第一个版本到现在的 v2.25.1（仅 1 年半左右），发布了 **144 个版本**。几乎每 3-4 天就有一个新版本。这个发布节奏在开源工具类项目中非常罕见。

项目使用 **Rollup** 打包（TypeScript + SCSS），**ESLint**（`@playcanvas/eslint-config`）做代码规范，没有 test 框架——直接从源码编译后在浏览器中手动验证。这种做法对一些项目来说可能不够"工程化"，但对于一个重度依赖 WebGL/WebGPU 图形管线的项目来说，自动化测试很难覆盖实际渲染效果，人工验证反而是最可靠的。

从贡献者角度看，项目结构极其模块化：

```
src/
├── core/        # Scene, Events, Camera, Renderer
├── tools/       # 11 个编辑工具
├── shaders/     # 12 个自定义着色器
├── ui/          # PCUI 面板 + SCSS + SVG 图标
├── io/          # 读写模块（read/ + write/）
├── data-processor/  # 高斯数据分析（bound, intersect, positions）
└── operations/  # 复合编辑操作
```

每个目录独立、内聚，理解成本大幅降低。想加一个新工具？写一个类继承工具接口，在 `main.ts` 的 `toolManager.register()` 加一行——3 分钟的事情。

## 十一、SuperSplat 的位

在 3DGS 生态中，SuperSplat 占据了"编辑器"这个独一无二的位置。

| 工具                       | 功能                   | 定位       |
| -------------------------- | ---------------------- | ---------- |
| **COLMAP + 3DGS**    | 从照片重建高斯泼溅     | 数据生产   |
| **SuperSplat**       | 编辑、修剪、优化、发布 | 数据加工   |
| **PlayCanvas**       | 在 Web 应用中渲染      | 数据消费   |
| **Three.js GS 插件** | 渲染已处理的高斯       | 备选消费端 |

没有 SuperSplat 之前，3DGS 工作流是"重建 → 直接使用"，中间缺少了一个关键的编辑环节。有了它之后，你可以在浏览器里完成一整套"重建 → 编辑 → 优化 → 发布"的闭环。

## 十二、未来与思考

SuperSplat 的前路有几个明确的方向。

**WebGPU 支持**已经在路上了。PlayCanvas 引擎的 WebGPU 后端正在开发中，一旦就绪，SuperSplat 可以直接切换。WebGPU 的计算着色器（Compute Shader）对高斯排序有天然优势——目前深度排序在 CPU 端做，是性能瓶颈，WebGPU 可以在 GPU 上直接排序。

**流式加载**是另一个大方向。目前整个高斯文件需要全部加载到内存才能编辑。对于超过 1 亿个高斯的场景（城市级重建），这个方法不现实。如果改成视锥体裁剪 + 流式加载，可以处理任意大的场景。

**协作编辑**——从代码中可以看到 `src/operations/` 目录已经有了雏形。多人同时编辑同一个高斯场景在浏览器中实现，靠事件同步和操作压缩，理论上可行。

我觉得 SuperSplat 做对的最重要的一件事是：**选择不造轮子**。渲染用 PlayCanvas，UI 用 PCUI，格式转换用 splat-transform（WASM），压缩用 WebP。它只做一件事——编辑，而且做到极致。

对于一个开源编辑器来说，144 个版本比 7.6K Stars 更有说服力。Stars 可以靠宣发，144 个版本只能靠持续开发。

> 如果你在做 3D 扫描、NeRF/3DGS 相关的项目，或者只是想看看"在浏览器里实时编辑百万级点云"到底能做到什么程度——[试试 SuperSplat](https://supersplat.io/)。拖一个 `.ply` 或者 `.splat` 文件进去就知道了。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)