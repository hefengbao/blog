---
title: Laravel 集合中使用 ensure() 方法验证数据类型
date: 2024-09-07 17:17:17
tags:
  - laravel
  - laravel-collection
categories: Laravel
---
`ensure()` 方法用于验证集合中的元素类型，验证不通过抛出 `UnexpectedValueException` 异常。

示例一：

```php
use Illuminate\Support\Collection;

$collection = collect([1, 2, 3, 4, 5]);

$validatedCollection = $collection->ensure(function ($item) {
    return is_int($item);
});


print_r($validatedCollection->all());
```

示例二：

```php
use Illuminate\Support\Collection;

$numbers = collect([1, 2, 3, 'four']); // Collection with mixed types

try {
    $numbers->ensure('int'); // Ensure all items are integers
} catch (UnexpectedValueException $e) {
    echo 'Error: Collection contains a non-integer value.';
    // Handle the exception as needed (e.g., log the error, provide user feedback)
}
```

示例三，自定义类型验证：

```php
class User {}

$users = collect([new User(), new User()]);

try {
    $users->ensure(function ($item) {
        return $item instanceof User;
    });
} catch (Exception $e) {
    echo $e->getMessage();
}
```

示例四，多类型验证：

```php
$data = collect([1, 'hello', new User]);

$data->ensure([int::class, string::class, User::class]); // Valid collection
```

参考：

https://qirolab.com/posts/data-type-validation-in-laravel-collections-with-the-ensure-method