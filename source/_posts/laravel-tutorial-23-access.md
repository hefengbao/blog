---
title: " Laravel 入门：23-用户授权"
date: 2022-08-08 17:17:17
tags:
  - laravel
categories: Laravel
---
对于多用户使用的系统，必须要考虑的是当前用户有没有权限去访问某一资源（比如系统设置只允许管理员访问操作），有没有权限执行某一操作（比如某一用户填写的表单其他用户不能删除）。

Laravel 主要提供了两种授权操作的方法: `拦截器（Gates）` 和 `策略（Policies）`。

文档中说：
> 拦截器（gates），更适用于没有与任何模型或资源有关的授权操作，例如查看管理员仪表盘。与之相反，当您希望为特定的模型或资源进行授权管理时，应该使用策略（policies) 方法。

以此去理解的话，文档中举得例子就不是那么合理了。

比如有一个博客系统，用户有几类角色：管理员（admin）、作者（author），使用 `User` 模型的 `role` 字段来保存。博客系统设置只允许管理员操作，那么我们就可以定义一个 gate：

```php
<?php

namespace App\Providers;

use App\Models\User;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Gate;

class AuthServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->registerPolicies();

        //
        Gate::define('settings', function (User $user){
            return $user->role == 'admin';
        });
    }
}
```

使用：

```php
<!-- app/Http/Controllers/SettingsController.php-->
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Gate;
class SettingsController extends Controller
{
    public function index(){
        if (! Gate::allows('settings')) {
            abort(403);
        }

        //TODO
    }
}
```

用户发布文章的相关授权则可以通过策略实现：

```shell
php artisan make:policy PostPolicy --model=Post
```

通过上面命令可以看出，策略是和模型绑定的。

```php
<?php

namespace App\Policies;

use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class PostPolicy
{
    use HandlesAuthorization;

    public function viewAny(User $user)
    {
        //
    }

    public function view(User $user, Post $post)
    {
        //
    }

    public function create(User $user)
    {
        //
    }

    public function update(User $user, Post $post)
    {
        return $user->id == $post->user_id || $user->role == 'admin';
    }

    public function delete(User $user, Post $post)
    {
        //
    }

    public function restore(User $user, Post $post)
    {
        //
    }

    public function forceDelete(User $user, Post $post)
    {
        //
    }
}

```

和我们通过 ` php artisan make:controller PostController --resource` 创建的控制器中的方法差不多是对应的。

```php
<?php

namespace App\Providers;

use App\Models\User;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Gate;

class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        // 'App\Models\Model' => 'App\Policies\ModelPolicy',
        'App\Models\Post' => 'App\Policies\PostPolicy'
    ];

```

然后判断用户是否有权限就可以了。

```php
class PostController extends Controller
{
    /**
     * 更新指定的博客文章
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \App\Models\Post  $post
     * @return \Illuminate\Http\Response
     *
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

	// 或者
	if ($request->user()->cannot('update', Post::class)) {
            abort(403);
        }
    }
}

```

其他在视图或路由的判断参考文档。

对于一些稍复杂的系统可通过 https://github.com/spatie/laravel-permission 等进行权限管理。