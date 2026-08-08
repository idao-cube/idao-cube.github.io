上一篇我们聊了 OpenMontage「是什么」——把 AI 编程助手变成视频工作室，12 条管线、52 个工具、上百个 agent skill。那篇偏产品视角。

这一篇我们往下钻一层，聊它「怎么做到的」。

我真正感兴趣的不是它能出什么片子，而是它选择了一条在工程界很少见的设计路线：**没有任何运行时的 Python 编排器**。你的 Claude Code / Cursor / Copilot 本身就是控制平面，Python 只负责提供工具和落盘。这意味着「调度逻辑」「创意决策」「审查标准」全部活在 Markdown 和 YAML 里，而不是代码里。

这听起来像 Architecture Astronaut 的呓语，但钻进 `tools/` 和 `schemas/` 之后，我发现它有一套相当自洽的工程约束把这件事撑住了。下面拆开看。

---

## 一、BaseTool：所有能力的统一契约

OpenMontage 里每一个能力都是一个继承自 `BaseTool` 抽象基类的 Python 类。命名约定很克制——`PascalCase` 且无 `Tool` 后缀：`MusicGen`、`VideoCompose`、`ElevenLabsTTS`、`Transcriber`。

调用方式统一为 `.execute(params_dict)`，返回 `ToolResult`（`success` / `data` / `error`），没有 `.run()`。这个统一入口是后面「选择器模式」和「注册表自动发现」能成立的前提。

但真正有意思的是每个工具**声明式地描述自己**：

```python
class ElevenLabsTTS(BaseTool):
    name = "elevenlabs_tts"
    version = "1.0.0"
    tier = "VOICE"                    # CORE/VOICE/ENHANCE/GENERATE/SOURCE/ANALYZE/PUBLISH
    capability = "tts"               # 能力族，选择器靠这个路由
    provider = "elevenlabs"          # 具体后端
    runtime = "API"                  # LOCAL / LOCAL_GPU / API / HYBRID
    stability = "PRODUCTION"         # EXPERIMENTAL / BETA / PRODUCTION
    dependencies = {
        "env": ["ELEVENLABS_API_KEY"],
        "cmd": [],                    # 需要的二进制，如 ffmpeg
        "python": [],                 # 需要的 pip 包
    }
    fallback_tools = ["google_tts", "openai_tts", "piper_tts"]
    agent_skills = [".agents/skills/elevenlabs/..."]  # 指向 Layer 3 知识
    resource_profile = {"vram": 0, "network": "required", ...}
    retry_policy = {"max_retries": 3, "backoff": "exponential"}
```

注意这几个字段的分工：

- `capability` + `provider` 让一个「能力」可以有多个「实现」。TTS 这个 capability 下挂着 ElevenLabs、Google、OpenAI、Piper 四个 provider。
- `runtime` 区分云端 API 和本地 GPU/CPU。这是 OpenMontage「免费本地 + 付费云端」双轨支持的物理基础。
- `dependencies` 不是文档，是**可执行的运行时探针**——注册表靠它判断「这个工具现在能不能用」。
- `fallback_tools` 是一串有序降级链。API key 没配？自动掉到下一个。
- `agent_skills` 把「使用手册」嵌进调用链——agent 读到这个工具，必须先读对应 skill 再执行。

这套声明式元数据的价值在于：它把「这个工具存在吗、能不能跑、跑挂了怎么办、怎么用才对」全部变成了**可枚举、可查询、可序列化成 JSON** 的数据，而不是散落在代码逻辑里的隐式约定。

---

## 二、ToolRegistry：自动发现 + 能力信封

有了统一的 `BaseTool` 契约，注册表就不需要手动注册了。`ToolRegistry` 是个单例，通过 `pkgutil.walk_packages()` 自动扫描所有 `BaseTool` 子类。

这解决了 agentic 系统里一个很实际的问题：agent 在动手前必须**精确知道当前机器上到底有哪些能力可用**。硬编码工具列表会立刻腐烂——你加一个本地模型，列表没更新，agent 就不知道它能用。

