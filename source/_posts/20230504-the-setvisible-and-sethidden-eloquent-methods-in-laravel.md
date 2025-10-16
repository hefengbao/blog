---
title: Laravel 中的 setVisible 和 setHidden 模型方法
date: 2023-05-04 17:17:17
tags:
  - laravel
categories: Laravel
---
你可以使用 `hidden` 属性来指定哪些属性在模型转为 JSON 时隐藏：

```php
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = ['api_token'];
}
```
当你把模型转换为 `array` 或 `json` 时，将会隐藏 `api_token` 属性：

```php
$user = User::find(1);

return $user->toArray();
/*
[
    "id" => 1,
    "name" => "Amit Merchant",
    "email" => "example@example.com",
]
*/
```

如果向动态的显示或隐藏某（几）个属性，请看下面的两个方法：

### `setVisible` 方法

```php
$user = User::find(1);

return $user->setVisible(['name', 'email'])->toArray();

/*
[
    "name" => "Amit Merchant",
    "email" => "
]
*/
```

### `setHidden` 方法

```php
$user = User::find(1);

return $user->setHidden(['api_token'])->toArray();
/*
[
    "id" => 1,
    "name" => "Amit Merchant",
    "email" => "example@example.com",
]
*/
```

参考：

https://www.amitmerchant.com/the-setvisible-and-sethidden-eloquent-methods-in-laravel-9x/