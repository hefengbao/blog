---
title: 在 Laravel 中使用 PHP Enums 存储附加信息
date: 2023-10-05 17:17:17
tags:
  - laravel
  - php-enum
categories: Laravel
---
## 定义 Enum

```php
<?php

namespace App\Enums;

enum SocialNetwork: string
{
   case FACEBOOK = 'facebook';
   case TWITTER = 'twitter';
   case YOUTUBE = 'youtube';
}
```

## 在模型（Model）中转换属性（Casting）

```php
<?php

namespace App\Models;

use App\Enums\SocialNetwork;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class SocialProfile extends Model
{
    use HasFactory;

    protected $casts = [
        'social_network' => SocialNetwork::class,
    ];
}
```

这意味着您可以与枚举实例交互，而不仅仅是与数据库中的字符串值交互，还可以访问enums方法。我们稍后再谈。

对这句话**这意味着您可以与枚举实例交互，而不仅仅是与数据库中的字符串值交互**的示例：

```php
$profile = new SocialProfile()
$profile->social_network = SocialNetwork::FACEBOOK;
profile->save();
```

如果不这么做，那么代码则要这么写：

```php
$profile = new SocialProfile()
$profile->social_network = SocialNetwork::FACEBOOK->value;
$pro
```

## 验证 Enum

```php
use App\Enums\SocialNetwork;
use Illuminate\Validation\Rules\Enum;

$request->validate([
    'social_network' => [new Enum(SocialNetwork::class)],
]);
```

## 根据 Enum 存储附加信息

除了定义选项外，枚举还允许使用其他方法。您甚至可以在方法中使用枚举。

我想要一种存储每个枚举选项的有效域名的方法，所以我创建了一个使用枚举值的匹配语句，并返回了一个有效域名数组。

```php
<?php

namespace App\Enums;

enum SocialNetwork: string
{
   case FACEBOOK = 'facebook';
   case TWITTER = 'twitter';
   case YOUTUBE = 'youtube';

   public function domains(): array
   {
        return match ($this) {
            SocialNetwork::FACEBOOK => [
                'facebook.com',
                'fb.me',
                'fb.com',
            ],
            SocialNetwork::TWITTER => [
                'twitter.com',
                't.co',
            ],
            SocialNetwork::YOUTUBE => [
                'youtube.com',
                'youtu.be',
            ],
        };
   }
}
```

另一个有用的地方是，如果我将来需要添加一个新的社交网络，我只有一个地方可以添加定义和域。我可以很容易地在枚举中添加 Instagram 作为选项，无论我在哪里使用枚举，代码都应该更新。

## 在自定义验证规则中使用枚举（Enum）

```php
<?php

namespace App\Rules;

use App\Enums\SocialNetwork;
use Closure;
use Illuminate\Contracts\Validation\ValidationRule;
use Illuminate\Support\Str;

class SocialNetworkRule implements ValidationRule
{
    /**
     * Get the allowed domains from the SocialNetwork enum
     * and ensure at least one matches the value.
     *
     * @param  \Closure(string): \Illuminate\Translation\PotentiallyTranslatedString  $fail
     */
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        $allowedDomains = array_map(
            fn (SocialNetwork $socialNetwork) => $socialNetwork->domains(),
            SocialNetwork::cases()
        );

        $domains = collect($allowedDomains)
            ->flatten()
            ->all();

        if (! Str::contains($value, $domains)) {
            $fail('The :attribute field must be a valid social network.');
        }
    }
}
```

## 从 url 获取社交网络

```php
use Illuminate\Support\Str;

public function getNetwork(string $link): string|null
{
    foreach (SocialNetwork::cases() as $case) {
        if (Str::contains($link, $case->domains())) {
            return $case->value;
        }
    }

    return null;
}
```

原文 [Using PHP Enums in Laravel to store additional information](https://www.csrhymes.com/2023/07/04/using-php-enums-in-laravel.html)