---
title: Laravel 入门：22-用户认证
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
`laravel/ui` 提供了认证相关的脚手架（Scaffold），使用 Artisan 命令快速实现相关功能：

```shell
php artisan ui bootstrap --auth
```

可在 `app/Http/Controllers/Auth` 目录下查看相关控制器 、在 `resources/views/auth` 目录下查看相关视图， 同时在 `routes/web.php` 中添加了路由组 `Auth::routes();`，可通过命令 `php artisan route:list` 查看：

```shell
 GET|HEAD        login ................................................ login › Auth\LoginController@showLoginForm
  POST            login ................................................................ Auth\LoginController@login
  POST            logout ..................................................... logout › Auth\LoginController@logout
  GET|HEAD        password/confirm .............. password.confirm › Auth\ConfirmPasswordController@showConfirmForm
  POST            password/confirm ......................................... Auth\ConfirmPasswordController@confirm
  POST            password/email ................ password.email › Auth\ForgotPasswordController@sendResetLinkEmail
  GET|HEAD        password/reset ............. password.request › Auth\ForgotPasswordController@showLinkRequestForm
  POST            password/reset ............................. password.update › Auth\ResetPasswordController@reset
  GET|HEAD        password/reset/{token} .............. password.reset › Auth\ResetPasswordController@showResetForm
  GET|HEAD        register ................................ register › Auth\RegisterController@showRegistrationForm
```

查看 `app/Http/Controllers/HomeController.php`：

```php
class HomeController extends Controller
{
    public function __construct()
    {
        // 根据情况使用下列的任意一个
        // 所有方法都需要认证
        $this->middleware('auth');
        // 指定需要认证的方法
        $this->middleware('auth', ['only'=>[]]);
        // 指定不需要认证的方法
        $this->middleware('auth', ['except' => []]);
    }
}
```

还可以在路由中设置：

```php
<!-- routes/web.php -->

Route::middleware(['auth'])->group(function (){
   // 定义路由
});
```

访问 http://laravel-demo.test/home  查看。

获取认证的用户：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class HomeController extends Controller
{
    public function authUser(Request $request){

        // 获取当前的认证用户信息 ...
        $user = Auth::user();
        // 或者
        $user = \auth()->user();
        // 或者
        $user = $request->user();

        // 获取当前的认证用户id ...
        $id = Auth::id();
        // 或者
        $id = \auth()->id();
    }
}
```

在一些场景下，比如系统接入了统一身份认证或者微信等第三方登录，那就不需要我们处理登录相关逻辑，但是用户登入系统，我们仍然需要用 `session` 保存用户状态，那么可以这么做：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Support\Facades\Auth;

class HomeController extends Controller
{
    public function scanLogin($token){
        // 假设用户表中有个 wechat_token 字段，保存微信登录的 token
        $user = User::where('wechat_token', $token)->first();

        Auth::login($user);
        // 或者
        \auth()->login($user);
        // 或者
        Auth::loginUsingId($user->id);
        // 或者
        \auth()->loginUsingId($user->id);
    }
}

```

参考：https://github.com/laravel/ui