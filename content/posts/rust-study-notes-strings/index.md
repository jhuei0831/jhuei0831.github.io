---
title: "Rust學習筆記 - Strings"
categories: ["Rust"]
description: 
tags: ["Rust", "Strings", "&str"]
authors: ["Kerwin"]
featuredImagePreview: feature.jpg
date: "2023-11-17"
lastmod: "2023-11-17"
---

> Rust字串的種類及其應用

<!--more-->

## 前言

Rust字串有不同的型態，這篇文章會介紹常用的字串類型，以及他們的應用。

## 字串類型

### &str

`&str` 是一個指向字串的指標，它是一個不可變的字串，它的值是在編譯時期就已經確定的，所以它的大小是固定的。

* UTF-8編碼
* 可以應用於slice([切片](https://doc.rust-lang.org/book/ch04-03-slices.html?highlight=slice#string-slices))
* 不具備ownership([所有權](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html))

```rust
let s = String::from("hello world");

let hello = &s[0..4];
// hello: hell
println!("hello: {}", hello);
```

{{< admonition type=warning title="" open=true >}}
需要注意使用slice的時候要注意邊界(`boundary`)，否則會出現panic。<br>
比如說定義了一個&str為`叮叮你好`，想使用slice取出`叮叮`，但是如果使用`&hello[0..2]`，會出現panic，因為`叮`的utf-8編碼是3個byte，所以要使用`&hello[0..6]`才是正確的。

```rust
let hello = "叮叮你好";

// panic: byte index 2 is not a char boundary; it is inside '叮' (bytes 0..3) of `叮叮你好`
// let s = &hello[0..2];

// correct
let s = &hello[0..6];
// 叮叮
println!("{}", s);

```
{{< /admonition >}}

### String

`String` 是一個可變的字串，它的值是在執行時期才會確定的，所以它的大小是不固定的。

* UTF-8編碼
* 可以改變字串的值
* 具備ownership([所有權](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html))

以下是建立`String`的幾種方式:

```rust
// 不可變的String
let s = String::from("Hello, world!");
// 可變的String
let mut s = String::from("Hello, world!");
// 使用to_string()方法
let s = "Hello, world!".to_string();
```

## 更新字串

使用`push_str`方法來更新字串:

```rust
let mut s = String::from("Hello, ");
s.push_str("world!");
// Hello, world!
println!("{}", s);
```

使用`push`方法來更新字串: 這個方法只能接受一個字元，如果要接受多個字元，可以使用`push_str`方法。

```rust
let mut s = String::from("你好啊, ");
s.push('旅');
s.push('行');
s.push('者');
s.push('A');
// 你好啊, 旅行者A
println!("{}", s);
```

## 串接字串

使用`+`來串接字串:

```rust
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // 注意 s1 被移動了，不能繼續使用
```

使用`format!`來串接字串:

```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3);
// tic-tac-toe： s1、s2、s3 還可以繼續使用
println!("{}", s);
```

## 迭代字串

使用`chars`方法來迭代字串: 這個方法會返回字串的字元。

```rust
let s = String::from("你好啊, 旅行者A");
for c in s.chars() {
    println!("{}", c);
}
```

使用`bytes`方法來迭代字串: 這個方法會返回字串的UTF-8編碼。

```rust
let s = String::from("你好啊, 旅行者A");
for b in s.bytes() {
    println!("{}", b);
}
```

## 字串常用方法

### len

使用`len`方法來取得字串的長度: 這個方法會返回字串的字元數量。

```rust
let s = String::from("你好啊, 旅行者A");
// 21，因為中文字元的UTF-8編碼是3個byte，英文、標點符號跟空白字元是1個byte
println!("{}", s.len());
```

### 反轉字串

```rust
let s = String::from("你好啊, 旅行者A");
// 將字串迭代後反轉，並使用collect方法將迭代收集起來
let rev_s: String = s.chars().rev().collect();
// A者行旅 ,啊好你
println!("{}", rev_s);
```

---

## 參考資料

* [String](https://doc.rust-lang.org/std/string/struct.String.html)
* [Storing UTF-8 Encoded Text with Strings](https://doc.rust-lang.org/book/ch08-02-strings.html#bytes-and-scalar-values-and-grapheme-clusters-oh-my)