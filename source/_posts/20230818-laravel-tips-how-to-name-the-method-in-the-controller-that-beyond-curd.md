---
title: Laravel Tips：控制器中 CURD 方法不能满足需求时如何给方法命名？
date: 2023-08-18 17:17:17
tags:
  - laravel
categories: Laravel
---
使用 Laravel 时可以方便的用 Artisan 命令创建控制器，比如 `php artisan make:controller PostController` ：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class PostController extends Controller
{
    //
}
```

但这样创建的控制器类中没有任何方法，可以添加 `-r` 参数来创建包含 CURD 方法的控制器，例如 `php artisan make:controller PostController -r`  ：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class PostController extends Controller
{
    /**
     * Display a listing of the resource.
     */
    public function index()
    {
        //
    }

    /**
     * Show the form for creating a new resource.
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     */
    public function store(Request $request)
    {
        //
    }

    /**
     * Display the specified resource.
     */
    public function show(string $id)
    {
        //
    }

    /**
     * Show the form for editing the specified resource.
     */
    public function edit(string $id)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     */
    public function update(Request $request, string $id)
    {
        //
    }

    /**
     * Remove the specified resource from storage.
     */
    public function destroy(string $id)
    {
        //
    }
}
```

然而我们实际的业务中肯定不仅仅是 CURD 操作，比如我们需要添加搜索（search） 功能，那么要在控制器中添加一个 `search()` 方法吗？之前看到过 Laravel 作者的推文，他认为良好的项目架构应该充分利用默认的几种方法，不要添加多余的方法导致控制器过于臃肿，如果要实现其他业务，应该创建新的控制器，比如添加 search 功能，通过在 PostController 前面添加限定修饰的方式创建新的控制器，比如，`php artisan make:controller SearchablePostController` ：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class SearchablePostController extends Controller
{
    public function index(Request $request)
    {
        //TODO
    }
}
```

也有人习惯使用[单动作控制器](https://laravel.com/docs/10.x/controllers#single-action-controllers) ，所有的控制器都只实现单一功能，那就更好办了，`php artisan make:controller Post/SearchController --invokable` ：

```php
<?php

namespace App\Http\Controllers\Post;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class SearchController extends Controller
{
    /**
     * Handle the incoming request.
     */
    public function __invoke(Request $request)
    {
        //
    }
}
```