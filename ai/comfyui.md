## 产品与定位

开源模块化 AI 图像生成工作流编辑器，采用节点式流程设计。灵活性最高、性能最优，支持 SD 1.5/SDXL/SD3/Flux/AuraFlow 等所有现代架构。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 节点工作流 | 可视化构建复杂生成管线 |
| 模型支持 | SD/Flux/SD3/AuraFlow/PixArt 等 |
| 局部重绘 | Inpainting/Outpainting 精准控制 |
| 高清放大 | ESRGAN/SwinIR/Swin2SR 等上采样 |
| 工作流分享 | JSON 导出和社区分享 |
| 性能最优 | 比 A1111/Forge 性能更好 |
| 工作流复用 | 部分节点重执行，只更新变化的环节 |

## 与 A1111/Forge 对比

| 特性 | A1111 | Forge | ComfyUI |
| --- | --- | --- | --- |
| 界面 | 表单式 | 表单式 | 节点式 |
| 灵活性 | 中 | 中 | 最高 |
| 性能 | 中 | 好 | 最优 |
| 上手难度 | 低 | 低 | 中高 |
| 适合用户 | 初学者 | 进阶用户 | 高级用户 |

## 常用工具

| 工具 | 说明 |
| --- | --- |
| CivitAI | 模型和 LoRA 下载 |
| Stable Diffusion WebUI Forge | A1111 替代方案 |
| Locally Uncensored | 本地桌面应用 |
| Upsampler | 云端 Flux 工具 |

## 调用与兼容性

```bash
# 本地安装
git clone https://github.com/comfyanonymous/ComfyUI
cd ComfyUI
pip install -r requirements.txt
python main.py

# 使用云端服务
# Replicate、Fell 等平台提供云端运行
```

## 选型建议

需要复杂生成管线、追求最佳性能首选 ComfyUI；初学者建议从 Forge 入门，熟练后迁移 ComfyUI。