# 从零搭建一个 GitHub Pages 静态站点（完整指南）

> 本文以一个实际案例讲解：如何用免费的 GitHub Pages 亲手搭一个**静态网站**。
> 它托管的是"课件/培训资料"这类内容，但原理适用于任何静态小站（个人主页、产品页、文档站……）。
> 看完你会明白：**Git 仓库 = 网站，一次 push = 一次发布。**

---

## 0. 核心思想（先记住这一句）

> GitHub Pages 把**一个 git 仓库当成一个网站**来托管。

- 仓库里的**文件**就是网站的文件（HTML / CSS / PDF / 图片……）。
- **文件路径就是网址路径**（`/css/a.css` 对应磁盘上 `css/a.css`）。
- 服务器只做一件事：**把文件按路径原样发给访问者**。没有后端、没有数据库、没有动态计算。
- 你从零要做的：**准备好文件、搭对目录、写对链接**；GitHub 负责域名、HTTPS 证书、全球 CDN、7×24 在线。

---

## 1. 准备（一次性）

### 1.1 一个 GitHub 账号
- 打开 https://github.com 注册（免费的即可）。
- 记下你的**用户名**（本文示例用 `MOUBQ`，请替换成你自己的）。

### 1.2 本地安装 Git
- 下载 https://git-scm.com ，一路默认安装。
- 装好后**配置身份**（git 才能知道提交者是谁）：

```bash
git config --global user.name  "你的名字"
git config --global user.email "你的邮箱"
```

> 可选：`gh` 命令行（`winget install GitHub.cli`），它能用命令**创建仓库**，免去网页操作。但**没有它也能做**，普通流程走网页建仓即可。

---

## 2. 站点长什么样（目录结构）

静态站没有"入口注册路由"，**网址就是文件路径**。一个最小可运行的站点长这样：

```
my-site/
├── index.html          # 首页（站点欢迎页，必须放在根目录）
├── css/
│   └── style.css       # 样式表
└── pages/
    └── page1.html      # 二级页面
```

| 访问网址 | 对应文件 |
|---|---|
| `https://<用户名>.github.io/my-site/` | `index.html` |
| `https://<用户名>.github.io/my-site/css/style.css` | `css/style.css` |
| `https://<用户名>.github.io/my-site/pages/page1.html` | `pages/page1.html` |

**加新页面 = 新建一个 `.html` 文件 + 在别处用一个 `<a href="...">` 指向它。**

---

## 3. 网页文件怎么写

### 3.1 一个最基本的 index.html

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>我的站点</title>
  <link rel="stylesheet" href="css/style.css" />
</head>
<body>
  <h1>欢迎来到我的站点</h1>
  <p><a href="pages/page1.html">进入子页面</a></p>
</body>
</html>
```

### 3.2 ⚠️ 头号大坑：必须用「相对路径」

因为站点部署在 `https://<用户名>.github.io/<仓库名>/` 这个**子路径**下，URL 是分层的：

- 首页在根目录，引用样式写 **`css/style.css`**（相对于当前目录）。
- 在 `pages/` 里往上一级引样式，写 **`../css/style.css`**。

**千万别写 `/css/style.css`（斜杠开头的绝对路径）**——它代表域名根目录 `https://…github.io/css/`，而你的文件在 `…/my-site/css/`，**必定 404**。这是"本地能跑、上线就挂"的头号原因。

> 经验法则：**链接永远相对于"当前文件所在目录"写；跨上一级就用 `../`。**

### 3.3 例子：用 `文件+链接` 实现课件主页

```
course-site/
├── index.html              # 首页：卡片列出 2 门课
├── css/style.css
├── courses/
│   ├── course-01.html      # 每门课一页：在线看 + 下载
│   └── course-02.html
├── pdf/                    # 课件 PDF（在线翻页用）
└── files/                  # 原始 .pptx（下载用）
```

首页里每门课一个卡片，两个按钮：

```html
<a class="btn" href="courses/course-01.html">在线浏览</a>
<a class="btn" href="files/course-01.pptx" download>下载课件</a>
```

### 3.4 "在线看 PPT" 的取巧做法（不用任何框架）

把 `.pptx` 转成 `.pdf`，然后用浏览器**原生 PDF 查看器**内嵌——一行代码即可翻页：

```html
<iframe src="../pdf/course-01.pdf" loading="lazy"></iframe>
```

下载原文件就用 `download` 属性，浏览器自动下载，零后端：

```html
<a href="files/course-01.pptx" download>下载原始课件</a>
```

> 想更炫（真翻页动画、主题）才需要 reveal.js / Slidev 等把 PPT 重写成 HTML 幻灯片页，那是进阶做法。

### 3.5 样式（css/style.css）随便怎么写

纯 CSS 即可，一个最小样式起点：

```css
body { font-family: "Microsoft YaHei", sans-serif; margin: 0; line-height: 1.6; }
.btn { display: inline-block; padding: 10px 16px; border-radius: 8px; background: #4f46e5; color: #fff; text-decoration: none; }
```

---

## 4. 上线三步（核心）

### 第 1 步：在 GitHub 上建仓库（网页操作）

1. 登录 GitHub，右上角 **+** → **New repository**。
2. 填 **Repository name**（本文示例 `course-site`）。
3. **Visibility 选 Public** ← 必须公开，免费账户才有 Pages。
4. ⚠️ **三个初始化勾都别选**（不要 README / .gitignore / license），否则首次推送会冲突。
5. 点 **Create repository**，得到一个空仓库。

