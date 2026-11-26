# 个人主页 · Jekyll 版

用 Jekyll 驱动、GitHub Pages 原生构建的个人网站。**新增内容的成本是「在 `_posts/` 放一个 Markdown 文件」**：列表、日期、上一篇/下一篇、RSS 全部自动生成，不需要改任何 HTML。

不需要 GitHub Actions，也不需要本地安装任何东西（本地预览才需要 Ruby）。

---

## 一、目录结构

```
个人网页/
├── _config.yml            站点配置：标题、网址、永久链接、插件
├── Gemfile                本地预览用（GitHub Pages 构建时不读它）
├── _data/
│   └── nav.yml            顶栏菜单，加一行就多一个入口
├── _layouts/
│   ├── default.html       全站骨架：head、顶栏、内容容器
│   └── post.html          文章页：日期、标题、标签、正文、上一篇/下一篇
├── _posts/                ★ 所有文章都在这里，一个文件一篇
│   ├── 2026-11-20-placeholder-one.md
│   ├── 2026-10-08-placeholder-two.md
│   └── 2026-09-12-placeholder-three.md
├── index.html             home 页：一句打招呼的话
├── shell/
│   └── index.html         shell 列表页（自动列出 _posts 里的全部文章）
├── 404.html               自定义 404
├── assets/
│   ├── style.css          全部样式
│   └── favicon.svg
└── README.md
```

---

## 二、新增一篇

在 `_posts/` 里新建一个文件，命名：

```
YYYY-MM-DD-slug.md
```

比如 `2026-12-01-hello.md`。日期用来排序，`slug` 是英文短名，会出现在网址里。

内容长这样：

```markdown
---
title: 今天的标题
tags: [随笔, 生活]
---

第一段。空一行就是新的一段。

## 小标题也可以

> 引用在行首加 `>`。

正文继续写，Markdown 支持的都能用。
```

保存、push，等一两分钟，列表顶部就会多出这一条，网址是 `/shell/2026-12-01/hello/`。

### 不写标题也可以

日记常常不需要标题。front matter 里**删掉 `title` 这一行**就行：

```markdown
---
tags: [随笔]
---

第一段。这一段会成为列表里显示的那句话。
```

不写标题时：列表显示「日期 + 正文第一句」，文章页顶部只有日期，浏览器标签也用日期。上一篇/下一篇同样会自动改用日期，不会出现空白链接。

> 只有 `title` 是可选的了，`---` 这一对分隔线必须保留，否则整篇不会被识别为文章。

### front matter 字段

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `title` | 否 | 标题，显示在列表、文章页和浏览器标签上；不写就是无标题条目 |
| `tags` | 否 | 写成 `[标签一, 标签二]`，显示在文章顶部；不要就整行删掉 |
| `date` | 否 | 默认取**文件名里的日期**；想精确到时间才需要加，如 `2026-12-01 21:30:00 +0800` |
| `published` | 否 | 写 `false` 就是草稿，不会被发布，适合先写一半 |
| `excerpt` | 否 | 列表里显示的摘要；不写就自动取正文第一段 |
| `layout` | 否 | 已在 `_config.yml` 里默认成 `post`，不用写 |

### 三个和日期有关的坑

1. **不要用未来的日期**。Jekyll 默认不发布「未来」的文章，写了明天的时间，明天之前列表里都看不到；确实要提前写，就在 `_config.yml` 加 `future: true`。
2. 文件名必须是 `YYYY-MM-DD-` 开头，少一位、用 `/` 分隔都不认，文件会被静默忽略。
3. 同一天写多篇没问题，`slug` 不同，网址就不会撞。

---

## 三、改菜单

编辑 `_data/nav.yml`：

```yaml
- label: home
  url: /
- label: shell
  url: /shell/
```

加一个页面就加一行，五个页面（home、shell、文章页）会同时生效，不用逐个改 HTML。当前页的高亮由 `page.url` 自动判断。

---

## 四、改设计

- **配色、字体、间距**：`assets/style.css` 顶部 `:root` 的变量；深色模式在同文件下面的 `prefers-color-scheme` 块里。
- **页面骨架**（head、顶栏、菜单图标）：`_layouts/default.html`。
- **文章页结构**（日期、标题、标签、正文、上下篇）：`_layouts/post.html`。
- **列表页**：`shell/index.html` 里的 `{% for post in site.posts %}` 循环。

改完 push 即可，GitHub Pages 会重新构建。

---


## 五、出问题先看这里

- **构建失败**：仓库 **Actions** 标签页 → 最新的 `pages build and deployment` → 日志里会写哪个文件哪一行出错；常见原因是 front matter 少了一条 `---`。
- **文章没出现**：文件名日期格式、未来日期、`published: false` 三选一。
- **列表是空的**：`_posts` 目录名必须带下划线，且文件必须是 `.md` 或 `.html`。
- **样式全丢**：`baseurl` 配错了（项目站点忘了填 `/仓库名`）。
