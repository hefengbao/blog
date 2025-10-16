---
title: Laravel 保存 json 数据不转义汉字
date: 2023-12-14 17:17:17
tags:
  - laravel
  - laravel-json
categories: Laravel
---
下面的例子，`tags` 字段使用 `json` 格式保存标签，定义属性转换（`cast`）：

```php
class Post extends Model
{
    use HasFactory;

    protected $fillable = ['title','body','tags'];

    protected $casts = [
        'tags' => 'array,
    ];
}
```

保存数据：

```php
$data = [
	'title' => '这是测试',
	'body' => '测试内容',
	'tags' => [
		'标签1',
		'标签2'
	]
];

Post::create($data);
```

无论查看数据库，还是以接口返回 `json` 格式的数据，会看到 `tags` 属性的值类似 `\uXXXX` ，很不直观。

解决办法，自定义 `Cast` ,例如:

```shell
php artian make:cast ChineseArray
```

```php
class ChineseArray implements CastsAttributes
{
    public function get(Model $model, string $key, mixed $value, array $attributes): mixed
    {
        return json_decode($value);
    }

 
    public function set(Model $model, string $key, mixed $value, array $attributes): mixed
    {
		// https://www.php.net/manual/en/json.constants.php
		// JSON_UNESCAPED_UNICODE 设置不转义
        return json_encode($value, JSON_UNESCAPED_UNICODE);
    }
}
```


```php
class Post extends Model
{
		...
    protected $casts = [
        'tags' => ChineseArray::class,
    ];
}

