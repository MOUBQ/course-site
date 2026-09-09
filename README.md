# 领导力系列课程站点

本项目是一个静态 GitHub Pages 站点，用于在线浏览并下载两门课程的课件。

- **课程 01 · 韧性领导力**
- **课程 02 · 非权力领导力**

## 目录结构

```
course-site/
├── index.html              # 首页（课程卡片入口）
├── css/style.css           # 样式
├── courses/
│   ├── course-01.html      # 韧性领导力 课件页
│   └── course-02.html      # 非权力领导力 课件页
├── pdf/                    # ← 把每门课的课件 PDF 放到这里
│   ├── course-01.pdf
│   └── course-02.pdf
└── files/                  # ← 把原始 .pptx 课件放到这里
    ├── course-01.pptx
    └── course-02.pptx
```

## 使用说明

1. **准备课件 PDF**：用 PowerPoint 打开每个 `.pptx`，另存为 PDF，按上面文件名放入 `pdf/`。
2. **准备原始课件**：把 `.pptx` 放入 `files/`（保持 `course-01` / `course-02` 文件名）。
3. 站点页面即会自动内嵌 PDF 在线阅读，并提供原课件下载。

## 部署到 GitHub Pages

1. 新建一个 GitHub **public** 仓库（例如 `course-site`）。
2. 在 `course-site/` 目录里执行：

   ```bash
   git init
   git add .
   git commit -m "领导力系列课程站点"
   git remote add origin https://github.com/<你的用户名>/course-site.git
   git branch -M main
   git push -u origin main
   ```

3. 打开仓库 **Settings → Pages**，Source 选择 **Deploy from a branch**，Branch 选 `main`、目录选 `/(root)`，保存。
4. 数分钟后访问：`https://<你的用户名>.github.io/course-site/`