注册表提供的关键查询：

```python
registry.get_by_capability("tts")      # 所有 TTS 工具
registry.get_by_provider("elevenlabs") # 所有 ElevenLabs 工具
registry.get_available()               # 依赖已满足的工具
registry.find_fallback("elevenlabs_tts")  # 解析降级链
registry.support_envelope()            # 完整能力报告（agent 消费）
registry.gpu_required_tools()          # 需要显卡的工具
registry.network_required_tools()      # 需要网络的工具
```

`provider_menu_summary()` 是给人类看的精简版，返回每个能力族的 `configured / total` 比例。agent 在 preflight 阶段会把这个翻译成人话：

```
YOUR CAPABILITIES
  Video Generation:  0/13 configured
  Image Generation:  1/7 configured
  Text-to-Speech:    1/3 configured
  Composition:       3/3 configured (FFmpeg, video_stitch, video_trimmer)
```

「X of Y configured」这个比值设计得很巧——它让「能力广度」对用户可见，而且天然引导出「再配一个 env var 就解锁 3 个 provider」的升级路径。

`capability_catalog()` 和 `provider_catalog()` 则分别按能力族和按厂商分组，agent 在做路由决策时直接查，而不是靠记忆或旧文档。AGENT_GUIDE 里反复强调：**Do not maintain hardcoded tool lists. Always query the registry at runtime.**

---

## 三、选择器模式：把多 provider 抽象成一刀

如果每次调用 TTS 都要 agent 自己写一串「先试 ElevenLabs，失败试 Google，再失败试 Piper」的逻辑，代码会灾难。OpenMontage 用三个 selector 工具把这件事收口：

| Selector | 路由能力 | 发现机制 |
|---|---|---|
| `tts_selector` | 文本转语音 | `registry.get_by_capability("tts")` |
| `image_selector` | 图像生成 | `registry.get_by_capability("image_generation")` |
| `video_selector` | 视频生成 | `registry.get_by_capability("video_generation")` |

选择器从注册表**动态发现**可用 provider，没有任何硬编码的 provider 顺序。路由优先级是：

```
用户显式指定 > 可用性 > 评分排序
```

评分维度包括任务契合度、质量、控制力、可靠性、成本、延迟、连续性。而且它会**透明地在不同 provider 之间适配输入 schema**——你不用关心 ElevenLabs 要 `voice_id` 而 Piper 要 `speaker`，选择器帮你翻译。

这个模式最优雅的地方在于：**新增一个 provider 工具，它自动对选择器可见，选择器代码一行都不用改。** 能力扩展是「加一个文件」而不是「改一堆路由逻辑」。这正是 OpenMontage 能堆到 13 个视频 provider、7 个图像 provider 还不失控的原因。

---

## 四、管线即 YAML，导演即 Markdown

每条管线是 `pipeline_defs/` 下的一个 YAML 清单。它声明的是**流程**而非实现：

```yaml
stages:
  - skill: skills/pipelines/animated-explainer/idea-director.md
    produces: brief
    tools_available: [web_researcher, ...]
    review_focus: [hook_clarity, target_platform]
    success_criteria: [...]
    human_approval_default: true
  - skill: skills/pipelines/animated-explainer/script-director.md
    produces: script
    ...
```

每个 stage 有几个关键属性：

- `skill` —— 指向一个「导演技能」Markdown 文件，教 agent **怎么**干这步
- `produces` —— 这一步产出的**规范产物**（canonical artifact）
- `tools_available` —— 这一步能用哪些工具
- `review_focus` —— 自我审查时盯什么
- `success_criteria` —— 什么叫「这步做完了」
- `human_approval_default` —— **是否要人工审批**（这个值具有约束力）

注意 `produces`。这是整个系统的关节。每个 stage 产出**一个**结构化 JSON 产物，成为下一步的输入契约。而且每个产物都要过 `schemas/artifacts/` 下的 JSON Schema 校验才能写 checkpoint。

