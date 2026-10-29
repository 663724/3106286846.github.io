# 0xWP · CTF 题解博客

一个跑在 GitHub Pages 上的静态博客，只干一件事：**放 Writeup**。

基于 Jekyll（GitHub Pages 原生支持），不需要本地装 Ruby、不需要编译，传上去它自己建。

## 目录

```
_config.yml          站点配置（标题、作者、网址）——先改这里
_layouts/            页面模板，一般不用动
_posts/              文章，一天一篇 md，文件名必须是 YYYY-MM-DD-slug.md
index.html           首页（自动列出 _posts 里的文章）
tags.html            标签页（自动汇总）
about.md             关于页
assets/css/style.css 样式
templates/           写新 WP 用的模板（不参与构建）
```

## 本地怎么写

1. 复制 `templates/wp-template.md`
2. 改名成 `_posts/2026-10-30-web-sqli-1.md`（**日期和 slug 用英文/数字**）
3. 改 front matter：`title`、`date`、`categories`、`tags`、`description`
4. 正文照着模板填
5. 把文件传到 GitHub（见下）

> 文件名日期 = 文章日期；slug 只用来拼 URL，中文标题也可以，但英文更稳。

## 发布到 GitHub Pages（三步）

### 1. 建仓库

打开 <https://github.com/new>，Repository name 填：

- 想用 `https://你的用户名.github.io` 直接访问 → 仓库名必须是 **`你的用户名.github.io`**（例如 `alice.github.io`）
- 无所谓，想叫 `ctf-wp` → 仓库名随意，网址会变成 `https://你的用户名.github.io/ctf-wp/`

选 **Public**（Pages 免费版要求公开仓库），不要勾 README。

### 2. 上传文件

**方式 A：网页上传（不用装 git）**

- 进仓库 → `Add file` → `Upload files`
- 把 `D:\ctf-blog` 里的**所有文件和文件夹**拖进去（`_posts`、`_layouts`、`assets` 这些文件夹要一起拖，网页会保留目录结构）
- 提交

**方式 B：装了 GitHub Desktop 的话**，把 `D:\ctf-blog` 添加成仓库，直接 Publish。

### 3. 开 Pages

仓库 → `Settings` → 左侧 `Pages`：

- Source 选 **Deploy from a branch**
- Branch 选 `main`，目录选 `/ (root)`
- Save

等 1~2 分钟，页面顶部会显示你的网址。以后每次上传新文章，等半分钟自动更新。

## 两处必须改的地方

打开 `_config.yml`：

```yaml
url: "https://YOURNAME.github.io"   # 换成你的用户名
baseurl: ""                          # 仓库名不是 <用户名>.github.io 时改成 "/仓库名"
```

`about.md` 里的 `YOUR_NAME` / `YOUR_CONTACT` 也换掉。

## 常见问题

**打开是 404**：Pages 还没构建完，等两分钟；或者仓库不是 Public。

**样式没了 / 链接 404**：`baseurl` 没配对——项目仓库（`ctf-wp` 这种）要写 `baseurl: "/ctf-wp"`。

**新文章不出现**：文件名是不是 `YYYY-MM-DD-xxx.md`？日期格式不对 Jekyll 会直接忽略。

**中文标签页跳转怪**：标签用了 `slugify`，中文会变成编码，能用但不美观；想好看就把标签写成英文（`web`、`crypto`）。

## 写 WP 的底线

- Flag 打码，别直接贴完整 flag
- 写失败过的尝试，证明是你自己做的
- 别用 AI 生成整篇——公告里专门写了「请独立完成题目，不要使用 AI 刷分」
