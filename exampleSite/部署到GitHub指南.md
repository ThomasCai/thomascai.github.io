# 🚀 部署到 GitHub Pages 指南

## 前提条件

1. 已有 GitHub 账号
2. 已创建 `caitianren.github.io` 仓库（或其他 `<username>.github.io` 仓库）
3. 已安装 Git 和 Hugo

## 📋 部署步骤

### 方法一：使用 GitHub Actions 自动部署（推荐）

#### 1. 准备仓库结构

```bash
# 在项目根目录（thomascai.github.io）
# 确保你的仓库结构如下：
# .
# ├── exampleSite/          # 你的网站源码
# ├── layouts/              # 主题布局文件
# ├── static/               # 主题静态文件
# └── ...其他主题文件
```

#### 2. 创建 GitHub Actions 工作流

创建文件 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches:
      - main  # 或 master，根据你的主分支名称

  # 允许手动触发
  workflow_dispatch:

# 设置 GITHUB_TOKEN 的权限
permissions:
  contents: read
  pages: write
  id-token: write

# 只允许一个并发部署
concurrency:
  group: "pages"
  cancel-in-progress: false

# 默认使用 bash
defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.151.2'
          extended: true

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install dependencies
        run: |
          cd exampleSite
          npm install

      - name: Build
        run: |
          cd exampleSite
          hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./exampleSite/public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

#### 3. 配置 GitHub Pages

1. 进入你的 GitHub 仓库
2. 点击 `Settings` > `Pages`
3. 在 `Build and deployment` 部分：
   - Source: 选择 `GitHub Actions`
4. 保存设置

#### 4. 推送代码

```bash
# 提交所有更改
git add .
git commit -m "Initial commit: Setup personal portfolio"
git push origin main
```

GitHub Actions 会自动构建并部署你的网站！

---

### 方法二：手动部署

#### 1. 本地构建

```bash
cd exampleSite
hugo --minify
```

这会在 `exampleSite/public/` 目录生成静态网站。

#### 2. 推送到 GitHub

```bash
# 方案 A: 使用子模块（推荐）
cd public
git init
git add .
git commit -m "Deploy website"
git remote add origin git@github.com:caitianren/caitianren.github.io.git
git push -u origin main --force

# 方案 B: 直接推送 public 目录内容到仓库根目录
# （需要创建专门的部署分支）
```

---

## 🔧 部署前检查清单

- [ ] 已替换所有个人图片（参考 `图片替换指南.md`）
- [ ] 已更新 `hugo.toml` 中的 baseURL 为你的 GitHub Pages 地址
- [ ] 已更新社交媒体链接
- [ ] 已删除或替换示例内容
- [ ] 已测试本地构建：`cd exampleSite && hugo server`
- [ ] 确认网站在本地正常显示

## 📝 更新网站内容

每次更新内容后：

```bash
# 1. 修改 exampleSite/data/content.yml 或其他文件
# 2. 本地预览
cd exampleSite
npm start  # 或 hugo server

# 3. 确认无误后提交
git add .
git commit -m "Update: 描述你的修改"
git push

# GitHub Actions 会自动部署更新
```

## 🌐 访问你的网站

部署成功后，访问：
- **https://caitianren.github.io/**

首次部署可能需要 3-5 分钟才能生效。

## 🐛 常见问题

### 1. 网站显示 404
- 检查 `baseURL` 是否正确设置
- 确认 GitHub Pages 已启用
- 等待几分钟让 DNS 生效

### 2. 样式/图片无法加载
- 确认 `baseURL` 以 `/` 结尾
- 检查图片路径是否正确
- 清除浏览器缓存

### 3. 部署失败
- 查看 GitHub Actions 日志
- 确认 Hugo 版本兼容
- 检查 `exampleSite/themes/port-hugo` 符号链接是否正确

## 💡 优化建议

1. **启用 HTTPS**（GitHub Pages 默认支持）
2. **自定义域名**：在仓库设置中添加
3. **压缩图片**：减小加载时间
4. **SEO 优化**：完善 meta 标签
5. **添加 Google Analytics**：在 `hugo.toml` 中配置

## 📚 相关资源

- [Hugo 官方文档](https://gohugo.io/documentation/)
- [GitHub Pages 文档](https://docs.github.com/en/pages)
- [Port Hugo 主题](https://github.com/tylersayshi/port-hugo)

---

需要帮助？随时联系！🚀