> 建好后你需要这个地址：`https://github.com/<你的用户名>/<仓库名>.git`

### 第 2 步：本地提交 + 推送（命令行，二选一认证）

进入站点根目录（把 `<你的用户名>` 替换成你的）：

```bash
cd my-site

git init
git add .
git commit -m "我的站点"
git branch -M main
git remote add origin https://github.com/<你的用户名>/my-site.git
git push -u origin main
```

**推送认证（重要）**：GitHub 已**禁止用账号密码**推送，必须用「令牌（PAT）」或「SSH 密钥」。

**方式 A：个人访问令牌（PAT，推荐，直连 HTTPS）**
1. 头像 → **Settings** → 底部 **Developer settings** → **Personal access tokens** → **Tokens (classic)** → **Generate new token**。
2. 勾选 **`repo`** 权限，生成。
3. 推送时：用户名填你的 GitHub 用户名，**密码框粘贴那串 `ghp_…` 令牌**。Windows 的凭据管理器会自动记住。

**方式 B：SSH 密钥**
1. 本地生成：`ssh-keygen -t ed25519 -C "你的邮箱"`，一路回车。
2. 复制公钥：`cat ~/.ssh/id_ed25519.pub`。
3. 头像 → **Settings** → **SSH and GPG keys** → **New SSH key**，粘贴进去。
4. 推送地址改成：`git remote add origin git@github.com:<你的用户名>/my-site.git`

### 第 3 步：开启 GitHub Pages

1. 仓库页面 → **Settings** → 左侧 **Pages**。
2. 找 **Build and deployment** → **Source** 下拉框（**默认显示 GitHub Actions**）→ 点开改成 **Deploy from a branch**。
3. 出现 **Branch** → 选 `main`；**Folder** 选 `/(root)` → **Save**。
4. 等 **1–3 分钟**，顶部出现网址：

```
https://<你的用户名>.github.io/<仓库名>/
```

打开即上线，全球可访问。

---

## 5. 内容与资源限制（要知道）

| 限制 | 说明 |
|---|---|
| 单文件 ≤ **100 MB** | 超了推不上去 |
| 仓库总量建议 ≤ 几 GB | 太大请瘦身或用其他托管 |
| 不适合放大视频 | 视频放网盘，页面只留链接 |
| 免费账户 Pages 需 **Public** 仓库 | 私有仓库的 Pages 需付费（Pro/Team） |
| 无后端 | 不能做登录、表单存数、后台管理；要交互只能写浏览器端 JS |

---

## 6. 常见报错速查表

| 报错 / 现象 | 原因与解决方法 |
|---|---|
| `remote origin already exists` | `git remote add` 重复执行。改成:先 `git remote -v` 看是否已设对，对就直接 `git push`；地址错了用 `git remote set-url origin <正确地址>` |
| `remote: Repository not found.` | 仓库不存在 / 用户名或仓库名打错 / 仓库是私有。核对 `https://github.com/<用户名>/<仓库名>` 浏览器能否打开，名字大小写要一致；私有则改 Public |
| `Support for password authentication was removed` | 你用了账号密码。改用 **PAT 令牌**或 **SSH** |
| `failed to push some refs` | 建仓库时勾了 README 导致冲突。先 `git pull origin main --rebase`，或删掉仓库重新建一个"空"的 |
| `error: …exceeds GitHub's file size limit (100MB)` | 单个文件太大，压缩后再推 |
| `Permission to … denied` | 令牌没勾 `repo` 权限，或你不是仓库所有者；重新生成令牌并勾满 `repo` |
| push 卡住没反应 | 公司网络到 github.com 受限。换网络（手机热点）或让 IT 放行 |
| 页面 404 / 样式丢失 | 八成是**绝对路径 `/xxx` 用错**，改成相对路径 |

---

## 7. 进阶：想更好看 / 更大站点时

- **静态站点生成器**：内容多了（几十个页面）再上。GitHub 原生支持 **Jekyll**（改一下分支源即可，还能自动把 Markdown 渲染成页面）。其他选择：MkDocs（文档站）、Astro / Eleventy / Hugo。
- **漂亮主题**：套一个 UI 模板即可；纯手写 HTML/CSS 也完全够用（本文的案例就是纯手写，清晰、零依赖）。
- **在线 PPT 增强**：换成 reveal.js / Slidev，把课件做成真正的翻页幻灯片。

---

## 8. 最小自查清单（从头做一遍对照）

- [ ] GitHub 仓库为 **Public**，建时**没勾** README
- [ ] `index.html` 放在仓库**根目录**
- [ ] 所有链接用**相对路径**（`css/…`、`../css/…`），**没有**以 `/` 开头的
- [ ] 在线看 PPT：`.pptx → .pdf` 后用 `<iframe>` 内嵌；下载用 `download` 属性
- [ ] 推送用 **PAT 或 SSH**，不是账号密码
- [ ] Settings → Pages → **Deploy from a branch** → `main` / `/(root)`
- [ ] 等 1–3 分钟，打开 `https://<用户名>.github.io/<仓库名>/` 确认真能访问

---

**一句话总结**：你编的不是"一个程序"，而是**一组用相对路径连起来的静态文件**；一个 git 分支就是网站，push 就是上线。抓住这两点，GitHub Pages 你就入门了。
