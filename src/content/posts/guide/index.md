---
title: 创建博客指南
published: 2026-01-01
description: "如何使用此博客模板"
image: "./cover.jpeg"
tags: ["博客"]
category: 指南
draft: false
---

## 文章的前置参数 (Front-matter)

```yaml
---
title: 我的博客文章
published: 2026-01-01
description: 我的新一篇文章
image: ./cover.jpg
tags: [Foo, Bar]
category: 前端
draft: false
---
```

| 属性 | 描述 |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `title`       | 文章的标题 |
| `published`   | 文章的发布日期 |
| `description` | 文章的简短描述，会显示在首页上 |
| `image`       | 文章封面图的路径，<br/>1. 以 `http://` 或 `https://` 开头：使用网络图片<br/>2. 以 `/` 开头：使用 `public` 目录中的图片<br/>3. 无上述前缀：相对于该 Markdown 文件的路径 |
| `tags`        | 文章的标签 |
| `category`    | 文章的分类 |
| `draft`       | 是否为草稿，如果设为 `true`，文章将不会被显示 |

## 放置文章文件的位置

您的文章文件应放置在 `src/content/posts/` 目录中。您还可以创建子目录，以便更好地管理您的文章和资源文件。

```
src/content/posts/
├── post-1.md
└── post-2/
    ├── cover.png
    └── index.md
```

---


## 具体操作指南：

### 一、修改或删除博文内容
所有的文章都保存在项目的 `src/content/posts/` 目录下。

#### 1.修改文章
1.  在本地找到该目录下的 `.md` 或 `.mdx` 文件。
2.  使用 VS Code 或记事本打开。
3.  **修改内容**：直接编辑正文。
4.  **修改元数据**：在文件顶部的 `---` 之间（Frontmatter），你可以修改：
    *   `title`: 标题
    *   `published`: 发布日期
    *   `image`: 文章封面图
    *   `tags`: 标签
5.  保存文件。

#### 2.删除文章
1.  直接在 `src/content/posts/` 文件夹中**删除**对应的 `.md` 文件。
2.  如果该文章有专门的图片文件夹（通常也在同级目录或 `assets` 下），也一并删除。

### 二、修改“关于”页面 (About)
1.  打开 `src/pages/about.astro`。
2.  这里使用的是 HTML/Astro 语法。直接修改里面的文字内容。