这等于把「流水线数据流」做了强类型约束——只不过类型系统不是 TypeScript，是 JSON Schema。垃圾产物在写入 checkpoint 之前就会被拦下来，不会 propagate 到下游。

stage 的标准顺序：

```
research → proposal → script → scene_plan → assets → edit → compose
```

agent 在每个 stage 的实际动作循环是：

```
读管线清单 → checkpoint.get_next_stage() 找续跑点 →
读导演技能 → 用工具 → 用 reviewer 元技能自查 →
写 checkpoint → 需要审批则停下等人
```

---

## 五、Checkpoint：状态落盘与断点续跑

传统编排器把状态存在内存或数据库里。OpenMontage 把状态写成项目目录下的 JSON 文件：

```
projects/<project-id>/
├── artifacts/      # 每个 stage 的规范产物
├── assets/         # images/ video/ audio/ music/ subtitles.srt
├── renders/        # final.mp4 —— 最终交付物
└── checkpoint_<stage>.json
```

`lib/checkpoint.py` 提供 `write_checkpoint` / `read_checkpoint` / `get_next_stage` / `get_completed_stages` 等函数。状态值只有四种：`completed` / `failed` / `awaiting_human` / `in_progress`。

这套设计解决了 agentic 工作流里最痛的一个问题：**长流程中途挂了怎么办**。

因为每一步的产物和状态都落盘了，任何 stage 失败，下次重启直接从最后一个 checkpoint 续跑，已经完成的 stage 不用重来。而且 superseded 的 checkpoint 会自动归档到 `history/`，stage 重跑不会摧毁运行历史——这给「调试一次失败的生产」留了完整的审计轨迹。

人工审批门是硬约束。`human_approval_default: true` 的 stage，如果没拿到 `human_approved=True`，写 checkpoint 会直接抛 `GATE VIOLATION`。agent 不能在同一个回复里绕过闸门继续往下干——这是防「agent 自说自话把整条片子都渲染了」的保险丝。

---

## 六、预算治理：把成本变成一等公民

AI 视频最大的隐性成本不是 API 账单，是「你花一小时调 prompt，生成的东西用不上」。OpenMontage 把预算做成了**一等概念**：

- **执行前预估**：提交任何付费生成前，先告诉你大概花多少
- **预算预留**：行动前先 reserve 一笔
- **对账**：执行后 reconciliation，agent 不能偷偷超支
- **阈值拦截**：单行动作超过阈值（默认 $0.50）要你点头，总预算上限（默认 $10）封顶

决策日志（decision_log）是 append-only 的审计轨——不是草稿本。如果中途换了 provider、换了声音、fallback 覆盖了原选择，必须**追加一条新记录**（用相同的 `category` 和 `subject`），把被覆盖的选项挪进 `options_considered` 并标注 `rejected_because`。直接改旧记录是缺陷：看板会继续显示过期选择。

这个设计哲学很清晰：agent 的每一个「花你钱」和「改你片子」的决定，都必须可被追溯、可被质询。

---

## 七、三层知识架构：为什么智能在 skill 里

OpenMontage 反复强调一句话：**The intelligence is in the skills, not in improvised code.**

```
Layer 1: tools/ + pipeline_defs/   → 有什么、怎么编排（可执行能力 + 流程定义）
Layer 2: skills/                    → OpenMontage 期望你怎么用（创意技法、质量清单、产物映射）
Layer 3: .agents/skills/            → 底层技术怎么工作（FFmpeg/GSAP/HyperFrames 的厂商知识）
```

Layer 3 不是可选的。每个生成类工具（video/image/tts/music）的 `agent_skills` 字段都链着对应的 Layer 3 skill——里面是 provider 特定的 prompt 工程、参数调优、质量技巧。agent 必须在调用工具**之前**读这些文件。

README 里有一句很直白的话：用不用 skill，区别是「能用的」和「电影级的」。通用 prompt 和 skill-informed prompt 的差距，就是垃圾和成片之间的差距。

