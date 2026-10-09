# 2025 Blog 项目结构说明
> 你好，我是ayun。想要访问我的网站吗， 嘻嘻。
> [点此即可查看 ayun的博客  （注：用魔法！）](https://ayun-blog.vercel.app/)
> 这是一份面向初学者的项目结构文档，帮助你理解每个文件和目录的作用。
> 本项目是一个基于 **Next.js 16** 的静态博客，特殊之处在于它**没有传统后端**，所有内容（文章、图片、配置）都存储在 GitHub 仓库中，通过 GitHub API 实现在线编辑和发布。

---

## 一、技术栈概览

| 技术 | 用途 |
|------|------|
| **Next.js 16** | 前端框架（React 的全栈框架） |
| **React 19** | UI 组件库 |
| **TypeScript** | 类型安全的 JavaScript |
| **Tailwind CSS 4** | CSS 样式框架 |
| **Zustand** | 轻量级状态管理 |
| **SWR** | 数据请求和缓存 |
| **Cloudflare / Vercel** | 部署平台 |
| **GitHub API** | 充当"数据库"，读写文章和图片 |

---

## 二、根目录文件

### `package.json`
项目的"身份证"——记录项目名称、版本、依赖包和脚本命令。`npm install` 就是根据这个文件安装依赖的。

常用命令：
- `pnpm dev` — 启动本地开发服务器（端口 2025）
- `pnpm build` — 构建生产版本
- `pnpm deploy` — 部署到 Cloudflare

### `tsconfig.json`
TypeScript 配置文件，告诉 TypeScript 编译器如何检查代码。`"@/*": ["./src/*"]` 这行定义了 `@` 符号代表 `src` 目录（比如 `@/components/card` 就等于 `src/components/card`）。

### `next.config.ts`
Next.js 的配置文件，包括：
- 开启 React Compiler（自动优化性能）
- 配置 `.svg`文件的导入方式
- 设置页面重定向（`/zh` 和 `/en` 自动跳转到首页）

### `wrangler.toml`
Cloudflare 部署配置文件。如果部署到 Vercel 则不需要这个文件。

### `open-next.config.ts`
Cloudflare 适配器的配置文件，一行代码即可让 Next.js 运行在 Cloudflare 上。

### `pnpm-lock.yaml` / `pnpm-workspace.yaml`
包管理器的锁定文件和 workspace 配置，确保团队成员安装的依赖版本一致。**你不需要手动修改。**

### `postcss.config.mjs`
PostCSS 配置文件，用于 Tailwind CSS 编译。**不需要手动修改。**

### `.gitignore`
告诉 Git 忽略哪些文件（如 `node_modules`、`.next` 等），不要提交到仓库。

### `.npmrc`
npm/pnpm 配置，这里设置了 `shamefully-hoist=true`（依赖提升策略）。

### `.prettierrc` / `.prettierignore`
代码格式化工具 Prettier 的配置。保持代码风格统一。

### `global.d.ts`
全局 TypeScript 类型声明文件。

### `LICENSE`
开源许可证文件。

---

## 三、目录结构

### `public/` — 静态资源目录

这个目录下的文件会被直接托管，可以通过 URL 直接访问。

```
public/
├── avatar.png                    # 网站头像/Logo
├── favicon.png                   # 浏览器标签页图标
├── manifest.json                 # PWA 配置（网站可以安装到手机桌面）
├── images/                       # 所有图片资源
│   ├── art/                      # 首页展示的艺术插图
│   ├── blogger/                  # "优秀博客"页面的博主头像
│   ├── christmas/                # 圣诞节雪花特效图片
│   ├── hats/                     # 首页帽子装饰图片
│   ├── misc/                     # 杂项图片
│   ├── pictures/                 # "照片墙"页面的照片
│   ├── project/                  # "我的项目"页面的项目截图
│   ├── share/                    # "推荐分享"页面的图标/截图
│   ├── blockies.svg              # 默认头像占位图
│   ├── cursor.svg                # 自定义鼠标光标样式
│   └── vue-kit.webp              # Vue 工具图标
└── blogs/
    ├── index.json                # ⭐ 博客文章索引文件（列出所有文章的信息）
    └── categories.json           # ⭐ 博客分类列表
```

### `scripts/` — 脚本目录

```
scripts/
└── gen-svgs-index.js             # 自动扫描 SVG 文件并生成导入索引
```

运行 `pnpm svg` 会自动扫描 `src/svgs/` 下所有 `.svg` 文件，并生成一个统一的 `index.ts` 导出文件。当你添加新的 SVG 图标时，运行这个命令即可自动更新。

### `src/` — 源代码主目录（核心！）

```
src/
├── app/                          # 📄 页面路由（Next.js App Router）
├── components/                   # 🧩 全局通用组件
├── config/                       # ⚙️ 配置文件
├── hooks/                        # 🪝 自定义 Hook（状态管理、数据获取）
├── layout/                       # 🏗️ 全局布局组件
├── lib/                          # 📚 工具库（核心业务逻辑）
├── styles/                       # 🎨 全局样式
└── svgs/                         # 🖼️ SVG 图标文件
```

---

## 四、src/app/ — 页面路由详解

Next.js 使用文件系统路由——`src/app` 下的每个文件夹对应一个 URL 路径。

### `src/app/layout.tsx` — 🌐 全局布局（最外层框架）

所有页面的"外壳"，定义了：
- HTML 文档结构（`<html>`、`<body>`）
- 网站的标题和描述（SEO 元数据）
- 全局 CSS 变量（主题颜色）
- 自定义鼠标光标
- 检测用户是否使用 Windows 系统

### `src/app/(home)/` — 🏠 首页

文件夹名带括号 `(home)` 表示它不影响 URL——首页的 URL 就是 `/`。

| 文件 | 作用 |
|------|------|
| `page.tsx` | 首页主文件，组合所有卡片组件 |
| `hi-card.tsx` | 欢迎卡片（显示你的个人介绍） |
| `art-card.tsx` | 艺术插图卡片 |
| `clock-card.tsx` | 时钟卡片 |
| `calendar-card.tsx` | 日历卡片 |
| `social-buttons.tsx` | 社交媒体链接按钮（GitHub、掘金等） |
| `share-card.tsx` | 推荐文章/分享卡片 |
| `aritcle-card.tsx` | 最新文章列表卡片 |
| `write-buttons.tsx` | 快捷写作按钮 |
| `like-position.tsx` | 点赞/位置相关卡片 |
| `hat-card.tsx` | 帽子装饰卡片 |
| `beian-card.tsx` | ICP 备案号卡片 |
| `home-draggable-layer.tsx` | 首页卡片拖拽功能 |

**子目录：**
- `config-dialog/` — 网站配置弹窗（主题色、布局、背景图等设置）
- `stores/` — 首页状态管理
  - `config-store.ts` — 全局配置状态（主题色、网站设置等）
  - `layout-edit-store.ts` — 首页卡片布局编辑状态
- `services/` — 数据推送服务
  - `push-site-content.ts` — 将配置修改推送到 GitHub 仓库

### `src/app/blog/` — 📝 博客文章列表页

URL: `/blog`

| 文件 | 作用 |
|------|------|
| `page.tsx` | 文章列表页（支持按日/周/月/年/分类 查看） |
| `types.ts` | 文章数据类型定义 |
| `components/category-modal.tsx` | 分类管理弹窗 |
| `services/save-blog-edits.ts` | 保存文章编辑（推送到 GitHub） |
| `services/batch-delete-blogs.ts` | 批量删除文章 |

### `src/app/blog/[id]/` — 📖 单篇文章详情页

URL: `/blog/文章id`

| 文件 | 作用 |
|------|------|
| `page.tsx` | 文章详情页（渲染 Markdown 内容） |

`[id]` 是 Next.js 的动态路由——方括号表示它是路径参数，实际 URL 中会被替换为具体的文章 ID。

### `src/app/write/` — ✍️ 文章写作/编辑页

URL: `/write`

| 文件 | 作用 |
|------|------|
| `page.tsx` | 写作页主体 |
| `components/editor.tsx` | Markdown 编辑器 |
| `components/preview.tsx` | 文章预览组件 |
| `components/sidebar.tsx` | 右侧边栏（元信息设置） |
| `components/actions.tsx` | 操作按钮（发布、保存等） |
| `components/sections/` | 分区组件（封面、图片、元信息） |
| `hooks/` | 自定义 Hook（加载文章、发布、写作数据） |
| `services/` | 推送/删除文章到 GitHub |
| `stores/` | 编辑器状态管理 |

### `src/app/write/[slug]/` — ✍️ 编辑已有文章

URL: `/write/文章id`

当你点击某篇文章的"编辑"时，会跳转到这个页面，自动加载已有文章内容。

### `src/app/projects/` — 🚀 我的项目页

URL: `/projects`

展示你的个人项目列表。支持编辑、添加、删除项目。

### `src/app/about/` — ℹ️ 关于网站页

URL: `/about`

网站介绍页面。

### `src/app/share/` — 🔗 推荐分享页

URL: `/share`

推荐的资源/工具/网站分享列表。

### `src/app/bloggers/` — 👥 优秀博客页

URL: `/bloggers`

展示其他优秀博主的链接和头像。

### `src/app/pictures/` — 🖼️ 照片墙页

URL: `/pictures`

展示照片集合，支持随机布局。

### `src/app/snippets/` — 📋 代码片段页

URL: `/snippets`

收集的代码片段。

### `src/app/music/` — 🎵 音乐页

URL: `/music`

音乐播放相关，`list.ts` 存储音乐列表。

### `src/app/clock/` — 🕐 时钟页

URL: `/clock`

独立的全屏时钟页面。

### `src/app/live2d/` — 🎭 Live2D 页

URL: `/live2d`

Live2D 动画角色展示页面。

### `src/app/image-toolbox/` — 🔧 图片工具箱

URL: `/image-toolbox`

图片处理工具页面。

### `src/app/svgs/` — 🎨 SVG 展示页

URL: `/svgs`

展示项目中的所有 SVG 图标。

### `src/app/rss.xml/` — 📡 RSS 订阅源

URL: `/rss.xml`

自动生成 RSS 订阅源（XML 格式），方便别人通过 RSS 阅读器订阅你的博客。

### `src/app/sitemap.ts` — 🗺️ 站点地图

自动生成 `sitemap.xml`，帮助搜索引擎（Google、百度）收录你的博客页面。

---

## 五、src/components/ — 全局通用组件

这些组件在多个页面中复用。

| 文件 | 作用 |
|------|------|
| `card.tsx` | 通用卡片容器（统一的圆角、阴影、背景样式） |
| `nav-card.tsx` | ⭐ 全局导航栏（首页显示完整导航，其他页显示图标导航） |
| `blog-preview.tsx` | 博客文章预览渲染组件 |
| `blog-sidebar.tsx` | 博客页面侧边栏 |
| `blog-toc.tsx` | 文章目录（Table of Contents） |
| `code-block.tsx` | 代码块组件（支持语法高亮、复制） |
| `color-picker.tsx` | 颜色选择器 |
| `dialog-modal.tsx` | 通用弹窗/对话框 |
| `markdown-image.tsx` | Markdown 图片渲染组件 |
| `music-card.tsx` | 音乐播放卡片 |
| `scroll-top-button.tsx` | 回到顶部按钮 |
| `like-button.tsx` | 点赞按钮 |
| `star-rating.tsx` | 星级评分组件 |
| `select.tsx` | 下拉选择器 |
| `wip.tsx` | "施工中"占位组件（Work In Progress） |
| `liquid-grass/` | "液态草地"动画特效组件 |

---

## 六、src/layout/ — 全局布局组件

| 文件 | 作用 |
|------|------|
| `index.tsx` | ⭐ 全局布局主文件（组装背景、导航、弹窗、音乐播放器） |
| `header.tsx` | 页头（目前为空，保留给未来使用） |
| `footer.tsx` | 页脚（目前为空，保留给未来使用） |
| `head.tsx` | HTML `<head>` 标签内容 |
| `backgrounds/blurred-bubbles.tsx` | 模糊气泡动态背景 |
| `backgrounds/snowfall.tsx` | 雪花飘落特效（圣诞节模式） |
| `backgrounds/utils.ts` | 背景工具函数 |

---

## 七、src/lib/ — 核心工具库

这是项目的"大脑"，包含所有核心业务逻辑。

| 文件 | 作用 |
|------|------|
| `github-client.ts` | ⭐⭐ **最核心的文件**。封装了所有 GitHub API 调用——获取令牌、读写文件、创建提交、批量操作 |
| `auth.ts` | ⭐ 认证管理。缓存和管理 GitHub 访问令牌（Token），自动处理 JWT 签发和令牌刷新 |
| `aes256-util.ts` | AES-256-GCM 加密解密。用于保护存储在浏览器中的 GitHub 私钥 |
| `blog-index.ts` | 博客索引管理。读取/更新/删除 `public/blogs/index.json` 中的文章索引 |
| `markdown-renderer.ts` | ⭐ Markdown 渲染引擎。将 Markdown 文本转换为 HTML，支持代码高亮（Shiki）、数学公式（KaTeX）、目录生成 |
| `file-utils.ts` | 文件处理工具（读取文件、Base64 转换、SHA-256 哈希） |
| `utils.ts` | 通用工具函数（如 `cn()` 合并 CSS 类名） |
| `color.ts` | 颜色处理工具（生成随机颜色、颜色转换） |
| `load-blog.ts` | 加载单篇博客文章详情 |
| `log.ts` | 日志工具 |

---

## 八、src/hooks/ — 自定义 Hook

| 文件 | 作用 |
|------|------|
| `use-auth.ts` | ⭐ 认证状态管理（Zustand store）。管理是否已认证、私钥状态 |
| `use-blog-index.ts` | ⭐ 博客索引数据获取（使用 SWR 从 `/blogs/index.json` 加载文章列表） |
| `use-categories.ts` | 博客分类数据获取 |
| `use-read-articles.ts` | 已读文章记录（存在 localStorage） |
| `use-center.ts` | 计算页面中心坐标（用于定位元素） |
| `use-size.ts` | 响应式屏幕尺寸检测（判断手机/桌面） |
| `use-markdown-render.tsx` | Markdown 渲染 Hook |

---

## 九、src/config/ — 配置文件

| 文件 | 作用 |
|------|------|
| `site-content.json` | ⭐ 网站全局配置——标题、描述、主题颜色、背景图、社交链接、备案号等 |
| `card-styles.json` | 首页卡片样式配置——各卡片位置、大小、是否显示 |
| `card-styles-default.json` | 卡片样式的默认值备份 |

---

## 十、src/styles/ — 样式文件

| 文件 | 作用 |
|------|------|
| `globals.css` | 全局 CSS（Tailwind 指令 + 自定义全局样式） |
| `theme.css` | 主题相关的 CSS 变量定义 |
| `article.css` | 文章内容页面的专属样式（排版、字体等） |

---

## 十一、工作原理解析

### 这个博客是怎么运作的？

1. **没有后端服务器**——所有数据都存在 GitHub 仓库里。
2. **GitHub 就是数据库**——文章内容、图片、配置全部以文件形式存储在仓库中。
3. **通过 GitHub App 认证**——用户在网页上导入 `.pem` 私钥文件，前端用这个私钥签发 JWT，然后调用 GitHub API 来读写仓库文件。
4. **在线编辑 = 提交代码到 GitHub**——当你通过网页修改文章时，实际是调用 GitHub API 新建了一个 commit。

### 部署流程

```
你的电脑                          GitHub 仓库                   Cloudflare/Vercel
   |                                |                              |
   |-- git push 代码 --------------->|                              |
   |                                |-- 触发自动部署 -------------->|
   |                                |                              |-- 构建网站
   |                                |                              |-- 上线 🎉
```

### 编辑文章流程

```
网页上的编辑器
   |
   |-- 你编辑 Markdown 内容
   |-- 点击"保存"
   |-- 前端用你的私钥认证
   |-- 调用 GitHub API，把内容写入仓库文件
   |-- GitHub 触发自动部署
   |-- 等待几分钟，刷新就看到新内容 ✅
```

---

## 十二、给新手的自定义指南

### 你要改的东西在哪？

| 想改什么 | 去哪里改 |
|----------|----------|
| 网站标题、用户名 | `src/config/site-content.json` |
| 主题颜色 | `src/config/site-content.json` 的 `theme`部分 或 首页配置弹窗 |
| 头像 | 替换 `public/images/avatar.png` |
| 浏览器标签页图标 | 替换 `public/favicon.png` |
| 首页欢迎文字 | `src/app/(home)/hi-card.tsx` |
| 社交链接 | `src/config/site-content.json` 的 `socialButtons` 部分 或 首页配置弹窗 |
| 导航栏菜单项 | `src/components/nav-card.tsx` 的 `list` 数组 |
| 项目列表 | `src/app/projects/list.json` |
| 备案号 | `src/config/site-content.json` 的 `beian` 部分 |
| 首页卡片显隐/位置 | `src/config/card-styles.json` |
| ICP 备案号 | `src/config/site-content.json` 的 `beian` 部分 |
| 全局 CSS 样式 | `src/styles/globals.css` |
| 文章排版样式 | `src/styles/article.css` |
| GitHub 仓库配置 | `src/consts.ts`（OWNER、REPO、APP_ID 等） |

### 重要提醒

- **密钥安全**：`.pem` 私钥文件不要上传到公开仓库，否则任何人都能操作你的仓库。
- **部署延迟**：通过网页编辑后，需要等待 GitHub 自动部署完成（通常 1-2 分钟），刷新才能看到更新。
- **图片建议**：上传图片前先压缩，宽度不超过 1200px。
- **首页布局**：按 `Ctrl+L`（Mac 按 `Cmd+L`）可以打开配置弹窗。

---

> 这份文档是 2026-08-02 基于项目当前代码结构整理的。项目如有更新，以实际代码为准。
> 真有耐心啊，居然看到这里了，那我也给你介绍一下原来的作者好了，原开源作者链接为https://www.yysuni.com/blog/readme
> 想要部署的可以移步这个链接哦[点击查看原作者的说明文档](./README-before.md)
