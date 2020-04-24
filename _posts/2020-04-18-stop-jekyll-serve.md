---
title: Stop Jekyll serve (127.0.0.1:4000)
summary: 解決Jekyll serve 無法關閉  - Jekyll serve service worker
layout: post
featured-img: 20200418
categories:
- EXECUTION
tags:
- jekyll
- cookie
---

### 前言
<hr> 
一般來講我們關閉本地的jekyll serve 都是使用 `ctrl + c` ，可是當你下次再次運行 `jekyll serve` 時發現上次的還在 。

### 解決
<hr> 
這時只要把`127.0.0.1`中的 ***Service Workers Cookie***  刪除即可，以GOOGLE瀏覽器為例，目標就是下圖紅框處的Cookie。

{:refdef: align="center" }
![cookie](https://i.imgur.com/hk0qGZQ.png)
{: refdef}

[如果你想了解更多](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle?hl=zh-tw)

Hope it will help!