这套设计的工程含义是：**agent 的行为可以通过改文本文件来调，而不是改代码。** 想要更好的转场审美？改 `skills/meta/bespoke-composition.md`。想要更强的脚本结构？改 `script-director.md`。无需发版、无需重启。

代价也在这：系统质量的上限，取决于这些 skill 文件写得多好。400+ 个 skill 文件是一笔巨大的、持续的内容债务——也是 OpenMontage 真正的护城河。

---

## 八、两个渲染引擎与 Atelier 模式

合成层（`video_compose`）背后有三个渲染引擎，在提案阶段选定并锁进 `edit_decisions.render_runtime`：

| 引擎 | 擅长 | 依赖 |
|---|---|---|
| FFmpeg | 纯剪切/拼接/裁剪/字幕烧录 | ffmpeg 二进制（永远可用） |
| Remotion | 基于 React 的合成：图片→动画、文字卡、图表、弹簧物理转场、词级字幕 | Node.js + remotion-composer |
| HyperFrames | HTML/CSS/GSAP：动态文字、产品宣传、网站转视频、SVG 角色绑定 | Node ≥ 22 + npx |

有个硬规则：当机器上 FFmpeg、Remotion、HyperFrames 都可用时，agent **必须**把两个渲染引擎都摆给用户选，不能默默选默认。即使管线 manifest 或导演技能推荐了某一个，那也只是对话的输入，不是决定。

正交于「引擎」的还有**创作模式**：

- **Templated**：拼装现成的场景类型（`text_card`、`stat_card`、`bar_chart`…），快、便宜、可靠——也是为什么大多数视频「看起来都一个样」
- **Atelier**：从零手写合成，一次性视觉语言，不复用任何现成组件。hero 级作品（营销、发布、品牌）默认走这个

决定规则很妙：**复用引擎知识，永不复用创意组件。** Atelier 模式下，现成的场景目录和组件是被冻结的「雷同外观」，禁止复用。

---

## 九、这套设计站得住脚吗？

钻完代码，我对 OpenMontage 工程上的判断比产品层面更正面。

**它做对的事：**

1. **声明式能力元数据**让「运行时能做什么」变成可查询的数据，而不是腐烂的硬编码列表。注册表自动发现是规模化的关键。
2. **选择器模式**把「多 provider 路由」收敛成一个动态发现的抽象，新增能力零改动。
3. **规范产物 + JSON Schema 校验**把流水线数据流做强类型约束，垃圾不 propagate。
4. **checkpoint 落盘 + 断点续跑**解决了长流程 agentic 工作最实际的痛点。
5. **预算治理和 decision_log** 把「花钱」和「改片子」变成可审计、可质询的事件。

**我仍有保留的点：**

- **质量上限绑死在 agent 状态上。** 没有运行时编排器意味着每次执行的质量取决于 agent 当天的推理水平和 prompt 质量。链式幻觉会在七八个决策步里累积。
- **skill 文件是内容债务。** 400+ 个 Markdown 是护城河，也是维护噩梦。它们写得好系统才好，写得差系统就崩。
- **非技术用户门槛高。** 它本质是给「能在终端里干活、读得懂 yaml 和 markdown」的开发者用的。

---

## 结语

OpenMontage 最值得借鉴的，不是它能出什么片子，而是它回答了一个更底层的问题：**当 LLM 成为控制平面，软件架构该怎么重新组织？**

它的答案是——把「能做什么」做成声明式契约（tools），把「流程是什么」做成数据（YAML），把「怎么做好」做成可读文本（Markdown skill），把「状态」做成落盘文件（checkpoint）。代码退居幕后，只提供能力和持久化。

这条路未必是终局，但它比「再写一个中央编排器」更有想象力。如果你也在设计 agentic 系统，OpenMontage 的工具契约、选择器模式和 schema 校验的产物流，值得直接抄。

> 原创技术博客 · 开源项目架构深潜 · [idao.fun](https://idao.fun)