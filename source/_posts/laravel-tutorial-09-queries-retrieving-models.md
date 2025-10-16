---
title: Laravel 入门：09-查询构造器 & 检索模型
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
Laravel 的查询构造器提供了一种方便的链式方法，让我们方便的编写数据库查询。需要基本的数据库知识作为基础，才能更好的理解相关的内容。

比如要查询  id 为 1 的 user，相应的 sql 语句：

```sql
select * from users where id = 1
```

如果使用原生 PHP ，则要连接数据库、查询、关闭数据库连接等一系列操作，在 Laravel 框架中就不用这么麻烦了，使用 `DB ` facade 提供的相关方法就可以获取所需的数据库连接：

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

class UserController extends Controller
{
    public function index()
    {
        $user = DB::table('users')->find(1);
        // 如果 users 数据表不是存在设置的默认数据库中，则应指定数据库连接
        $user = DB::connection('mysql')->table('users')->find(1);

        return $user;
    }
}
```

在实际的开发过程中，大多数情况下，* 对数据库的操作是通过模型（model）来完成的 *，上面的代码相当于：

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
        $user = User::find(1);

        return $user;
    }
}
```

这便是所谓的模型检索。

查询构造器提供的方法都可用于模型，知识点比较多，结合文档、项目不断的理解，找到最优的使用方法。

文档已足够详细，这里就不赘言了。

参考文档：

查询构造器：https://learnku.com/docs/laravel/9.x/queries/12246

检索模型：https://learnku.com/docs/laravel/9.x/eloquent/12251#d66211

Demo：https://github.com/hefengbao/laravel-demo