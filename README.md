# iwindoors

洪窗官网源码，基于 Hugo 构建，站点定位为「专注南昌封窗、换窗、阳台整装服务」。

线上地址：[https://iwindoors.com](https://iwindoors.com)

## 项目简介

这是一个中文静态站点项目，使用自定义过的 `hello-friend` 主题，包含博客文章、独立页面、友链、好物清单、相册、搜索、评论以及豆瓣数据同步等功能。

主要特性：

- Hugo 静态站点生成
- 中文内容优化，已开启 `hasCJKLanguage`
- 自定义 `hello-friend` 主题和多个页面模板
- Twikoo 评论配置
- Atom 订阅和首页 JSON 输出
- GitHub Actions 自动同步豆瓣电影、图书数据
- 适合通过 Cloudflare Pages 等静态托管平台部署

## 目录结构

```text
.
├── config.toml                 # Hugo 站点配置
├── content/                    # 页面和文章内容
│   ├── posts/                  # 博客文章
│   │   ├── chat/               # 闲聊/碎碎念
│   │   ├── coding/             # 技术折腾记录
│   │   ├── daily/              # 日常记录
│   │   └── reading/            # 阅读记录
│   ├── about.md                # 关于页面
│   ├── friends.md              # 友链页面
│   ├── goods.md                # 好物页面
│   ├── gallery.md              # 相册页面
│   └── search.md               # 搜索页面
├── data/
│   └── douban/                 # 豆瓣电影、图书 CSV 数据
├── static/                     # 静态资源
│   ├── friends.json            # 友链数据
│   ├── goods/goods.json        # 好物数据
│   ├── photos/                 # 相册图片
│   └── template/               # 文章模板
├── themes/
│   └── hello-friend/           # Hugo 主题和自定义布局
└── .github/workflows/
    └── douban.yml              # 豆瓣数据同步工作流
```

## 环境要求

- Hugo `0.57+`
- Git

安装 Hugo 后可确认版本：

```bash
hugo version
```

## 本地预览

在项目根目录执行：

```bash
hugo server -D
```

默认会启动本地预览服务，通常地址为：

```text
http://localhost:1313
```

如果只想生成静态文件：

```bash
hugo
```

生成结果会输出到 `public/` 目录，该目录已在 `.gitignore` 中忽略。

## 内容维护

### 新增文章

文章建议放在 `content/posts/` 下对应分类目录中：

- `content/posts/coding/`：技术、工具、折腾记录
- `content/posts/daily/`：日常记录
- `content/posts/chat/`：闲聊类内容
- `content/posts/reading/`：阅读记录

可参考 `static/template/` 里的模板创建文章。

常用 front matter 示例：

```markdown
---
title: "文章标题"
date: 2026-07-07T20:00:00+0800
tags: [折腾]
feature:
---

这里是正文摘要。

<!--more-->

这里是正文内容。
```

注意：

- 摘要分隔符使用 `<!--more-->`
- 日期建议使用 ISO 格式，避免文章排序异常
- 项目已允许 Markdown 中直接写 HTML

### 修改菜单

主导航菜单在 `config.toml` 的 `[menu]` 区域维护，通过 `weight` 控制排序。

当前菜单包括：

- 哔哔
- 友圈
- 好物
- 归档
- 搜索
- 关于

### 修改友链

友链数据位于：

```text
static/friends.json
```

### 修改好物

好物数据位于：

```text
static/goods/goods.json
```

相关图片位于：

```text
static/goods/
```

### 修改页面模板

自定义布局主要位于：

```text
themes/hello-friend/layouts/
```

短代码位于：

```text
themes/hello-friend/layouts/shortcodes/
```

如果新增特殊页面，通常需要同时添加：

1. `content/[页面名].md`
2. `themes/hello-friend/layouts/_default/[布局名].html` 或对应布局目录

## 豆瓣数据同步

项目使用 GitHub Actions 自动同步豆瓣数据：

```text
.github/workflows/douban.yml
```

同步内容：

- `data/douban/movie.csv`
- `data/douban/book.csv`

工作流使用 `lizheming/doumark-action`，豆瓣用户 ID 当前配置为 `immmmm`。同步后会自动提交更新，提交信息为：

```text
chore: update douban data
```

## 部署

项目适合部署到 Cloudflare Pages、Vercel、Netlify 等静态站点平台。

推荐构建命令：

```bash
hugo
```

推荐输出目录：

```text
public
```

站点基础地址在 `config.toml` 中配置：

```toml
baseURL = "https://iwindoors.com"
```

## 关键配置

核心配置文件为 `config.toml`。

常见配置项：

```toml
baseURL = "https://iwindoors.com"
languageCode = "zh-CN"
title = "洪窗-专注南昌封窗，换窗服务"
theme = "hello-friend"
paginate = 10
hasCJKLanguage = true
```

评论相关配置：

```toml
[params.twikoo]
  enable = true
```

输出格式：

```toml
[outputs]
  home = ["Atom", "HTML", "JSON"]
```

## 维护建议

- 修改内容前先确认文章 front matter 是否完整
- 新增图片放入 `static/` 下对应目录，正文中使用站点绝对路径引用
- 修改主题模板前先本地预览，避免影响多个页面
- `public/` 是构建产物，不需要提交
- 豆瓣数据由工作流维护，通常不需要手动编辑
