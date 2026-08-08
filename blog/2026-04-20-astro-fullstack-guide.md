## 为什么选择 Astro

Astro 是一个现代化的静态网站生成器，它引入了创新的 **岛屿架构（Islands Architecture）**，能够在构建时生成纯静态 HTML，只在需要交互的地方加载 JavaScript。这使得 Astro 网站在性能上具有天然优势。

### 核心优势

1. **零 JS 默认**：默认情况下，Astro 页面不加载任何 JavaScript
2. **内容集合**：类型安全的 Content Collections，让内容管理变得简单
3. **多框架支持**：可以在同一项目中混用 React、Vue、Svelte 等组件
4. **优秀的 SEO**：天然支持 SSG，搜索引擎友好

## 项目初始化

首先，创建项目：

```bash
bun create astro@latest
```

安装必要的集成：

```bash
bunx astro add tailwind
bunx astro add sitemap
```

## 配置内容集合

在 `src/content/config.ts` 中定义博客文章的 Schema：

```typescript
import { z, defineCollection } from 'astro:content'

const blogCollection = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string().max(160),
    publishDate: z.date(),
    tags: z.array(z.string()).default([]),
    category: z.string().default('general'),
  }),
})
```

## 生成博客页面

使用 `getStaticPaths` 动态生成所有文章页面：

```typescript
export async function getStaticPaths() {
  const posts = await getCollection('blog')
  return posts.map((post) => ({
    params: { slug: post.slug },
    props: { post },
  }))
}
```

## 总结

Astro 为内容网站提供了极佳的开发体验和运行时性能。结合 Tailwind CSS 和 Content Collections，可以快速搭建一个类型安全、性能优异的技术博客。