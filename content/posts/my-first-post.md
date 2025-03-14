+++
date = '2025-03-13T06:33:29Z'
draft = false
title = '第一篇笔记：Hugo & Gitbub Pages 搭建个人静态笔记站点'
tags = ['markdown', 'Hugo']
categories = ['markdown']
+++

## 介绍

用 Hugo 写文章放到 github 进行托管并用 github pages 进行在线展示。

## 配置 GitHub Actions/Pages

### GitHub Actions 配置

在文档管理的仓库里面增加一个文件 `.github/workflows/deploy.yml`，内容如下：
```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.145.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
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

或者直接在 GitHub Actions 里面重新添加一个 workflow，选择 hugo 的模板即可。

在 github Actions 的配置，需要设置一下对应的代码推送权限，主要是为了实现当笔记文件有更新的时候使用 github actions 流水线自动部署最新的 github pages 静态页面的内容。

1. 找到笔记代码仓库的 **Settings** > **Actions** > **General** > **Workflow permissions**
2. 选择 **Read and write permissions** 选择项
3. 并选择 **Allow GitHub Actions to create and approve pull requests**

### GitHub Pages 配置

1. 找到笔记代码仓库的 **Setting** > **Pages** > **Build and deployment**
2. 将 **Build and deployment** 选项下的 **Source** 选择为 **GitHub Actions**

## Hugo 使用

请参考官方文档的 [quick start](https://gohugo.io/getting-started/quick-start/)

以下罗列几个常用的命令：
```bash
# 1. 创建一个 hugo 的站点
hugo new site quickstart

# 2. 下载一个主题：使用 git

# 3. 启动本地服务预览站点
hugo server

# 4. 添加一个文档页面
hugo new content content/posts/my-first-post.md

# 5. 发布站点
hugo
```

### 站点配置

参考链接 [Configure the site](https://gohugo.io/getting-started/quick-start/#configure-the-site)


## 参考链接

1. [Hugo 官方主页](https://gohugo.io/)
2. [smigle 主题 demo](https://smigle-hugo-theme.netlify.app/)
3. [smigle 主题页](https://themes.gohugo.io/themes/smigle-hugo-theme/)
4. [toml](https://toml.io/cn/v1.0.0): hugo 使用的配置方式