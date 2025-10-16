---
title: Laravel 入门：12-请求 & CSRF 保护
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
以添加用户、更新用户为例来说明。

编辑 `resources/views/users/create.blade.php`：

```php
@extends('layouts.app')

@section('content')
<div class="card">
    <div class="card-body">
        <form action="{{ route('users.store') }}" method="POST">
            <div class="mb-3">
                <label for="name" class="form-label">用户名</label>
                <input type="text" class="form-control" id="name" name="name" placeholder="" required>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">邮箱</label>
                <input type="email" class="form-control" id="email" name="email" placeholder="" required>
            </div>
            <div class="mb-3">
                <button class="btn btn-primary" type="submit">保存</button>
            </div>
        </form>
    </div>
</div>
@endsection
```

编辑 `app/Http/Controllers/UserController.php` 的 `create` 方法：

```php
class UserController extends Controller
{
    public function create()
    {
       return view('users.create');
    }
}
```

访问 `http://laravel-demo.test/users/create` ，输入数据，点击保存，会发现报错 `419 PAGE EXPIRED`，这是因为 Laravel 框架 Web 请求默认开启了 * CSRF *（跨站点请求伪造）保护， 编辑 `resources/views/users/create.blade.php` ：

```php
@extends('layouts.app')

@section('content')
<div class="card">
    <div class="card-body">
        <form action="{{ route('users.store') }}" method="POST">
            @csrf
            <div class="mb-3">
                <label for="name" class="form-label">用户名</label>
                <input type="text" class="form-control" id="name" name="name" placeholder="" required>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">邮箱</label>
                <input type="email" class="form-control" id="email" name="email" placeholder="" required>
            </div>
            <div class="mb-3">
                <button class="btn btn-primary" type="submit">保存</button>
            </div>
        </form>
    </div>
</div>
@endsection
```

仅仅只是添加了 `@csrf` 指令，相当于添加了

```html
<input type="hidden" name="_token" value="{{ csrf_token() }}" />
```

参考文档 `https://learnku.com/docs/laravel/9.x/csrf/12211` 学习。

 编辑 `app/Http/Controllers/UserController.php` 的 `store` 方法 ：
 
 ```php
 <?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function store(Request $request)
    {
        dd($request);

        // 或者
        $request->dd();
    }
}
 ```
 
 提交数据查看：
 
 ![post request](https://www.8ug.icu/storage/upload/images/202212/u11xAHaIVnwdcjyDhAexchLjCo5eClEn4E2pkOxi.png)
 
 如上图标注的，就是请求参数，接下来说明如何获取请求参数：
 
```php
 class UserController extends Controller
{
    public function store(Request $request)
    {
        $name = $request->name;
        // 或者
        $name = $request->input('name'); 
        // 或者
        $name = $request->input('name', '默认用户名'); // 在 name 参数为 null 时指定默认值
    }
}
```

完善代码：

```php
class UserController extends Controller
{
    public function store(Request $request)
    {
        // 如果参数不做任何处理
        // 需要在 User 模型中指定 $fillable
        User::create($request->except('_token'));

        // 或者
        // 需要对参数进行处理，比如加密
        // 需要在 User 模型中指定 $fillable
        User::create([
           'name' => $request->input('name'),
           'email' => $request->input('email')
        ]);

        // 或者
        // 需要对参数进行处理，比如加密
        $user = new User();
        $user->name = $request->input('name');
        $user->email = $request->input('email');
        $user->save();

        return redirect()->route('users.index');
    }
}
```

创建并编辑 `resources/views/users/edit.blade.php` ：

```php
@extends('layouts.app')

@section('content')
<div class="card">
    <div class="card-body">
        <form action="{{ route('users.update', $user->id) }}" method="POST">
            @csrf
            @method('PUT')
            <div class="mb-3">
                <label for="name" class="form-label">用户名</label>
                <input type="text" class="form-control" id="name" name="name" value="{{ $user->name }}" required>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">邮箱</label>
                <input type="email" class="form-control" id="email" name="email" value="{{ $user->email }}" required>
            </div>
            <div class="mb-3">
                <button class="btn btn-primary" type="submit">更新</button>
            </div>
        </form>
    </div>
</div>
@endsection
```

要点：`@method('PUT')` ，对于 `PUT/PATCH`、`DELETE` 等请求，需要在表单中通过 `method` 指令指明。

```php
class UserController extends Controller
{
    public function edit($id)
    {
        $user = User::findOrFail($id);

        return view('users.edit', compact('user'));
    }

    public function update(Request $request, $id)
    {
        $user = User::findOrFail($id);

        $user->update([
            'name' => $request->input('name'),
            'email' => $request->input('email')
        ]);

        return redirect()->route('users.index');
    }
}
```

参考文档：https://learnku.com/docs/laravel/9.x/requests/12213。

完善 `resources/views/users/index.balde.php` ：

```php
@section('content')
<div class="card">
    <div class="card-header">
        <a href="{{ route('users.create') }}" class="btn btn-primary">添加用户</a>
    </div>
    <div class="card-body">
        <table class="table table-bordered">
            <thead>
            <tr>
                <th>ID</th>
                <th>姓名</th>
                <th>邮箱</th>
                <th></th>
            </tr>
            </thead>
            <tbody>
            @foreach($users as $user)
                <tr>
                    <td>{{ $user->id }}</td>
                    <td>{{ $user->name }}</td>
                    <td>{{ $user->email }}</td>
                    <td><a href="{{ route('users.edit', $user->id) }}" class="btn btn-warning">编辑</a></td>
                </tr>
            @endforeach
            </tbody>
        </table>
    </div>
</div>
@endsection
```

Demo：https://github.com/hefengbao/laravel-demo