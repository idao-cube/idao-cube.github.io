## 项目简介

iDao技术魔方是一个基于Astro 6的全栈技术博客与个人品牌站点。它支持Markdown内容管理、RSS订阅、站内搜索、暗色模式等功能，旨在为技术爱好者提供一个简洁、高效、个性化的博客平台。

## 技术栈

- **Astro**: 用于构建现代Web应用的框架，支持组件化、静态站点生成等特性。
- **TypeScript**: 用于编写类型安全的JavaScript代码，提高代码的可维护性和可读性。
- **UnoCSS**: 用于构建高性能、可扩展的CSS框架，支持按需加载和主题定制。

## 功能特点

- **Markdown内容管理**: 支持使用Markdown编写博客文章，支持代码高亮、公式渲染等功能。
- **RSS订阅**: 提供RSS订阅功能，方便用户订阅博客更新。
- **站内搜索**: 支持站内搜索功能，方便用户快速查找文章。
- **暗色模式**: 支持暗色模式，适应不同用户的阅读习惯。

## 项目结构

```
├── src
│   ├── components
│   │   ├── Layout.tsx
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── PostList.tsx
│   │   ├── PostItem.tsx
│   │   ├── PostDetail.tsx
│   │   ├── Search.tsx
│   │   └── ...
│   ├── pages
│   │   ├── index.astro
│   │   ├── posts.astro
│   │   ├── post/[slug].astro
│   │   └── ...
│   ├── styles
│   │   ├── global.css
│   │   └── ...
│   └── ...
├── astro.config.mjs
└── package.json
```

## 项目截图

![项目截图](https://idao.fun/images/astro-site.png)

## 项目链接

- [GitHub](https://github.com/idao-cube/astro-site)
- [Live Demo](https://idao.fun)