---
title: Laravel 使用：在类型为 datetime-local 的 input 中显示日期
date: 2023-03-28 17:17:17
tags:
  - laravel
categories: Laravel
---

类型属性为 `datetime-local` 的HTML输入用于显示表单字段并输入日期和时间：

```html
<input type="datetime-local" >
```

在本教程中，了解如何在[ `datetime-local`  HTML  input ](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input/datetime-local)中显示Laravel模型中的日期（天、月、年、小时、分钟和秒）。

要在HTML表单的 `datatime-local` 表单中显示Laravel模型的日期（email_verified_at、created_at、updated_at…），我们首先需要确保数据库表中的列是用 `timestamp` 数据类型定义的。

在Laravel迁移中，我们使用 `timestamp()` 方法来定义时间戳列的模式：

```php
$table->timestamp('email_verified_at')->nullable(); // column email_verified_at
$table->timestamps(); // columns created_at and updated_at
```

如果您有另一个存储日期的列，则可以通过模型中的$casts属性将其属性转换为日期格式（[Carbon](https://github.com/briannesbitt/Carbon)实例）：

```php
protected $casts = [ "online_at" => "datetime" ];
```

在视图（Blade模板）中，我们可以使用 `format()` 方法将日期和时间格式化为与`datetime-local` HTML字段兼容并可用的字符串，以显示它。
例如，对于具有 `created_at` 列的 `User` 模型，我们可以使用以下语法在 `datetime-local` 字段中显示日期和时间：

```html
<input type="datetime-local" name="created_at" value="{{ $user->created_at->format('Y-m-d\TH:i:s') }}">
```

在本例中，我们使用 `format()` 方法将模型的 `created_at` 日期转换为 `Y-m-d\TH:i:s` 格式的字符串，这是HTML `datetime-local` 字段的兼容格式。`\T` 字符用于分隔字符串中的日期和时间。

要在日期类型的HTML输入中显示 `date`，兼容格式为 `Y-m-d`：

```html
<input type="date" name="online_at" value="{{ $post->online_at->format('Y-m-d') }}" >
```

提示：我们也可以通过 `$casts` 属性在模型中格式化日期，而不是在Blade视图中格式化日期：

```php
 protected $casts = [ "online_at" => "datetime:Y-m-d\TH:i:s" ];
```

原文：https://wilo-ahadi.com/posts/laravel-show-a-date-in-a-datetime-local-input