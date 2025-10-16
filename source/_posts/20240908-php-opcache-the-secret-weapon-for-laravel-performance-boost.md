---
title: PHP OPCache：Laravel性能提升的秘密武器
date: 2024-09-08 17:17:17
tags:
  - Opcache
categories: PHP
---
## OPCache 优化原理

- 编译缓存：当执行 PHP 脚本时，OPCache 会将编译后的字节码存储在内存中。
- 内存存储：对同一脚本的后续请求由内存提供，避免了磁盘 I/O 和编译开销。
- 自动失效：检测到更改时，OPCache 会自动失效并重新编译脚本。

## 使用 OPCache 的好处

- 减少加载时间：减少加载和执行 PHP 脚本所需的时间。
- 降低 CPU 使用率：通过提供预编译脚本来减少 CPU 负载。
- 改进的可扩展性：增强了使用相同服务器资源处理更多请求的能力。
- 成本效益：通过优化资源使用来降低服务器成本。

## 安装和启用 OPCache

### 1. 安装 OPCache

使用 `php -m ` 命令可查看有没有安装 OPCache 模块：

```shell
php -m 
```

如果未安装，参考如下为 php8.3 版本的命令，下文中都是基于 php8.3 版本:

```php
sudo apt install php8.3-opcache
```

### 2. 开启 OPCache

需要在 `php.ini` 文件中配置，可通过 `php --ini` 命令查看 `php.ini` 文件的位置：

```shell
php --ini
```

