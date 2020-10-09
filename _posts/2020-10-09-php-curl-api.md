---
title: PHP cURL API (POST & GET)
layout: post
featured-img: 20201009
categories:
- CODE
tags:
- php
- curl
- api
description: 使用Pusher在Laravel的事件中做出廣播功能
---

### 前言
---
如果要用PHP去接別人的API，可以用cURL的函數去做。

### 本篇重點
---
 * cURL
 * POST
 * GET

### 1. cURL
---
[https://www.php.net/manual/en/book.curl.php](https://www.php.net/manual/en/book.curl.php)

使用cURL支援HTTP Authorization 、POST的特性去接別人的API

### 2. POST
---

首先要看OAS文件去決定要用哪種方式去傳送或接收資料，以下是POST的方法，

```php
//init curl
$ch = curl_init();
//curl_setopt可以設定curl參數
//CURLOPT_RETURNTRANSFER為true的話，curl只會將結果傳回，並不會輸出在畫面上
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
//設定url
curl_setopt($ch , CURLOPT_URL , "http://www.API.com/API");
//設定header
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-type: application/x-www-form-urlencoded'));
//啟用POST
curl_setopt($ch, CURLOPT_POST, true);
//傳入POST參數
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query( array( "ID"=>"TESTID") ));
//執行，並將結果存回
$result = curl_exec($ch);
//關閉連線
curl_close($ch);
```

可以根據回傳的資料再去做處理。

### 3. GET
---

```php
//init curl
$ch = curl_init();
//curl_setopt可以設定curl參數
//設定url
curl_setopt($ch , CURLOPT_URL , "http://www.API.com/API?ID=TESTID");
//設定AGENT
curl_setopt($ch, CURLOPT_USERAGENT, "API Bot");
//執行，並將結果存回
$result = curl_exec($ch);
//關閉連線
curl_close($ch);
```
