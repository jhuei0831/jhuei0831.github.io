---
title: Translate Big Parantheses in Markdown
layout: post
featured-img: 20200422-2
categories:
- EXECUTION
tags:
- jekyll
- liquid
- markdown
description: 如何在markdown中顯示雙大括號 - How to translate double big parantheses in markdown
---

### 前言
<hr> 
由於jekyll 使用liquid 的關係，在 POST 中使用 {% raw %} {{  }} {% endraw %} 會無法正常顯示。

### 解決
<hr> 
```{% assign openTag = '{%' %}  
{{ openTag }} raw %}      
{% raw %}
  {{ 加入raw即可 }}
{% endraw %}
{{ openTag }} endraw %}
```
<div class="notice--info">
之後補充如何顯示 {% raw %} {% raw %} {% endraw %}
</div>

Hope it will help !
