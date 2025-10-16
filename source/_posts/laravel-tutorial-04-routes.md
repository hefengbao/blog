---
title: Laravel 入门：04-路由
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
## 一、基本路由

所有的路由文件都位于 routes 目录下，routes/web.php 中已经定义好了一个路由：

``` php
Route::get('/', function () {
    return view('welcome');
});
```

但是一般情况下，格式为：`Route::路由方法('uri', ['控制器'，'方法'])->name('路由命名')` ;

假设有控制器 `app/Http/Controllers/UserController::class`：

``` php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index()
    {
        echo 'Hello, World!';
    }

    public function create()
    {

    }

    public function store(Request $request)
    {

    }

    public function edit($id)
    {

    }

    public function update($id, Request $request)
    {

    }

    public function show($id)
    {
      echo $id;
    }

    public function destroy($id)
    {
        
    }
}
```

则可以定义路由：

``` php
Route::get('/users',[\App\Http\Controllers\UserController::class, 'index'])->name('users.index');
Route::get('/users/create', [\App\Http\Controllers\UserController::class, 'create'])->name('users.create');
Route::post('/users', [\App\Http\Controllers\UserController::class, 'store'])->name('users.store');
Route::get('/users/{id}', [\App\Http\Controllers\UserController::class, 'show'])->name('users.show');
Route::get('/users/{id}/edit', [\App\Http\Controllers\UserController::class, 'edit'])->name('users.edit');
Route::put('/users/{id}', [\App\Http\Controllers\UserController::class, 'update'])->name('users.update');
Route::delete('/users/{id}', [\App\Http\Controllers\UserController::class, 'destroy'])->name('user.destroy');
```

访问 `http://laravel-demo.test/users/index` 。

## 二、路由方法

常用的 get，post，put, patch, delete, options 和 HTTP 方法一一对应，可参照理解。

## 三、路由参数

`Route::get('/users/{id}', [\App\Http\Controllers\UserController::class, 'show'])->name('users.show')` 中的 id 即为路由参数， 访问的url 为：` http://laravel-demo.test/users/1` , id 必须有值，如果 id 可以为空，则可以这样定义：

```php
Route::get('/users/{id?}', [\App\Http\Controllers\UserController::class, 'show']);
```

一个路由中可以有多个参数，比如：

```php
Route::get('/users/{id}/comments/{commentId}', [\App\Http\Controllers\UserController::class, 'show']);
```

对应到控制器中：

``` php
class UserController extends Controller
{
  //这里的参数命名可以和路由中的参数命名不一样，但要记得，顺序是一样的，第一个参数获取的一定是 id， 第二个参数是commentId
  public function show($id, $commentId){}
}
```

### 四、路由命名

在路由中使用 name() 方法给该路由命名，比如： 

``` php
Route::get('/users/{id}', [\App\Http\Controllers\UserController::class, 'show'])->name('users.show');
```

路由命名的使用：

``` php
class UserController extends Controller
{
  public function index()
  {
     echo route('name.index');
   }

  public function show($id)
  {
     echo route('name.show', $id);
      // 或者
     echo route('name.show', ['id' => $id]);
   } 
}
```

## 五、路由分组（前缀、中间件）

路由前缀、路由中间件都是结合路由分组使用，比如管理后台的路由：

```php
Route::prefix('admin')->group(function(){
  Route::get('/', [\app\Http\Controllers\Admin\HomeController::class, 'index']);
});
```

如果项目很复杂，管理后台的路由可单独放在一个文件中，在 `routes` 目路中，新建 `admin.php`， 然后再 `app/Providers/RouteServiceProvider::class` 的 `boot` 方法中添加：

```php
$this->routes(function () {
  ...
  
  // 仿照添加即可
  Route::middleware('web')
    ->prefix('admin')
    ->group(base_path('routes/admin.php'));
});
```

路由中间件后面再说。

Demo：https://github.com/hefengbao/laravel-demo

https://laravel-news.com/managing-routes


```php
$route = Route::current();
$name = Route::currentRouteName();
$action = Route::currentRouteAction();
```