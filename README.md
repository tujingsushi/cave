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

## 二、新增一篇（日常只做这一件事）

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

### front matter 字段

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `title` | 是 | 标题，显示在列表、文章页和浏览器标签上 |
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

## 五、本地预览（可选）

线上构建就够用；但想改样式时立刻看到效果，可以在本地跑。需要 Ruby（Windows 推荐用 RubyInstaller）：

```bash
bundle install
bundle exec jekyll serve
```

打开 `http://127.0.0.1:4000`。改文件会自动重建。

`github-pages` 这个 gem 在 Windows 上偶尔装不上（要编译原生扩展）。装不动就退一步，用普通 Jekyll：

```bash
gem install jekyll jekyll-feed jekyll-sitemap
jekyll serve
```

没装 Ruby 也不影响写作 —— 直接 push，然后在仓库的 **Actions** 标签页看 `pages build and deployment` 的结果。

---

## 六、部署

**Settings → Pages → Source 选 Deploy from a branch → `main` / `(root)` → Save。**

Jekyll 由 GitHub 在服务端构建，不需要 Actions 配置，也不需要提交 `_site/`。

### 仓库名与网址

| 仓库名 | 配置 | 网址 |
| --- | --- | --- |
| `你的用户名.github.io` | `_config.yml` 里 `baseurl: ""` | `https://你的用户名.github.io` |
| `my-site` 之类 | `_config.yml` 里 `baseurl: "/my-site"` | `https://你的用户名.github.io/my-site/` |

`baseurl` 只写路径，结尾不要带 `/`。想换成自己的域名，在根目录加 `CNAME` 文件（一行域名），并在 Pages 设置里填同一个域名、勾选 Enforce HTTPS。

> 注意：仓库根目录**不要**再放 `.nojekyll` 文件，它是用来跳过 Jekyll 构建的，和现在的方案冲突。

---

## 七、自动生成的东西，不用手工维护

| 产物 | 地址 | 来源 |
| --- | --- | --- |
| shell 列表 | `/shell/` | `_posts` 目录扫描，按日期倒序 |
| 上一篇 / 下一篇 | 每篇文章底部 | `page.previous` / `page.next` |
| RSS | `/feed.xml` | `jekyll-feed` 插件 |
| 站点地图 | `/sitemap.xml` | `jekyll-sitemap` 插件 |
| 文章网址 | `/shell/年-月-日/短名/` | `_config.yml` 里的 `permalink` |

---

## 八、替换占位内容

| 占位符 | 位置 |
| --- | --- |
| `站点名` | `_config.yml` 的 `title` |
| `你的名字` | `index.html` 的打招呼 |
| 标语与说明 | `index.html` 的 `.intro-quote`、`shell/index.html` 的 `.page-desc` |
| 标题占位一 / 二 / 三 | `_posts/` 里的示例文章，可以整篇替换或删除 |

带虚线下划线（`.ph` 样式）的文字就是占位符，全部换完后可以删掉 `style.css` 里的 `.ph` 规则。

---

## 九、出问题先看这里

- **构建失败**：仓库 **Actions** 标签页 → 最新的 `pages build and deployment` → 日志里会写哪个文件哪一行出错；常见原因是 front matter 少了一条 `---`。
- **文章没出现**：文件名日期格式、未来日期、`published: false` 三选一。
- **列表是空的**：`_posts` 目录名必须带下划线，且文件必须是 `.md` 或 `.html`。
- **样式全丢**：`baseurl` 配错了（项目站点忘了填 `/仓库名`）。
