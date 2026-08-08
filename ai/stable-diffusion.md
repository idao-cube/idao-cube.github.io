## 产品与定位

Stability AI 推出的开源图像生成模型，提供多个 WebUI 界面选择。完全免费、本地运行、无限生成，是 AI 图像领域的开源基石。

## WebUI 选项

| WebUI | 特点 | 适合用户 |
| --- | --- | --- |
| A1111 | 原版，最稳定 | 老用户 |
| Forge | 性能优化，模型支持广 | 进阶用户 |
| ComfyUI | 节点式，最灵活 | 高级用户 |
| InvokeAI | 界面友好，专注于绘画 | 设计师 |

## 核心功能

| 功��� | 说明 |
| --- | --- |
| 文生图 | 文字描述生成图像 |
| 图生图 | 图像到图像转换 |
| 局部重绘 | Inpainting 精准修改 |
|  ControlNet | 姿态/深度/线稿控制 |
| LoRA | 微调风格和角色 |
| 高清放大 | SD upscale/Extra upscale |

## 调用与兼容性

```bash
# AUTOMATIC1111 WebUI
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
./webui.sh

# Forge WebUI
git clone https://github.com/lllyasviel/stable-diffusion-webui-forge
./webui-user.sh

# 模型下载
# CivitAI: https://civitai.com
# Hugging Face: https://huggingface.co/stabilityai
```

## 选型建议

初学者用 Forge，上手后根据需求选择 A1111（稳定）或 ComfyUI（灵活）。完全免费，适合追求可控和隐私的用户。