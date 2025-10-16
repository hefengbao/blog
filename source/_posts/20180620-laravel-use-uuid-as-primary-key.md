---
title: Laravel 用 UUID 做主键
date: 2018-06-20 17:17:17
tags:
  - laravel
  - uuid
categories: Laravel
---
> Laravel version 5.6.*

Laravel 项目用 UUID 做主键, 可以用 `Str::uuid` 和 `Str::orderedUuid` 方法生成，但是当前版本直接使用 `Str::orderedUuid` 方法会报错，需要安装 `moontoast/math` 扩展包：

```shell
composer require moontoast/math
```

我遇到的另一个问题是，查询数据时， 结果中 ID (主键) 会自动转化为整数，发生的原因：

```php
<?php

namespace Illuminate\Database\Eloquent;

abstract class Model implements ArrayAccess, Arrayable, Jsonable, JsonSerializable, QueueableEntity, UrlRoutable{

	...
	
	/**
     * The "type" of the auto-incrementing ID.
     *
     * @var string
     */
    protected $keyType = 'int';
	
	...
}
```

由于主键默认设置为 `int` 类型，所以会自动转换。

解决办法，在我们的 模型 文件中，重写该属性：

```php
  protected $keyType = 'string';
```