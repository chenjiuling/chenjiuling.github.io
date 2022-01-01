---
title: "如何通过hugo和github快速创建个人博客"
date: 2022-01-01T14:11:54+08:00
draft: true
---

### 安装Hugo

#### 1. 二进制安装（推荐：简单、快速）

到 [Hugo Releases](https://github.com/spf13/hugo/releases) 下载对应的操作系统版本的Hugo二进制文件（hugo或者hugo.exe）

Mac下直接使用 `Homebrew` 安装：

```bash
brew install hugo
```

<!--more-->

#### 2. 源码安装

源码编译安装，首先安装好依赖的工具：

- [Git](http://git-scm.com/)
- [Mercurial](http://mercurial.selenic.com/)
- [Go](http://golang.org/) 1.3+ (Go 1.4+ on Windows)

设置好 `GOPATH` 环境变量，获取源码并编译：

```bash
$ export GOPATH=$HOME/go
$ go get -v github.com/spf13/hugo
```

源码会下载到 `$GOPATH/src` 目录，二进制在 `$GOPATH/bin/`

如果需要更新所有Hugo的依赖库，增加 `-u` 参数：

```bash
$ go get -u -v github.com/spf13/hugo
```

### 生成站点

使用Hugo快速生成站点，比如希望生成到 `/path/to/site` 路径：

```bash
$ hugo new site /path/to/site
```

这样就在 `/path/to/site` 目录里生成了初始站点，进去目录：

```bash
$ cd /path/to/site
```

站点目录结构：

```
  ▸ archetypes/
  ▸ content/
  ▸ layouts/
  ▸ static/
    config.toml
```

### 创建文章

创建一个 `about` 页面：

```bash
$ hugo new about.md
```

`about.md` 自动生成到了 `content/about.md` ，打开 `about.md` 看下：

```
+++
date = "2015-10-25T08:36:54-07:00"
draft = true
title = "about"

+++

正文内容
```

内容是 `Markdown` 格式的，`+++` 之间的内容是 [TOML](https://github.com/toml-lang/toml) 格式的，根据你的喜好，你可以换成 [YAML](http://www.yaml.org/) 格式（使用 `---` 标记）或者 [JSON](http://www.json.org/) 格式。

创建第一篇文章，放到 `post` 目录，方便之后生成聚合页面。

```bash
$ hugo new post/first.md
```

打开编辑 `post/first.md` ：

```
---
date: "2015-10-25T08:36:54-07:00"
title: "first"
 
---

### Hello Hugo

 1. aaa
 1. bbb
 1. ccc
```

### 安装皮肤

到 [皮肤列表](https://www.gohugo.org/theme/) 挑选一个心仪的皮肤，比如你觉得 `Hyde` 皮肤不错，找到相关的 `GitHub` 地址，创建目录 `themes`，在 `themes` 目录里把皮肤 `git clone` 下来：

```bash
# 创建 themes 目录
$ cd themes
$ git clone https://github.com/spf13/hyde.git
```

### 运行Hugo

在你的站点根目录执行 `Hugo` 命令进行调试：

```bash
$ hugo server --theme=hyde --buildDrafts
```

（注明：v0.15 版本之后，不再需要使用 `--watch` 参数了）

浏览器里打开： `http://localhost:1313`

### 部署

Create a file in `.github/workflows/gh-pages.yml` containing the following content (based on [actions-hugo](https://github.com/marketplace/actions/hugo-setup)):

```yml
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

For more advanced settings [actions-hugo](https://github.com/marketplace/actions/hugo-setup) and [actions-gh-pages](https://github.com/marketplace/actions/github-pages-action).
