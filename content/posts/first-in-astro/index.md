---
title: "First in Astro"
categories: ['CODE']
description: 初次使用astro時要注意的事情
tags: ['astro', 'javascript', 'react']
authors: ["Kerwin"]
featuredImagePreview: feature.jpg
date: 2023-07-29T15:38:57+08:00
lastmod: 2023-07-29T15:38:57+08:00
---

> 初次使用astro時要注意的事情
<!--more-->

## 前言

前陣子發現一款靜態網站的框架[ASTRO](https://astro.build/)，接著就嘗試用ASTRO做一個API串接顯示的DEMO，

其中發現為什麼API回傳的資料都沒有顯示，後來才發現問題

---

## 情境

發現API回傳的資料無法顯示於React的指定Component中

## 解決

在Astro中有一個特色叫做[Astro Islands](https://docs.astro.build/en/concepts/islands/)，

他可以把指定的Component變成動態，其餘的部分依舊是靜態，達到效能提升的目的。

所以要使Component為動態，需要加入`client:load`

```js
<!-- 現在這個 component 可以在這頁面上互動了！
     你網站剩下的部分依然是靜態的且零 JavaScript。 -->
<MyReactComponent client:load />
```