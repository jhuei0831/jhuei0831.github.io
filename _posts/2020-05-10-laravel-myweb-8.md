---
title: Laravel Backend Control Website - 8
layout: post
featured-img: 20200510
categories:
- CODE
tags:
- php
- html
- laravel
- javascript
- css
- bootstrap
- backend
- request
description: 頁面管理 - Page Manager
---

### 前言
---
Hello 大家 ! 在 [第6篇](https://jhuei.com/laravel-myweb-7/) 創建了導覽列管理，而那時候將導覽列分成兩個類型，分別是 `導覽目錄` 以及 `一般頁面` 。

* 一般頁面 : 點擊後直接導向該頁面。
* 導覽目錄 : 點擊後產生下拉式選單，內容為選單。

而這篇要做的就是 `一般頁面` 的頁面管理。

### 本篇重點
---
* [新增Controller、Model、Migration](#cmm)
* [修改Migration](#migration)
* [修改Model](#model)
* [加入路由](#route)
* [建立視圖](#view)
* [頁面管理](#manage)
* [加入編輯器](#editor)
* [頁面新增](#create)
* [頁面修改](#edit)
* [頁面刪除](#delete)
* [結語](#end)

{: id='cmm'}
### 1. 新增Controller、Model、Migration [🔝](#top)
---

```
// 一次性建立
php artisan make:model Page -mcr
```

{: id='migration'}
### 2. 修改Migration [🔝](#top)
---

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreatePagesTable extends Migration
{
    public function up()
    {
        Schema::create('pages', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('menu_id')->nullable()->comment('選單id');
            $table->string('editor')->comment('編輯者');
            $table->string('title')->comment('標題');
            $table->string('url')->comment('頁面網址');
            $table->longText('content')->nullable()->comment('頁面內容');
            $table->boolean('is_open')->default(true)->comment('是否開放');
            $table->boolean('is_slide')->default(true)->comment('是否輪播');
            $table->timestamps();
        });
    }
    public function down()
    {
        Schema::dropIfExists('pages');
    }
}

```
之後會建立選單連結頁面，所以先加入選單ID欄位。

然後寫入資料庫 :

```
php artisan migrate
```

{: id='model'}
### 3. 修改Model [🔝](#top)
---

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Page extends Model
{
    protected $table = 'pages';

    protected $primaryKey = 'id';

    protected $fillable = [
        "name", "menu_id" ,"title", "url", "content", "is_open",
    ];
}

```

{: id='route'}
### 4. 加入路由 [🔝](#top)
---

```php
Route::prefix('manage')->middleware('auth','admin')->group(function(){
    Route::resource('member', 'MemberController');
    Route::resource('navbar', 'NavbarController');
    Route::resource('page', 'PageController');
});

```

{: id='view'}
### 5. 建立視圖 [🔝](#top)
---

```
views
├── _layouts
├── _partials
├── auth
└── manage    # 後台管理
    └── member  # 會員管理
    └── navbar  # 導覽列管理
    └── page    # 頁面管理
        ├── create.blade.php 
        ├── edit.blade.php
        └── index.blade.php
```

{: id='manage'}
### 6. 頁面管理 [🔝](#top)
---

先建立頁面管理首頁 `index.blade.php` :

```php
@extends('_layouts.manage.app')
@section('title', trans('Page').trans('Manage'))
@section('content')
<div class="container-fluid">
    <div class="row justify-content-center">
        <div class="col-md-12">
            <div class="card">
                <div class="card-header">{{ trans('Page').trans('Manage') }}</div>

                <div class="card-body">
                <ul class="list-inline">
                    <li class="list-inline-item">{{ App\Button::Create() }}</li>
                </ul>
                <div class="alert alert-warning" role="alert">
                        1. 前台排序以頁面修改日期為主。<br>
                        2. 頁面網址唯一，用途為導向該頁面。
                    </div>
                    <div class="table-responsive">
                        <table id="data" class="table table-hover table-bordered text-center">
                            <thead>
                                <tr class="table-info active">
                                    <th class="text-nowrap text-center">{{ trans('Editor') }}</th>
                                    <th class="text-nowrap text-center">{{ trans('Title') }}</th>
                                    <th class="text-nowrap text-center">{{ trans('Page').trans('Url') }}</th>
                                    <th class="text-nowrap text-center">{{ trans('Is_open') }}</th>
                                    <th class="text-nowrap text-center">{{ trans('Is_slide') }}</th>
                                    <th class="text-nowrap text-center">{{ trans('Action') }}</th>
                                </tr>
                            </thead>
                            <tbody>
                                @foreach ($all_pages as $page)
                                    <tr>
                                        <td>{{ $page->editor }}</td>
                                        <td>{{ $page->title }}</td>
                                        <td>
                                            {{ $page->url }}
                                        </td>
                                        <td>
                                            <font color="{{App\Enum::is_open['color'][$page->is_open]}}"><i class="fas fa-{{App\Enum::is_open['label'][$page->is_open]}}"></i></font>
                                        </td>
                                        <td>
                                            <font color="{{App\Enum::is_open['color'][$page->is_slide]}}"><i class="fas fa-{{App\Enum::is_open['label'][$page->is_slide]}}"></i></font>
                                        </td>
                                        <td>
                                            <form action="{{ route('page.edit',$page->id) }}" method="GET">
                                            @csrf
                                            {{ App\Button::edit($page->id) }}
                                            </form>
                                            <form action="{{ route('page.destroy',$page->id) }}" method="POST">
                                            @method('DELETE')
                                            @csrf
                                            {{ App\Button::deleting($page->id) }}
                                            </form>
                                        </td>
                                    </tr>
                                @endforeach
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection

```
`PageController.php` :
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\DB;
use App\Page;
use App\Navbar;

 ...略...
 
public function index()
{
    $all_pages = DB::table('pages')->get();
    return view('manage.page.index',compact('all_pages'));
}
```

{: id='editor'}
### 7. 加入編輯器 [🔝](#top)
---

在新增文章時需要使用編輯器輔助，可以使用像是 [Ckeditor](https://ckeditor.com/ckeditor-4/download/) 或 [TinyMCE](https://www.tiny.cloud/) 等等的編輯器。

這次我使用 [Summernote](https://summernote.org/) 當作範例，在 `manage/app.blade.php` 中加入 :

```html
<!-- include libraries(jQuery) -->
<script src="https://code.jquery.com/jquery-3.4.1.min.js" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<!-- include summernote css/js -->
<link href="https://cdn.jsdelivr.net/npm/summernote@0.8.16/dist/summernote-bs4.min.css" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/summernote@0.8.16/dist/summernote-bs4.min.js"></script>
```

{: id='create'}
### 8. 頁面新增 [🔝](#top)
---

`create.blade.php` :

```html
@extends('_layouts.manage.app')
@section('title', trans('Page').trans('Create'))
@section('content')
<div class="container-fluid">
    <div class="row justify-content-center">
        <div class="col-md-12">
            <div class="card">
                <form action="{{ route('page.store') }}" method="POST">
                    <div class="card-header">{{ trans('Page').trans('Create') }}</div>
                    <div class="card-body">
                        <ul class="list-unstyled">
                            <li>{{ App\Button::GoBack(route('page.index')) }}</li>
                        </ul>
                        @csrf
                        <div class="form-group row">
                            <label for="title" class="col-md-4 col-form-label text-md-right">{{ trans('Title') }}</label>
                            <div class="col-md-6">
                                <input type="text" class="form-control @error('title') is-invalid @enderror" id="title" name="title" value="{{ old('title') }}" placeholder="{{ trans('Title') }}">
                                @error('title')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="url" class="col-md-4 col-form-label text-md-right">{{ trans('Page').trans('Url') }}</label>
                            <div class="col-md-6">
                                <input type="text" class="form-control @error('url') is-invalid @enderror" id="url" name="url" value="{{ old('url') }}" placeholder="{{ trans('Page').trans('Url') }}">
                                @error('url')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="content" class="col-md-4 col-form-label text-md-right">{{ trans('Content') }}</label>
                            <div class="col-md-12">
                                <textarea id="summernote" name="editordata" class="form-control" >{!! old('content') !!}</textarea>
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="is_open" class="col-md-4 col-form-label text-md-right">{{ trans('Is_open') }}</label>
                            <div class="form-inline col-md-6">
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_open" id="is_open1" value="1">
                                    <label class="custom-control-label" for="is_open1">{{ trans('Yes') }}</label>
                                </div>
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_open" id="is_open2" value="0">
                                    <label class="custom-control-label" for="is_open2">{{ trans('No') }}</label>
                                </div>
                            </div>
                        </div>
                        <div class="form-group row">
                            <label for="is_slide" class="col-md-4 col-form-label text-md-right">{{ trans('Is_slide') }}</label>
                            <div class="form-inline col-md-6">
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_slide" id="is_slide1" value="1">
                                    <label class="custom-control-label" for="is_slide1">{{ trans('Yes') }}</label>
                                </div>
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_slide" id="is_slide2" value="0">
                                    <label class="custom-control-label" for="is_slide2">{{ trans('No') }}</label>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="card-footer text-center">
                        <input type="submit" class="btn btn-primary" value="{{ trans('Create') }}">
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
@endsection
@section('script')
@parent
<script type="text/javascript">
    $(document).ready(function() {
      $('#summernote').summernote();
      $('.note-btn').removeAttr('title');
    });
</script>
@show

```

`PageController.php` :

```php
public function create()
{
    if (Auth::check() && Auth::user()->permission < '2') {
        return back()->with('warning', '權限不足以訪問該頁面 !');
    }

    return view('manage.page.create');
}

public function store(Request $request)
{
    if (Auth::check() && Auth::user()->permission < '2') {
        return back()->with('warning', '權限不足以訪問該頁面 !');
    }
    $error = 0;
    $page = new Page;
    $data = $request->validate([
        'menu_id' => ['nullable'],
        'title' => ['required', 'string', 'max:255'],
        'url' => ['required', 'string', 'max:255','unique:pages,url'],
        'is_open' => ['required'],
        'is_slide' => ['required'],
    ]);

    foreach ($request->except('_token', '_method', 'files') as $key => $value) {
        if ($request->filled($key) && $request->filled($key) != NULL && $key != 'content') {
            $page->$key = $data[$key];
            if ($page->$key == '') {
                $error += 1;
            }
        }
        else{
            $page->$key = NULL;
        }
    }

    $page->content = $request->input('content');
    $page->editor = Auth::user()->name;
    if ($error == 0) {
        $page->save();
    }
    else{
        return back()->withInput()->with('warning', '請確認輸入 !');
    }

    return back()->with('success','頁面新增成功 !');
}
```

{: id='edit'}
### 9. 頁面修改 [🔝](#top)
---

`edit.blade.php` :

```html
@extends('_layouts.manage.app')
@section('title', trans('Page').trans('Edit'))
@section('content')
<div class="container-fluid">
    <div class="row justify-content-center">
        <div class="col-md-12">
            <div class="card">
                <div class="card-header">{{ trans('Page').trans('Edit') }}</div>

                <div class="card-body">
                    <ul class="list-unstyled">
                        <li>{{ App\Button::GoBack(route('page.index')) }}</li>
                    </ul>
                    <form method="POST" action="{{ route('page.update' , $page->id) }}">
                        @csrf
                        @method('PUT')

                        <div class="form-group row">
                            <label for="title" class="col-md-4 col-form-label text-md-right">{{ trans('Title') }}</label>

                            <div class="col-md-6">
                                <input id="title" type="text" class="form-control @error('title') is-invalid @enderror" name="title" value="{{ $page->title }}" required autocomplete="{{ trans('Title') }}" autofocus>

                                @error('title')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <div class="form-group row">
                            <label for="url" class="col-md-4 col-form-label text-md-right">{{ trans('Page').trans('Url') }}</label>

                            <div class="col-md-6">
                                <input id="url" type="text" class="form-control @error('url') is-invalid @enderror" name="url" value="{{ $page->url }}" required autocomplete="{{ trans('Page').trans('Url') }}" autofocus readonly>

                                @error('url')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <div class="form-group row">
                            <label for="content" class="col-md-4 col-form-label text-md-right">{{ trans('Content') }}</label>

                            <div class="col-md-12">
                                <textarea id="summernote" name="content" class="form-control ckeditor" >{{ $page->content }}</textarea>
                            </div>
                        </div>

                        <div class="form-group row">
                            <label for="is_open" class="col-md-4 col-form-label text-md-right">{{ trans('Is_open') }}</label>
                            <div class="form-inline col-md-6">
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_open" id="is_open1" value="1" {{ ($page->is_open=="1")? "checked" : "" }}>
                                    <label class="custom-control-label" for="is_open1">{{ trans('Yes') }}</label>
                                </div>
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_open" id="is_open2" value="0" {{ ($page->is_open=="0")? "checked" : "" }}>
                                    <label class="custom-control-label" for="is_open2">{{ trans('No') }}</label>
                                </div>
                            </div>
                        </div>

                        <div class="form-group row">
                            <label for="is_slide" class="col-md-4 col-form-label text-md-right">{{ trans('Is_slide') }}</label>
                            <div class="form-inline col-md-6">
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_slide" id="is_slide1" value="1" {{ ($page->is_slide=="1")? "checked" : "" }}>
                                    <label class="custom-control-label" for="is_slide1">{{ trans('Yes') }}</label>
                                </div>
                                <div class="custom-control custom-radio custom-control-inline">
                                    <input class="custom-control-input" type="radio" name="is_slide" id="is_slide2" value="0" {{ ($page->is_slide=="0")? "checked" : "" }}>
                                    <label class="custom-control-label" for="is_slide2">{{ trans('No') }}</label>
                                </div>
                            </div>
                        </div>

                        <div class="form-group row">
			                <div class="col-md-4">
			                    <input type="submit" class="btn btn-primary" value="送出">
			                </div>
			            </div>
                	</form>
                </div>

            </div>
        </div>
    </div>
</div>
@endsection
@section('script')
@parent
<script type="text/javascript">
    $('#summernote').summernote();
    $('.note-btn').removeAttr('title');
</script>
@show

```

`PageController.php` :

```php
public function edit($id)
    {
        if (Auth::check() && Auth::user()->permission < '3') {
            return back()->with('warning', '權限不足以訪問該頁面 !');
        }
        $page = Page::where('id',$id)->first();
        $navbars = Navbar::where('type', '=', '1')->get();
        return view('manage.page.edit',compact('page','navbars'));
    }

    public function update(Request $request, $id)
    {
        if (Auth::check() && Auth::user()->permission < '3') {
            return back()->with('warning', '權限不足以訪問該頁面 !');
        }
        $error = 0;
        $page = Page::where('id',$id)->first();

        $data = $this->validate($request, [
            'menu_id' => ['nullable'],
            'title' => ['required', 'string', 'max:255'],
            'url' => ['required', 'string', 'max:255'],
            'is_open' => ['required'],
            'is_slide' => ['required'],
        ]);

        foreach ($request->except('_token','_method','files') as $key => $value) {
            if ($request->filled($key) && $request->filled($key) != NULL && $key != 'content') {
                $page->$key = $data[$key];
                if ($page->$key == '') {
                    $error += 1;
                }
            }
            else{
                $page->$key = NULL;
            }
        }
        $page->content = $request->input('content');
        $page->editor = Auth::user()->name;

        if ($error == 0) {
            $page->save();
        }
        else{
            return back()->withInput()->with('warning', '請確認輸入 !');
        }
        return back()->with('success','修改頁面成功 !');
    }
```

{: id='delete'}
### 10. 頁面刪除 [🔝](#top)
---

`PageController.php` :

```php
public function destroy($id)
{
    if (Auth::check() && Auth::user()->permission < '4') {
        return back()->with('warning', '權限不足以訪問該頁面 !');
    }
    Page::destroy($id);
    return back()->with('success','刪除頁面成功 !');
}
```

{: id='end'}

### 11. 結語 [🔝](#top)
---

叮叮結束😁

下次來聊聊前台顯示以及為什麼 `Controller` 的 `store()` 和 `update()` 都要寫這麼醜。

一般不都 `Page::create($request->all())` 跟 `Page::updateOrCreate($request->all())` 直接搞定嗎 ?
