# Copilot AI Coding Instructions for iwindoors

## Project Overview
**iwindoors** is a Hugo-based static blog site focused on window/door business content (洪窗-专注南昌封窗，换窗服务). The site combines personal blogging, product management, and friend linking features with an automated Douban data sync workflow.

- **Site**: https://iwindoors.com
- **Theme**: hello-friend (minimal, dark/light theme support)
- **Language**: Chinese (zh-CN)
- **Hosting**: Cloudflare Pages (auto-deploys from git push)

## Architecture & Key Components

### Content Structure
```
content/
├── posts/                 # Main blog articles
│   ├── chat/             # Personal chat/diary posts (tags: [词穷])
│   ├── coding/           # Tech tutorials & configuration guides (tags: [折腾])
│   ├── daily/            # Daily notes
│   └── reading/          # Book/reading notes
├── [root pages]          # Standalone pages with custom layouts:
│   ├── bb.md             # layout: "bb" (short posts/status)
│   ├── bbs.md            # layout: "bbs" (bulletin board)
│   ├── books.md          # layout: "books" (book collection)
│   ├── friends.md        # layout: "friends" (friend links)
│   ├── goods.md          # layout: "goods" (product showcase)
│   ├── gallery.md        # layout: "gallery" (photo gallery)
│   └── [others]          # album, caipu (recipes), search, talk, etc.
```

### Data Flow
- **Douban Integration**: `.github/workflows/douban.yml` syncs Douban movie/book data (CSV) every 12h via `lizheming/doumark-action`
- **Friends Data**: `static/friends.json` - friend links with Gravatar md5 avatars
- **Goods/Product Data**: `static/goods/goods.json`

## Development Conventions

### Front Matter & Content Patterns
**Post Template** (`static/template/coding-template.md`):
```markdown
---
title: "Post Title"
date: YYYY-MM-DDTHH:mm:ssZZ  # ISO format with timezone
tags: [折腾]              # "折腾" (tinkering) for tech posts, "词穷" (diary)
feature: https://...      # Optional featured image
---
```

**Critical Content Rules**:
- Use `<!--more-->` (NOT `<!-- more -->` with spaces) for post excerpt separator
- Date format must be consistent (`YYYY-MM-DD` or ISO 8601) or Hugo won't sort properly
- HTML tags require `unsafe = true` in `config.toml` markup settings (already configured)

### Custom Layouts
Each root-level content page (`bb.md`, `books.md`, etc.) uses custom layouts in `themes/hello-friend/layouts/`:
- `layouts/[pagename]/single.html` - custom rendering for special pages
- Example: `bb.html`, `bbs.html`, `books.html`, `friends.html`, `goods.html`

If adding new special pages:
1. Create `content/[newpage].md` with `layout: "[newpage]"`
2. Create `themes/hello-friend/layouts/[newpage]/single.html`

### Shortcodes
Located in `themes/hello-friend/layouts/shortcodes/`:
- `book.html` - renders book cards: `{{<book image author description link>}}`
- Extend by adding `.html` files and reference in content via `{{< name params >}}`

## Critical Workflows

### Git & Deployment
**Publish Flow**:
1. Edit content locally in VSCode or push to GitHub
2. Git push to `master` branch triggers Cloudflare Pages rebuild (auto Hugo build)
3. No manual build needed - Cloudflare detects git changes

**GitHub Actions**:
- `.github/workflows/douban.yml` - Runs on schedule (0, 12, 23 UTC) + watch events
- Commits Douban data updates with message `'chore: update douban data'`

### Content Creation Tips
- Use template files in `static/template/` as starting points (support Templater plugin syntax)
- Keep posts in category subdirectories (`posts/coding/`, `posts/chat/`, etc.)
- Tag conventions: `[折腾]` for tech, `[词穷]` for personal/diary content

## Configuration Points

### config.toml Essentials
```toml
baseURL = "https://iwindoors.com"
languageCode = "zh-CN"
theme = "hello-friend"
paginate = 10              # Posts per page
hasCJKLanguage = true      # Chinese language support

[params]
  defaultTheme = "dark"    # Dark mode by default
  showReadingTime = false
  EditPath = 'https://github.com/UzMark-Git/iwindoors/edit/master/content'

[markup.goldmark.renderer]
  unsafe = true            # Allow raw HTML in markdown

[outputs]
  home = ["Atom", "HTML", "JSON"]  # Generate Atom feed + JSON
```

**Theme Configuration** (`themes/hello-friend/theme.toml`):
- Min Hugo version: 0.57+
- Features: dark/light themes, responsive, syntax highlighting

### Menu Structure
Configured in `config.toml` under `[menu.main]`:
```toml
[[menu.main]]
  name = "哔哔"          # Short posts/status
  url = "/bbs/"
  weight = 1
```
Menu order is controlled by `weight` attribute.

## External Dependencies & Integrations

| Component | Purpose | Reference |
|-----------|---------|-----------|
| `lizheming/doumark-action` | Douban data sync (movies, books as CSV) | GH Action in workflow |
| `Twikoo` | Comment system (enabled in params) | Deployed separately |
| `hello-friend theme` | Blog theme | `themes/hello-friend/` |
| Cloudflare Pages | Static hosting + auto-deploy | CI/CD via git hook |

## Common Patterns & Gotchas

1. **Post Sorting Issues**: Ensure `date:` field is valid ISO format; bad dates break chronological ordering
2. **More Tag**: Must be `<!--more-->` exactly - extra spaces break excerpt detection
3. **HTML Rendering**: Raw HTML in content needs `markup.goldmark.renderer.unsafe = true`
4. **i18n**: Site is Chinese-centric; all UI strings in config use Chinese characters
5. **Douban Updates**: Auto-sync runs 4 times daily; manual check-in of data is git-managed (not direct DB)

## File References by Purpose

| Purpose | Key Files |
|---------|-----------|
| Add blog post | `content/posts/[category]/title.md` + use template |
| Add special page | `content/[name].md` + `themes/hello-friend/layouts/[name]/single.html` |
| Update friends | `static/friends.json` (add objects with name, url, md5, des) |
| Update products | `static/goods/goods.json` |
| Modify theme styling | `themes/hello-friend/static/` or `layouts/` |
| Trigger Douban sync | Push any commit + wait for scheduled workflow (or manual run) |
| Customize menu | Edit `[menu.main]` in `config.toml` |
