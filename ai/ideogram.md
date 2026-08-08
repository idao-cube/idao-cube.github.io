## 产品与定位

专注于文字渲染和艺术风格生成的 AI 图像工具，在文字嵌入图像方面领先。支持精准的字体、颜色和布局控制，适合海报、Logo 和社交媒体内容。

## 功能速览

| 功能 | 说明 |
| --- | --- |
| 精准文字 | 业界领先的文字渲染能力 |
| 多风格 | 写实、插画、3D、海报等 |
| 颜色控制 | 精确的颜色和色调控制 |
| 比例调节 | 多种画幅比例可选 |
| 历史记录 | 保存生成历史和变体 |
| API 访问 | 开发者 API 支持 |

## 常用参数

| 参数 | 作用 | 示例 |
| --- | --- | --- |
| `style` | 风格预设 | `realistic`、`illustration`、`3d` |
| `aspect_ratio` | 画幅比例 | `1:1`、`16:9`、`9:16` |
| `prompt` | 图像描述 | 支持中英文 |

## 调用与兼容性

```bash
# API 调用示例
curl -X POST https://api.ideogram.ai/v1/describe \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "prompt": "A modern logo design",
    "style": "3d",
    "aspect_ratio": "1:1"
  }'
```

## 定价

| 套餐 | 价格 | 说明 |
| --- | --- | --- |
| Free | 免费 | 100 credits/天 |
| Plus | $8/月 | 1000 credits/月 |
| Pro | $20/月 | 无限生成 |

## 选型建议

需要精准文字渲染首选 Ideogram；海报、Logo、品牌设计等文字密集场景优势明显。