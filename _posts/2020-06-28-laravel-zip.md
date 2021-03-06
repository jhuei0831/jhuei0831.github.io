---
title: Laravel Zip
layout: post
featured-img: 20200628
categories:
- CODE
tags:
- php
- html
- laravel
- zip
- ZipArchive
description: 壓縮下載 - Zip Download
---

### 前言
---
將選取的資料夾內的檔案全部加入壓縮檔並且下載。

### 本篇重點
---
* [View](#view)
* [Route](#route)
* [Controller](#controller)
* [Reference](#reference)


{: id='view'}
### 1. View
---
`zip.blade.php` :

```html
<form action="{% raw %}{{ route('zip.download') }}{% endraw %}" method="post">
    @csrf
    <select name="folder">
        <option value="">請選擇</option>
        @foreach ($folders as $folder)
            <option>{% raw %}{{ $folder }}{% endraw %}</option>
        @endforeach
    </select>
    <button type="submit">Submit</button>
</form>
```

{: id='route'}
### 2. Route
---
`web.php` :

```php
Route::get('zip', 'ZipController@index')->name('zip');
Route::post('zip/download', 'ZipController@zip')->name('zip.download');
```

{: id='controller'}
### 3. Controller
---
`ZipController.php` :

```php
use ZipArchive;
use File;

public function index()
{
    $directories = File::directories(public_path().'\storage\your\path');
    $folders = array_map('basename', $directories);

    return view('zip',compact('folders'));
}

public function zip(Request $request)
{
    $zip = new ZipArchive;
    $fileName = $request->folder.'.zip';
    if ($zip->open(public_path($fileName), ZipArchive::CREATE) === TRUE)
    {
        $files = File::files(public_path('storage/your/path/'.$request->folder));
        foreach ($files as $key => $value) {
            $relativeNameInZipFile = basename($value);
            $zip->addFile($value, $relativeNameInZipFile);
        }
        $zip->close();
    }
    return response()->download(public_path($fileName));
}
```

在第6行中使用了 `File::directories()`  獲取一個目錄下的所有目錄，

接著第7行使用 `array_map` 的 `basename` 獲取目錄名稱，並回傳給 `zip.blade.php`。

`zip()` 在 18~21 行中，將選取資料夾內的檔案逐一加到壓縮檔中。

{: id='reference'}
### 4. Reference
---

* [ZipArchive](https://www.php.net/ziparchive)
* [File](https://laravel.com/api/7.x/Illuminate/Filesystem/Filesystem.html)
