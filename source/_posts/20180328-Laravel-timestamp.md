---
title: Laravel timestamp
date: 2018-03-28 17:17:17
tags:
  - laravel
categories: Laravel
---
博客数据库中有个 `published_at` 字段，主要是为了实现延迟发布的功能，但发现每次查看博文时，`published_at` 都会自动更新，打开博文时，唯一做的更新操作是为了统计浏览量： ` $post->increment('view_count',1);`， SQL 如下：

```shell
update `posts` set `view_count` = `view_count` + 1, `updated_at` = '2017-08-17 18:30:33' where `id` = '1' and `posts`.`deleted_at` is null
```

并没有做更新 `published_at` 的操作呀？

后来去数据库查看字段设置，发现  `published_at` 默认勾选了 `根据当前时间戳更新`，而 `created_at` 和 `update_at` 却默认没有勾选`根据当前时间戳更新`，而 `created_at` 和 `update_at` 是同过 `$this->timestamps()` 生成的，于是查看 `timestamps()` 源码：

```php
public function timestamps()
{
	$this->timestamp('created_at')->nullable();

	$this->timestamp('updated_at')->nullable();
}	
```

原来这俩个字段默认设置允许为空，而我建立数据表是这样写的`$table->timestamp('published_at');` ,默认不为空，于是做修改 `  $this->timestamp('published_at')->nullable();` ,解决问题。

可知， MySQL 中，timestamp 类型的字段，如果不允许为空，那么 mysql 会自动维护，所有的更新操作都会引起该字段值的改变。