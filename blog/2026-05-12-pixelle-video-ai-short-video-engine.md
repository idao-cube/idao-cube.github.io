> 项目地址：[github.com/AIDC-AI/Pixelle-Video](https://github.com/AIDC-AI/Pixelle-Video) | **15.4K Stars** | Apache 2.0 协议 | 340+ Commits | 12 Releases（v0.1.15）

## 一、Pixelle-Video 是什么？

Pixelle-Video 是一个由 AIDC-AI 打造的 AI 全自动短视频引擎。核心理念很简单：**输入一个主题，输出一条完整的视频**。不需要写脚本、找素材、学剪辑——AI 包办全部流程。

```text
一句话主题
    ↓
AI 写脚本 → AI 生成配图/视频 → TTS 配音 → 配 BGM → 合成输出
```

从 2025 年底开源以来，这个项目积累了 15,400+ Stars、340+ 次提交和 12 个正式版本。它的增长速度和社区活跃度，在 AI 短视频生成这个细分领域里算相当快的。微信社群和 Discord 都有稳定的用户讨论——这在中文 AI 开源项目里并不常见，很多项目火两周就冷下来了，Pixelle-Video 不是这样。

**核心亮点速览：**

| 特性                                   | 说明                                      |
| -------------------------------------- | ----------------------------------------- |
| **全自动流水线**                 | 输入主题→输出视频，零人工介入            |
| **3 条流水线**                   | standard / custom / asset_based，灵活组合 |
| **数字人 + 图生视频 + 动作迁移** | 最新支持的三个扩展模块                    |
| **声音克隆**                     | 上传参考音频克隆特定音色                  |
| **多模型 LLM**                   | GPT、通义千问、DeepSeek、Ollama 等        |
| **ComfyUI 架构**                 | 原子能力灵活组合，可替换任何环节          |
| **Windows 集成包**               | 解压双击即用，零配置                      |
| **完全免费方案**                 | Ollama + 本地 ComfyUI = 零成本            |

---

## 二、代码架构：PixelleVideoCore 与三流水线设计

大多数同类工具把"生成视频"做成一个黑盒函数——输入文本，输出视频，中间不可控、不可拆、不可定制。Pixelle-Video 不是这样。

### 2.1 PixelleVideoCore 服务层

核心代码在 `pixelle_video/service.py` 中，`PixelleVideoCore` 类是所有能力的统一入口：

```
PixelleVideoCore
  ├── config          # 全局配置管理器（支持热加载）
  ├── llm             # LLM 服务（直接调用 OpenAI SDK）
  ├── tts             # TTS 服务（ComfyKit 工作流）
  ├── media           # 媒体服务（图片/视频生成）
  ├── image_analysis  # 图片分析
  ├── video_analysis  # 视频分析
  ├── video           # 视频合成
  ├── frame_processor # 逐帧处理
  ├── persistence     # 文件持久化
  ├── history         # 历史记录管理
  └── pipelines       # 视频生成流水线
      ├── standard    # 标准流水线（默认）
      ├── custom      # 自定义模板流水线
      └── asset_based # 素材驱动流水线
```

### 2.2 ComfyKit：懒加载 + 配置热更新

Pixelle-Video 的核心依赖是 ComfyKit（`comfykit>=0.1.12`），它是 ComfyUI 的 Python SDK 封装，用来远程调用 ComfyUI 工作流。

但 ComfyKit 实例不是在初始化时就创建的。看 service.py 的实现：

```python
# ComfyKit 是懒加载的——只在第一次使用时创建
async def _get_or_create_comfykit(self) -> ComfyKit:
    current_config = self._get_comfykit_config()
    current_hash = self._compute_comfykit_config_hash(current_config)
  
    if self._comfykit is None or self._comfykit_config_hash != current_hash:
        # 关闭旧实例
        # 创建新实例
        self._comfykit = ComfyKit(**current_config)
```

通过 MD5 哈希检测配置变化，如果用户在 Web UI 中修改了 ComfyUI URL 或 API Key，不需要重启服务——下次调用时自动重建 ComfyKit 实例。这个设计在桌面应用场景里很实用：用户可能在生成间隙切换 GPU 服务器。

### 2.3 三流水线系统

Pixelle-Video 定义了三种视频生成流水线，`generate_video()` 方法通过 `pipeline` 参数选择：

```python
# 标准流水线（默认）
result = await pixelle_video.generate_video(text="如何提高学习效率", n_scenes=5)

# 自定义模板流水线
result = await pixelle_video.generate_video(text=your_content, pipeline="custom")

# 素材驱动流水线
result = await pixelle_video.generate_video(text=your_content, pipeline="asset_based")
```

- **standard**：LLM 生成脚本 → 分镜规划 → 逐帧生成配图 → TTS 配音 → 合成视频
- **custom**：按自定义模板的逻辑执行，用户可控制每一帧的呈现方式
- **asset_based**：上传用户自己的照片/视频，AI 分析素材内容后生成匹配脚本

每种流水线接收 `text` 和 `**kwargs`，返回统一的 `VideoGenerationResult`。如果要新增流水线，在 `pixelle_video/pipelines/` 下加一个类，注册到 `self.pipelines` 字典里就行。

---

## 三、完整处理流水线

一条视频从输入到输出经历以下步骤：

### 3.1 LLM 脚本生成

配置的 LLM（GPT / 通义千问 / DeepSeek / Ollama）根据主题生成文案。文案格式是结构化的，按"分镜"划分，每个分镜包含解说词和对画面的描述。

### 3.2 配图/视频生成

ComfyKit 调用 ComfyUI 工作流。支持的工作流类型：

| 类型     | 说明                   | 典型模型     |
| -------- | ---------------------- | ------------ |
| 文生图   | 根据画面描述生成插图   | FLUX / SDXL  |
| 文生视频 | 生成动态画面           | WAN 2.1      |
| 图生视频 | 上传图片，生成动态效果 | 动画模式     |
| 数字人   | 生成说话的数字人形象   | 支持多语言   |
| 动作迁移 | 参考视频驱动目标图片   | 如"跳舞的猫" |

### 3.3 TTS 语音合成

支持 Edge-TTS、Index-TTS（声音克隆）等多种引擎。`pixelle_video/tts_voices.py` 中列出所有可用音色。用户上传参考音频后，Index-TTS 会克隆该音色。

### 3.4 视频合成

使用 `moviepy==1.0.3` 进行视频合成，`ffmpeg-python` 处理编码。每一帧图片按分镜时长渲染，叠加 TTS 音频轨和 BGM 音轨，最终输出 MP4。

---

## 四、配置系统

### 4.1 LLM 配置

支持热门模型预设，也支持自定义 OpenAI 兼容接口：

| 模型               | 成本 | 特点                     |
| ------------------ | ---- | ------------------------ |
| **通义千问** | 极低 | 推荐中国用户，中文质量高 |
| **GPT-4o**   | 中等 | 英文和多语言场景         |
| **DeepSeek** | 低   | 性价比好                 |
| **Ollama**   | 免费 | 本地运行，零成本         |

预设通过 `pixelle_video/llm_presets.py` 管理。

### 4.2 图像配置

| 方式                           | 成本     | 说明                        |
| ------------------------------ | -------- | --------------------------- |
| **本地 ComfyUI**（推荐） | 零成本   | `http://127.0.0.1:8188`   |
| **RunningHub 云端**      | 按量计费 | 支持 48G 显存实例，并发可配 |
| **ComfyUI API Key**      | 按量     | 远程 ComfyUI 服务           |

配置通过 `config.yaml` 管理，支持在 Web UI 中热更新。

---

## 五、Web UI 三栏布局

界面使用 **Streamlit** 构建，三栏布局：

### 左侧栏：内容输入

- **AI 生成脚本**：输入主题，AI 自动写文案
- **固定文案**：手动粘贴已有文案
- **自定义素材**：上传照片/视频，AI 分析后生成脚本
- **脚本分割方式**：支持按段落/行/句子分割
- **BGM 选择**：内置曲库 / 自定义上传 MP3/WAV

### 中间栏：视觉与语音设置

```
┌─ 语音设置 ──────────────────────────┐
│ TTS 工作流选择（Edge-TTS / Index-TTS）│
│ 参考音频上传（声音克隆）             │
│ 语音预览（输入文本试听）             │
└──────────────────────────────────────┘
┌─ 视觉设置 ──────────────────────────┐
│ 图像工作流选择（Flux / SDXL 等）     │
│ 画面比例（竖屏/横屏/方形）          │
│ 提示词前缀（控制整体风格）          │
│ 模板选择（支持预览）                │
│ 预览风格按钮                       │
└──────────────────────────────────────┘
```

### 右侧栏：生成与预览

- **生成按钮**：一键启动完整流水线
- **实时进度**：显示当前步骤（如"分镜 3/5 - 生成插图"）
- **视频预览**：生成完成后自动播放，显示时长/大小/分镜数

---

## 六、模板系统

视频模板是 HTML 文件，存放在 `templates/` 目录，按命名约定自动归类：

| 前缀        | 类型     | 说明                      |
| ----------- | -------- | ------------------------- |
| `static_` | 静态模板 | 纯文字样式，无需 AI 媒体  |
| `image_`  | 图片模板 | AI 生成的图片作为背景     |
| `video_`  | 视频模板 | AI 生成的动态视频作为背景 |

模板按尺寸分组显示（竖屏/横屏/方形）。支持预览选择。懂 HTML 的话可以自己写模板。

---

## 七、三种扩展模块

这三个模块是 Pixelle-Video 区别于大多数"文本转视频"工具的核心差异化能力。

### 7.1 数字人口播

2026 年 1 月新增。将 TTS 语音与数字人形象结合，生成面对镜头说话的视频。支持多语言（包括韩语）。配置简单：选择数字人工作流，其他和普通视频生成一样操作。

### 7.2 图生视频

上传一张静态图片，AI 将其转化为动态视频。适合：

- 将封面图做成动态开头
- 将产品图转成展示视频
- 做出"会动的插画"效果

### 7.3 动作迁移

2026 年 1 月底新增。上传一段参考视频（如跳舞的人）和一张目标图片（如猫的插画），AI 从视频中提取动作序列，驱动目标图片做出同样的动作。

---

## 八、技术栈

| 层级                 | 技术选型                                              |
| -------------------- | ----------------------------------------------------- |
| **核心语言**   | Python 76.1%（`pixelle_video/` 目录结构清晰分层）   |
| **Web UI**     | Streamlit + FastAPI                                   |
| **包管理**     | uv                                                    |
| **工作流引擎** | ComfyKit（ComfyUI Python SDK）                        |
| **视频处理**   | moviepy ==1.0.3 + ffmpeg-python                       |
| **LLM**        | openai SDK（兼容 GPT / 通义千问 / DeepSeek / Ollama） |
| **TTS**        | edge-tts ==7.2.7 + Index-TTS（声音克隆）              |
| **运行时**     | Python >=3.11                                         |
| **代码质量**   | ruff（line-length=100, py311 target）                 |
| **文档**       | mkdocs（GitHub Pages）                                |
| **容器化**     | Docker + docker-compose                               |
| **测试**       | pytest + pytest-asyncio                               |
| **依赖**       | pydantic, httpx, loguru, pillow, fastmcp, playwright  |

完整依赖链 25+ 个包。值得注意的是 `comfykit>=0.1.12`——这是 Pixelle 生态自研的 ComfyUI Python SDK，也以独立项目形式存在。

---

## 九、快速安装

### Windows（推荐）

从 Releases 下载 Windows 集成包——Python、uv、ffmpeg 全部打进去了，解压双击 `start.bat` 就行。

### 源码安装（macOS / Linux）

```bash
# 前置依赖：uv + ffmpeg
git clone https://github.com/AIDC-AI/Pixelle-Video.git
cd Pixelle-Video
uv run streamlit run web/app.py
```

### Docker

```bash
docker-compose up
```

---

## 十、与其他视频生成工具对比

| 维度                           | Pixelle-Video            | MoneyPrinterTurbo | Sora      | Runway Gen-3 |
| ------------------------------ | ------------------------ | ----------------- | --------- | ------------ |
| **自动流水线**           | ✅ 完整 + 3 条流水线     | ✅ 类似           | ❌ 仅视频 | ❌ 仅视频    |
| **AI 脚本 + 分镜**       | ✅ 内置                  | ✅ 内置           | ❌        | ❌           |
| **TTS + BGM + 声音克隆** | ✅ 完整支持              | ✅ 基础           | ❌        | ❌           |
| **模板系统**             | ✅ HTML 灵活模板         | ❌                | ❌        | ❌           |
| **数字人**               | ✅ 支持                  | ❌                | ❌        | ❌           |
| **图生视频**             | ✅ 支持                  | ❌                | ❌        | ❌           |
| **动作迁移**             | ✅ 支持                  | ❌                | ❌        | ❌           |
| **配置热更新**           | ✅ 支持                  | ❌                | ❌        | ❌           |
| **本地运行**             | ✅ 完全本地 + Docker     | ✅                | ❌        | ❌           |
| **免费方案**             | ✅ Ollama + 本地 ComfyUI | ✅                | ❌        | ❌           |
| **协议**                 | Apache 2.0               | MIT               | 闭源      | 闭源         |
| **Stars**                | 15.4K                    | 35K               | —        | —           |

Pixelle-Video 的定位很清晰——它不是一个"视频生成模型"，而是一个**视频生成流水线编排引擎**。核心价值不在 AI 能力本身（底层的 LLM 和图像模型它一个都不自己训练），而在**如何把这些原子能力串成一条完整的、可定制的自动化流水线**。这是和 Sora、Runway 这类纯视频生成模型根本不同的思路。

---

## 十一、社区与增长

从 2025 年底到现在，Pixelle-Video 的增长轨迹值得注意：10.5K → 15.4K Stars（+46%），12 个 Release，74 个 Issue，340+ 次提交。社区有微信群和 Discord 双渠道支持。项目的文档站通过 mkdocs 部署在 GitHub Pages 上，FAQ 作为 Web UI 侧边栏内置。

---

## 十二、什么时候用它

如果你只是想"把一段文字变成一条视频"：

- 有 GPU 或愿意花点钱用云 ComfyUI → 完全可用，生成质量取决于你选的模型
- 什么显卡都没有 → 用通义千问 LLM + RunningHub 云端图像，成本很低
- 一分钱都不想花 → Ollama 本地 LLM + 本地 ComfyUI = 零成本

如果你已经有现成素材（照片/视频片段），可以用 Asset Pipeline 导入分析。要做数字人口播或动作迁移，这两个模块独立可用。

项目技术栈 Python 76% + HTML 23%，代码结构清晰，扩展一条新流水线的成本不高。

```bash
# 最简单的方式
# 下载 Windows 集成包 → 解压 → 双击 start.bat

# 或源码
git clone https://github.com/AIDC-AI/Pixelle-Video.git
cd Pixelle-Video
uv run streamlit run web/app.py
```

> 文档：[aidc-ai.github.io/Pixelle-Video/zh](https://aidc-ai.github.io/Pixelle-Video/zh)

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)