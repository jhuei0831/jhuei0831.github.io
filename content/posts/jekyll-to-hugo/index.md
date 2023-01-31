---
title: "Use Hugo build website"
categories: ['CODE']
description: 使用Hugo建立靜態網站
tags: ['hugo', 'jekyll', 'github', 'chocolatey', 'DoIt']
authors: ["Kerwin"]
featuredImagePreview: feature.jpg
date: 2023-01-31T19:51:30+08:00
lastmod: 2023-01-31T19:51:30+08:00
---

> 使用Hugo建立靜態網站

<!--more-->

## 前言

Hello，大家🤞

之前的部落格是使用[jekyll](https://jekyllrb.com/)，最近想改成用[Hugo](https://gohugo.io/)，Host一樣是使用[Github Page](https://pages.github.com/)。

---

## 安裝Hugo

**介紹Windows及macOS安裝方法**

### Windows

我是使用[Chocolatey](https://chocolatey.org/)這個套件管理程式安裝，安裝Chocolatey後在終端機輸入:

```bash
choco install hugo-extended
```

安裝完成後驗證一下:

```bash
hugo version
```

### macOS

使用Homebrew安裝:

```bash
brew install hugo
```

安裝完成後驗證一下:

```bash
hugo version
```

---

## 快速建立Hugo網站

建立Hugo專案，並進入專案資料夾

```bash
hugo new site my_website
cd my_website
```

加入網站主題(theme)，透過submodule方式

```bash
git init
git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt
```

修改網站設定

```Toml
baseURL = "http://example.org/"
# 設定默認語言
defaultContentLanguage = "en"
# 語言
languageCode = "en"
title = "My New Hugo Site"

# 把預設的主題替換成DoIt
theme = "DoIt"

# 主題參數
[params]
  # DoIt主題版本
  version = "0.2.X"

# Nav右側選單
[menu]
  [[menu.main]]
    identifier = "posts"
    # 您可以在名稱前加入額外信息（支援HTML格式），例如icons
    pre = ""
    # 您可以在名稱後加入額外信息（支援HTML格式），例如icons
    post = ""
    name = "Posts"
    url = "/posts/"
    # title在滑鼠懸停時會顯示
    title = ""
    weight = 1
  [[menu.main]]
    identifier = "tags"
    pre = ""
    post = ""
    name = "Tags"
    url = "/tags/"
    title = ""
    weight = 2
  [[menu.main]]
    identifier = "categories"
    pre = ""
    post = ""
    name = "Categories"
    url = "/categories/"
    title = ""
    weight = 3

# Markup related configuration in Hugo
[markup]
  # Syntax Highlighting (https://gohugo.io/content-management/syntax-highlighting)
  [markup.highlight]
    # false is a necessary configuration (https://github.com/dillonzq/LoveIt/issues/158)
    noClasses = false
```

修改設定後執行指令:

```bash
hugo server -D
```

{{< admonition type=info title="" open=true >}}
使用`hugo server`，檔案被修改後網站會自動更新；`-D`表示包含Draft的內容
{{< /admonition >}}

打開瀏覽器在網址輸入指令提供的網址，一般為`http://localhost:1313`，看到以下畫面就成功了😆。

{{< image src="hugo.png" caption="Hugo DoIt">}}

---

## 新增文章

在`content/posts`底下新增名稱為`first_post`的文章

```bash
hugo new posts/first_post.md
```

{{< image src="new-post.png" caption="Hugo new post">}}

---

## 結語

叮叮結束，下一篇介紹將Hugo部屬到Github Page👏

--- 

## 參考

* [macOS安裝Hugo](https://gohugo.io/installation/macos/)
* [Windows安裝Hugo](https://gohugo.io/installation/windows/)
* [DoIt主題](https://hugodoit.pages.dev/)