---
title: Laravel 入门：14-模型关联
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
稍微复杂点的项目，数据必然不可能存放在一张数据表中，数据表的设计一般通过外键来关联。对应到 Laravel 的模型，就是本篇文章要说的模型关联，是一个比较重要的知识点。

理解或使用模型关联，先选定一个模型作为主体，然后在和其他模型关联，以此理解所谓的 `一对一`、`一对多`、`多对多` 等关联模式。一般情况下，可以以默认的 `User` 模型作为主体开始。

## 一对一

对于用户表（User）用来保存频繁查询的账户信息，而对于用户的其他信息（比如个人简介、地址等）则保存在用户资料表中（Profile），那么 `User` 模型则与 `Profile` 模型建立一对一的关系。

``` php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\HasOne;

class User extends Authenticatable
{
    public function profile(): HasOne
    {
        return $this->hasOne(Profile::class);
    }
}
```

查询用户资料：

``` php
class UserController extends Controller
{
    public function show($id)
    {
        $user = User::find($id);

        $profile = $user->profile;
				
				return $profile;
    }
}
```

像 ` $user->profile` 这样， profile 就可以作为 User 模型的一个属性来使用，其他形式的关联也一样，在那个模型中定义了关联关系，那就可以作为该模型的一个属性直接使用。

相对应的，一条用户资料属于某一个用户：

``` php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Profile extends Model
{
    use HasFactory;

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

## 一对多

比如在一个博客项目中，一个用户（User）可以发布多篇文章（Post），那么 `User` 模型和 `Post` 模型可以建立一对多的关系：

``` php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\HasMany;

class User extends Authenticatable
{
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class);
    }
}
```

但是一篇文章则属于某一个作者：

``` php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Post extends Model
{
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

## 多对多

一个常见的应用场景就是系统的角色（Role）权限（Permission）功能，比如一个用户可以有多个角色（一个用户既可以是管理员，也可以是作者），而一个角色也可以属于多个用户（管理员可以有多个人），要保存它们之间的关系，则需要第三张数据表 `role_user`（一般按模型的首字母顺序） ，当然也可以是其他命名方式，参考文档学习（自定义中间表模型 https://learnku.com/docs/laravel/9.x/eloquent-relationships/12252#c1c3db）：

```shell
php artisan make:migration create_role_user_table
```

编辑 `create_role_user_table`：

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('role_user', function (Blueprint $table) {
            $table->unsignedBigInteger('user_id');
            $table->unsignedBigInteger('role_id');
        });
    }
    public function down()
    {
        Schema::dropIfExists('role_user');
    }
};
```

定义关联关系：

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class User extends Authenticatable
{
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }
}
```

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class Role extends Model
{
    public function uses(): BelongsToMany
    {
        return $this->belongsToMany(User::class);
    }
}
```

## 多态关系

文档比较详细，一对一多态（morphOne ）和一对多多态（morphMany ），逻辑是一样的，而多对多多态关联类似多对多关联，需要增加一张数据表来保存关联数据。

## 保存数据

一对一（hasOne）、一对多（hasMany）、一对一多态（morphOne ）和一对多多态（morphMany ）等，可使用 `save()`，`create()` 方法：

```php
class UserController extends Controller
{
    public function profile(Request $request)
    {
        // 做法 1
        $user = User::find(1);

        $user->profile()->create([
            'gender' => '男',
            'bio' => '哈哈~'
        ]);


        // 做法 2
        $user = User::find(1);

        $profile = new Profile([
            'gender' => '男',
            'bio' => '哈哈~'
        ]);

        $user->profile()->save($profile);

        // 做法 3
        $user = User::find(1);
        $profile = new Profile();
        $profile->gender = '男';
        $profile->bio = '哈哈~';
        $profile->user()->associate($user);
        $profile->save();
    }
}
```

多对多（belongsToMany）、多对多多态（morphToMany）使用 `attach` 方法：

```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function store(Request $request)
    {
        $user = $request->user();

        $post = $user->post()->create([
            'title' => $request->input('title'),
            'body' => $request->input('body')
        ]);

        $post->tags()->attach($request->input('tagIds'));

        return $post;
    }
}
```

Demo：https://github.com/hefengbao/laravel-demo