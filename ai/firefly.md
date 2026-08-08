## 产品与定位

Adobe 创意 AI 工具套件，深度集成 Creative Cloud 全家桶。适合设计师快速生成素材、修改图像和创作内容，遵循 Adobe 的 AI 伦理标准。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 生成式填充 | 选区扩展、内容填充、对象移除 |
| 文本效果 | 文字转图像、字体样式生成 |
| 图像扩展 | 延展画面边界智能补充 |
| 创意填充 | 颜色匹配、风格迁移 |
| 视频生成 | 文字转视频，支持运动效果 |

## 常用参数

| 参数 | 作用 | 常见建议 |
| --- | --- | --- |
| `prompt` | 描述词 | 中英文均可，详细描述效果更好 |
| `style` | 风格选择 | 照片、写实、艺术等预设风格 |
| `aspectRatio` | 画幅比例 | 根据使用场景选择 |
| `contentClass` | 内容类型 | 决定生成内容的适配度 |

## 调用与兼容性

```bash
# REST API 调用示例
curl -X POST https://firefly-api.adobe.io/v3/assets \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -d '{
    "prompt": "modern office workspace",
    "style": "photo",
    "aspectRatio": "16:9"
  }'
```

## 版本与更新注意

持续集成到 Photoshop、Illustrator、Express 等产品中。Creative Cloud 订阅用户可直接使用。

## 选型建议

Adobe 用户、设计工作流集成首选 Firefly；批量素材生成配合 Express 使用体验更佳。