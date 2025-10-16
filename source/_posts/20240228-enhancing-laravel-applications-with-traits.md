---
title: 用 Traits 增强 Laravel 应用程序
date: 2024-02-28 17:17:17
tags:
  - laravel
  - php-trait
categories: Laravel
---
`Traits ` 用来解决代码复用问题。

通常把 `Traits` 放在 `app/Models/Traits` 目录下。

大概框架如下：

```php
<?php

namespace App\Models\Traits;

trait HasUuid
{
    public static function booted()
    {
        ...
    }

}
```

完整的 UUID trait 示例：

```php
<?php

namespace App\Models\Traits;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Str;

trait HasUuid
{
    public function getIncrementing(): bool
    {
        return false;
    }

    public function getKeyType(): string
    {
        return 'string';
    }

    public static function booted()
    {
        static::creating(function (Model $model) {
            // Set attribute for new model's primary key (ID) to an uuid.
            $model->setAttribute($model->getKeyName(), Str::uuid()->toString());
        });
    }
}
```

原文：

https://dcblog.dev/enhancing-laravel-applications-with-traits-a-step-by-step-guide