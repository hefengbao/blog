---
title: Laravel 备份
date: 2018-11-14 17:17:17
tags:
  - laravel
categories: Laravel
---
> laravel 版本： 5.6.*

安装 ` spatie/laravel-backup`:

```shell
composer require spatie/laravel-backup
```

发布配置：

```shell
php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider"
```

生成配置文件 `config/backup.php`.

```shell
'source' => [
		/*
		*设置要备份的文件
		*/
		'files' => [

			/*
			 * 默认是备份整个项目文件，实际上是不必要的，一般只备份上传的文件、图片等。
			 */
			'include' => [
				base_path(),
			],

			/*
			* 配合上面的设置，去除不需要备份的目录或文件
			 */
			'exclude' => [
				base_path('vendor'),
				base_path('node_modules'),
			],
		],

		/*
		*要备份的数据库
		*/
		'databases' => [
			'mysql',
		],
	],
```

```shell
'mail' => [
            'to' => 'example@example.com',
        ],
```
这里配置接受备份通知的邮箱地址，可以参考[Laravel Mail 配置]( https://www.hellocode.wang/article/laravel-mail-config-Fjydufmk) 配置邮箱。

接下来，在 `config/database.php` 配置数据库备份的相关信息，文档 https://docs.spatie.be/laravel-backup/v5/installation-and-setup#dumping-the-database  ：

```shell
//config/database.php

'connections' => [
	'mysql' => [
		'driver'    => 'mysql'
		...,
		'dump' => [
		   'dump_binary_path' => '/path/to/the/binary', // only the path, so without `mysqldump` or `pg_dump`
		   'use_single_transaction',
		   'timeout' => 60 * 5, // 5 minute timeout
		   'exclude_tables' => ['table1', 'table2'],
		   'add_extra_option' => '--optionname=optionvalue', 
		]  
	],
```

其中 ` dump_binary_path`  指 `mysqldump` 等所在的目录，在 Linux 系统中，可以通过 `which mysqldump` 命令查看。

示例：

```shell
'connections' => [
	'mysql' => [
		'driver'    => 'mysql'
		...,
		'dump' => [
		  'dump_binary_path' => '/usr/bin', 
                'use_single_transaction' => true,
                'timeout' => 60 * 5, 
		]  
	],
```

运行备份：

```shell
php artisan backup:run
```

生成的备份文件位于 `storage/app/Laravel` 目录下。


参考：
https://docs.spatie.be/laravel-backup/v5/introduction
https://www.jianshu.com/p/39457bc6ca22