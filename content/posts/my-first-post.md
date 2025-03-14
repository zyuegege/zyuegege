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
name: Deploy Hugo Site to GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        with:
          submodules: true

      - name: Set up Hugo
        run: |
          wget https://github.com/gohugoio/hugo/releases/download/v0.145.0/hugo_extended_withdeploy_0.145.0_Linux-64bit.tar.gz
          tar -xvzf hugo_extended_withdeploy_0.145.0_Linux-64bit.tar.gz
          sudo mv hugo /usr/bin/

      - name: Build site
        run: hugo

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

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