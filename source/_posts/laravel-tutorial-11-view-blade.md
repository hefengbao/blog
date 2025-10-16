---
title: Laravel 入门：11-视图 & Blade 模板
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
视图在 `resources/views` 目录下，默认已存在 `welcome.blade.php` 视图，可知 Laravel 视图文件的扩展名是 `.blade.php` ，这便是 Blade 模板。可以使用官方扩展包 `laravel/ui: Laravel UI utilities and presets. (github.com) ` 开启学习，本篇博客使用 bootstrap 作为前端预设，要学习下面的内容，你要熟悉 bootstrap 的基本语法：

```shell
composer require laravel/ui

php artisan ui bootstrap --auth

# 😓我遇到的问题：使用 Homestead 虚拟机，在虚拟机中遇到报错，没找到好的解决办法，可以在自己主机打开运行下面命令
npm install && npm run dev
```

如果` npm install && npm run dev` 无论如何都搞不定的话，修改 `resources/views/layouts/app.balde.php` ：

```php
 // 删除
 @vite(['resources/sass/app.scss', 'resources/js/app.js'])

//添加
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-rbsA2VBKQhggwzxH7pPCaAqO46MgnOM80zW1RWuH61DGLwZJEdK2Kadq2F9CUG65" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-kenU1KFdBIe4zVF0s0G1M5b4hcpxyD9F7jL+jjXkk+Q2h455rYXK/7HAuoJl+0I4" crossorigin="anonymous"></script>

// 使用其他的 CDN 也行
```

一个网站或者一个系统的 UI，往往有一些各个页面共有的元素，比如顶部导航、侧边栏、页脚等，把这些元素可以抽象出来作为总体布局，`resources/views/layouts/app.balde.php` 就是这样一个总体布局：

```php
// 代码做了简化

<!doctype html>
<head>
<!-- title -->
    <title>@yield('title')</title>
</head>
<body>
    <div id="app">
        <nav class="navbar navbar-expand-md navbar-light bg-white shadow-sm">
            <!-- 顶部导航 -->
        </nav>

        <!-- 内容区域 -->
        <main class="py-4">
            @yield('content')
        </main>
    </div>
</body>
</html>
```

一些可变内容可以用 `yield` 指令占位，然后在子布局中用 `section` 指令替换，比如创建一个显示用户列表的界面 `resources/views/users/index.blade.php` ：

```php
<!--继承主布局-->
@extends('layouts.app')

<!--对应主布局中的 @yield('title')-->
@section('title')
    用户列表
@endsection

<!--对应主布局中的 @yield('content')-->
@section('content')
<div class="card">
    <div class="card-body">
        <table class="table table-bordered">
            <thead>
            <tr>
                <th>ID</th>
                <th>姓名</th>
                <th>邮箱</th>
            </tr>
            </thead>
        </table>
    </div>
</div>
@endsection
```

访问 `http://laravel-demo.test/users` 查看。

在控制器中渲染视图：

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

class UserController extends Controller
{
    public function index()
    {
        return view('users.index');

        //或者
        return view('users/index');
    }
}
```

把数据传递到视图，使用 `compact` ：

```php
class UserController extends Controller
{
    public function index()
    {
        $users = User::all();

        
        return view('users/index', compact('users'));
    }
}
```

修改视图：

```php
<!-- resources/views/users/index.blade.php -->

@section('content')
<div class="card">
    <div class="card-body">
        <table class="table table-bordered">
            <thead>
            <tr>
                <th>ID</th>
                <th>姓名</th>
                <th>邮箱</th>
            </tr>
            </thead>
            <tbody>
            @foreach($users as $user)
                <tr>
                    <td>{{ $user->id }}</td>
                    <td>{{ $user->name }}</td>
                    <td>{{ $user->email }}</td>
                </tr>
            @endforeach
            </tbody>
        </table>
    </div>
</div>
@endsection
```

这里使用了 `foreac`h 指令，和 php 的 foreach 方法是类似的，只是在 Blade 模板中不能用 {} 来定义方法体范围，  而是使用成对出现的指令来界定，比如 `@section` 和 `@endsection`、`@foreach` 和 `@endsection` 等。

其他的指令请结合文档学习。

Demo：https://github.com/hefengbao/laravel-demo