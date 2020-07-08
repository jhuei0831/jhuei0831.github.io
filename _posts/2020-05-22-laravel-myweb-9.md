---
title: Laravel Backend Control Website - 9
layout: post
featured-img: 20200522
categories:
- CODE
tags:
- php
- html
- laravel
- javascript
- request
- XSS
- htmlpurifier
description: Laravel XSS Defense by use Htmlpurifier
---

### 前言
---
 
在 [第八篇](https://jhuei.com/code/2020/05/10/laravel-myweb-8.html) 中的結尾提到為什麼要把儲存寫的比較複雜，這是因為要防止 [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting) 的攻擊。
 
這篇就講如何在 `Controller` 中防禦 `XSS` 。
 
### 本篇重點
---
 
* [XSS](#xss)
* [Htmlpurifier](#htmlpurifier)
* [結語](#end)

{: id='xss'}
### 1. XSS (Cross-site scripting)
---

{: .notice--warning aligin="center"}
***「知己知彼，百戰百勝」***

`XSS` 就是利用 `Javascript` 等語言方式在網站注入指令，進而使網站無法運作或者竊取機敏資料。

在 `Laravel` 中雖然內建 `{% raw %}{{ $data }}{% endraw %}` 的方式達成 `htmlspecialchars()` ， 但這是在輸出的部分。

如果再資料被寫入的時候就提早防禦，是不是更好呢?


{: id='htmlpurifier'}
### 2. Htmlpurifier
---

這次就介紹一個套件可以在輸入或輸出的時候先將資料進行過濾，那就是 [HtmlPurifier](https://github.com/mewebstudio/Purifier)。

一款可以自訂參數並支援 `Laravel` 的過濾 `XSS` 套件。

安裝 :

```command
composer require mews/purifier
```

接著由於我們使用的是`Laravel 5.8` 的版本，所以執行以下指令 :

```command
php artisan vendor:publish --provider="Mews\Purifier\PurifierServiceProvider"
```

即可生成 `config/purifier.php` 檔案，在這個檔案內設定調整要過濾的選項。

最後只要使用 `clean()` 或者 `Purifier::clean()` 的方式就可以將你討厭的東西過濾掉了。

以新增會員的 `store()` 作為範例 :

```php
public function store(Request $request)
{
    if (Auth::check() && Auth::user()->permission < '5') {
        return back()->with('warning', '權限不足以訪問該頁面 !');
    }
    $error = 0;
    $user = new User;

    $data = $request->validate([
        'name' => ['required', 'string', 'max:255'],
        'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
        'password' => ['required', 'string', 'min:1', 'confirmed'],
        'permission' => ['required', 'string', 'max:5', 'min:0'],
    ]);
    
    foreach ($request->except('_token','_method','password_confirmation') as $key => $value) {
        if ($request->filled($key) && $key == 'password') {
            $user->password = Hash::make($data['password']);
        }
        elseif ($request->filled($key)) {
            $user->$key = strip_tags(clean($data[$key]));
            if ($user->$key == '') {
                $error += 1;
            }
        }
    } 

    if ($error == 0) {
        $user->save();
    }
    else{
        return back()->withInput()->with('warning', '請確認輸入 !');
    }

    return back()->with('success','會員新增成功 !');
}
```

* 16行將不要的輸入排除。
* 18行將 `Password` 加密處理。
* 21行將其餘輸入逐一進行過濾，加上 `strip_tags` 是因為 `clean()` 之後會留下 `<p></p>` ，所以將之排除。
* 22行判斷如果過濾後為空值，則錯誤。


{: id='end'}
### 結語
---

請注意不要在頁面管理的 `content` 使用 `strip_tags`，會很可怕。

在所有的`Controller` 中都加入 `Htmlpurifier` 吧 !

下一篇將介紹抓戰犯的工具 `LOG` 🤪