![](https://hefengbao.github.io/assets/images/202408281559889.png)

在 `php.ini` 文件中添加：

```shell
; Enable OPCache extension
zend_extension=opcache.so

; Recommended OPCache settings
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.revalidate_freq=2
opcache.fast_shutdown=1
opcache.enable_cli=1
opcache.validate_timestamps=1
opcache.file_cache=/path/to/cache
opcache.file_update_protection=2
opcache.max_wasted_percentage=5
```

### 3.了解 OPCache 设置

- `opcache.enable=1`: Enables OPCache for PHP.
- `opcache.memory_consumption=128`: Allocates 128MB of memory for storing precompiled scripts.
- `opcache.interned_strings_buffer=8`: Allocates 8MB for interned strings in memory.
- `opcache.max_accelerated_files=10000`: Sets the maximum number of files that can be cached.
- `opcache.revalidate_freq=2`: Sets the frequency (in seconds) for checking script timestamps to see if they have been updated.
- `opcache.fast_shutdown=1`: Enables fast shutdown to reduce memory usage when scripts are terminated.
- `opcache.enable_cli=1`: Enables OPCache for the CLI version of PHP. This is useful for speeding up long-running PHP scripts executed from the command line.
- `opcache.validate_timestamps=1`: When enabled, OPCache checks the timestamps of files to see if they have been updated. If a file is updated, it is recompiled. By default, it is enabled.
- `opcache.file_cache=/path/to/cache`: Specifies the directory where OPCache should store cached scripts if they cannot be stored in shared memory.
- `opcache.file_update_protection=2`: Ensures that cached scripts are not accessed until at least this many seconds have passed since they were last modified.
- `opcache.max_wasted_percentage=5`: The percentage of "wasted" memory (due to fragmentation, etc.) that OPCache can tolerate before it triggers a restart of the cache to reclaim memory.


### 4. 为 Laravel 配置 OPCache

为了优化Laravel的OPCache，微调配置参数至关重要。以下是一些推荐的设置：

```shell
; Increase memory consumption to handle more scripts
opcache.memory_consumption=256

; Higher number of interned strings buffer for better performance
opcache.interned_strings_buffer=16

; Max number of scripts that can be cached
opcache.max_accelerated_files=20000

; Frequency of file status checks (in seconds)
opcache.revalidate_freq=60

; Enable file cache for scripts that can\'t be stored in shared memory
opcache.file_cache=/tmp

; Enable optimization for faster execution
opcache.opt_debug_level=0
```

## 创建 Laravel 预加载脚本

预加载允许您在启动时将一组PHP文件加载到内存中，使其可用于所有请求。这可以进一步减少常用类的加载时间。

### 1.创建预加载脚本

在 Laravel 应用程序的根目录中创建一个 `preload.php 文件。

```php
<?php

require_once __DIR__ . '/vendor/autoload.php';

class Preloader
{
    private array $ignores = [];

    private static int $count = 0;

    private array $paths;

    private array $fileMap;

    public function __construct(string ...$paths)
    {
        $this->paths = $paths;

        // We'll use composer's classmap
        // to easily find which classes to autoload,
        // based on their filename
        $classMap = require __DIR__ . '/vendor/composer/autoload_classmap.php';

        $this->fileMap = array_flip($classMap);
    }

    public function paths(string ...$paths): Preloader
    {
        $this->paths = array_merge(
            $this->paths,
            $paths
        );

        return $this;
    }

    public function ignore(string ...$names): Preloader
    {
        $this->ignores = array_merge(
            $this->ignores,
            $names
        );

        return $this;
    }

    public function load(): void
    {
        // We'll loop over all registered paths
        // and load them one by one
        foreach ($this->paths as $path) {
            $this->loadPath(rtrim($path, '/'));
        }

        $count = self::$count;

        echo "[Preloader] Preloaded {$count} classes" . PHP_EOL;
    }

    private function loadPath(string $path): void
    {
        // If the current path is a directory,
        // we'll load all files in it
        if (is_dir($path)) {
            $this->loadDir($path);

            return;
        }

        // Otherwise we'll just load this one file
        $this->loadFile($path);
    }

    private function loadDir(string $path): void
    {
        $handle = opendir($path);

        // We'll loop over all files and directories
        // in the current path,
        // and load them one by one
        while ($file = readdir($handle)) {
            if (in_array($file, ['.', '..'])) {
                continue;
            }

            $this->loadPath("{$path}/{$file}");
        }

        closedir($handle);
    }

    private function loadFile(string $path): void
    {
        // We resolve the classname from composer's autoload mapping
        $class = $this->fileMap[$path] ?? null;

        // And use it to make sure the class shouldn't be ignored
        if ($this->shouldIgnore($class)) {
            return;
        }

        // Finally we require the path,
        // causing all its dependencies to be loaded as well
        require_once($path);

        self::$count++;

        echo "[Preloader] Preloaded `{$class}`" . PHP_EOL;
    }

    private function shouldIgnore(?string $name): bool
    {
        if ($name === null) {
            return true;
        }

        foreach ($this->ignores as $ignore) {
            if (strpos($name, $ignore) === 0) {
                return true;
            }
        }

        return false;
    }
}

(new Preloader())
    ->paths(__DIR__ . '/vendor/laravel')
    ->ignore(
        \Illuminate\Filesystem\Cache::class,
        \Illuminate\Log\LogManager::class,
        \Illuminate\Http\Testing\File::class,
        \Illuminate\Http\UploadedFile::class,
        \Illuminate\Support\Carbon::class,
    )
    ->load();
```

### 2.更新 PHP 配置

将预加载脚本添加到 PHP 配置（`php.ini`）中。

```shell
; Path to the preload script
opcache.preload=/path/to/your/laravel/project/preload.php
opcache.preload_user=www-data
```

### 3.重新启动 Web 服务器

修改php.ini文件后，重新启动 web 服务器使更改生效。

Apache:

```shell
sudo systemctl restart apache2
```

Nginx with PHP-FPM:

```shell

sudo systemctl restart php8.3-fpm
sudo systemctl restart nginx
```

### 4.检查 OPCache 状态

要验证 OPCache 是否按预期工作并检查预加载细节，请在 Laravel 项目的根目录中创建一个名为 `opache.php` 的 PHP 脚本，其内容如下：

```php
<?php

$status = opcache_get_status();

print_r($status);
```

运行脚本：

```shell
php opcache.php
```

输出将提供有关 OPCache 使用、配置和预加载脚本的详细信息。查找统计部分以验证预加载是否按预期工作。

## 刷新 OPCache

有这两种方法：

### 1. 重启 PHP or Web 服务器:
- 最直接的清空 OPCache 方法
- 可能打断即将到来的请求

### 2. 使用 PHP 脚本

```php
opcache_reset();

echo 'OPCache flushed successfully.';
```

## 监控 OPCache

使用诸如 `opcache-gui` 、 `opcache-status` 之类的工具。

### 使用 `opcache-gui`

1. 安装 `opcache-gui`:

```bash
git clone https://github.com/amnuts/opcache-gui.git /var/www/html/opcache-gui
```

2. 访问 `opcache-gui`: Open your browser and navigate to http://your-domain/opcache-gui.

### 使用 `opcache-stat`

1. 安装 `opcache-status`

```bash
git clone https://github.com/rlerdorf/opcache-status.git /var/www/html/opcache-status
```

2. 访问 `opcache-status`: Open your browser and navigate to http://your-domain/opcache-status.


参考：

https://qirolab.com/posts/php-opcache-the-secret-weapon-for-laravel-performance-boost