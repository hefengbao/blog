---
title: Laravel 入门：06-控制器
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
## 一、创建控制器

控制器在 `app/Http/Controllers` 目录下，默认新建的控制器应继承自该目录下的 `Controller::class`，可以使用 `php artisan make:controller` 命令创建类：

```shell
php artisan make:controller UserController
```

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    //
}
```

如上面代码所示，里面没有任何方法，我们可以添加 -r 参数，创建一个资源控制器（resource controller），即所谓的 CURD：它代表创建（Create）、更新（Update）、读取（Read）和删除（Delete）操作：

```shell
php artisan make:controller UserController -r
```

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index()
    {
        //
    }

    public function create()
    {
        //
    }

    public function store(Request $request)
    {
        //
    }

    public function show($id)
    {
        //
    }

    public function edit($id)
    {
        //
    }

    public function update(Request $request, $id)
    {
        //
    }
    
    public function destroy($id)
    {
        //
    }
}
```

## 二、定义路由

参照在路由那一篇的内容，我们可以这样定义：

```php
Route::get('/users',[\App\Http\Controllers\UserController::class, 'index'])->name('users.index');
Route::get('/users/create', [\App\Http\Controllers\UserController::class, 'create'])->name('users.create');
Route::post('/users/store', [\App\Http\Controllers\UserController::class, 'store'])->name('users.store');
Route::get('/users/{id}', [\App\Http\Controllers\UserController::class, 'show'])->name('users.show');
Route::get('/users/{id}/edit', [\App\Http\Controllers\UserController::class, 'edit'])->name('users.edit');
Route::put('/users/{id}/update', [\App\Http\Controllers\UserController::class, 'update'])->name('users.update');
Route::delete('/users/{id}', [\App\Http\Controllers\UserController::class, 'destroy'])->name('user.destroy');
```

但是对于资源控制器，我们可以把上面的路由简化为：

```php
Route::resource('/users', \App\Http\Controllers\UserController::class);
```

有时我们可能不需要删除操作，那么可以这样排除掉：

```php
Route::resource('/users', \App\Http\Controllers\UserController::class)->except(['destroy']);
```

或者只需要 `index` 和 `show` ，那么可以只添加：

```php
Route::resource('/users', \App\Http\Controllers\UserController::class)->only(['index','show']);
```

当然这只是脚手架创建的一般功能下的控制器，如果要实现别的功能，比如导出，我们可以继续在 `UserController::class` 中添加添加` export` 方法，并定义相应的路由：

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    ...

    public fun export()
    {
      //todo
    }
}
```

```php
Route::get('/users/export', [\App\Http\Controllers\UserController::class, 'export'])->name('users.export');
Route::resource('/users', \App\Http\Controllers\UserController::class);
```

Demo：https://github.com/hefengbao/laravel-demo