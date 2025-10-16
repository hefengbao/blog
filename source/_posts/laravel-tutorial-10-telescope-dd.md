---
title: Laravel 入门：10-Telescope & dd 方法
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
## 一、Telescope

Telescope 是官方提供的调试工具，可以记录应用程序的请求、异常、日志条目、数据库查询、排队的作业、邮件、消息通知、缓存操作、定时计划任务、变量打印等。

```shell
composer require laravel/telescope --dev

php artisan telescope:install

php artisan migrate
```

Telescope 主要用于调试，一般在生产环境就可以不用安装， 所以上面安装时加了 `--dev` 参数，可以查看根目录下的 `composer.json` 文件，`laravel/telescope` 添加在 `require-dev` 中：

```json
"require-dev": {
    ...
    "laravel/telescope": "^4.10",
    ...
 },
```

所以需要做一些额外的设置，编辑 `config/app.php` ：

```php
'providers' => [

  // 找到这一句并删除
   App\Providers\TelescopeServiceProvider::class,

];
```

然后编辑 `app/Providers/AppServiceProvider.php` 文件：

```php
public function register()
{
  // 添加如下内容
  if ($this->app->environment('local')) {
    $this->app->register(\Laravel\Telescope\TelescopeServiceProvider::class);
    $this->app->register(TelescopeServiceProvider::class);
  }
}
```

最后编辑 `composer.json` ：

```json
"extra": {
    "laravel": {
        "dont-discover": [
            // 添加
            "laravel/telescope"
        ]
    }
},
```

访问` http://laravel-demo.test/telescope`  探索具体用法，比如查询构造器生成的 sql 具体是什么样的 😄。

## 二、dd()

`dd()` 是个有用的辅助调试方法：

```php
class UserController extends Controller
{
    public function index()
    {
        $user = User::find(1);

        dd($user);
    }
}
```

访问 ` http://laravel-demo.test/users` 查看效果。

`dd()` 方法会终止他后面的代码执行：

```php
class UserController extends Controller
{
    public function index()
    {
        $user = User::find(1);

        dd($user);

        echo  'Hello World';
    }
}
```

程序不会输出 Hello World 。

Demo：https://github.com/hefengbao/laravel-demo