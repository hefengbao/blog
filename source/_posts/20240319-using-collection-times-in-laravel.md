---
title: " Laravel 的 Collection::times() 使用"
date: 2024-03-19 17:17:17
tags:
  - laravel
  - laravel-collection
categories: Laravel
---
Collection:：times（）允许您通过运行指定次数的回调来创建新的Collection。

```php
use Illuminate\Support\Collection;
use Illuminate\Support\Str;
 
$randomStrings = Collection::times(
    number: 10,
    callback: fn (): string => Str::random(8),
);
 
// $randomStrings is now a Collection with 10 random strings:
// [
//     "aBcDeFgH",
//     "iJkLmNoP",
//     "qRsTuVwX",
//     and so on...
// ]
```

正如我们在上面的例子中看到的，该方法采用两个参数：

1. number-运行回调的次数。

2. callback-每次运行以在集合中生成新项的回调。

回调还接受当前迭代次数作为参数。如果您需要使用当前迭代次数来生成项目，这将非常有用。

```php
use Illuminate\Support\Collection;
 
$intervals = Collection::times(
    number: 10,
    callback: fn (int $index): int => $index * 15,
);
 
// $intervals is now a Collection with 10 intervals:
// [
//     15,
//     30,
//     45,
//     and so on...
// ]
```


来自：
https://ashallendesign.co.uk/blog/using-collection-times-in-laravel