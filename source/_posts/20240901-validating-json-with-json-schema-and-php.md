---
title: " PHP 使用 JSON Schema  验证 JSON  数据"
date: 2024-09-01 17:17:17
tags:
  - laravel
  - laravel-json
categories: Laravel
---
## `json_validate()` 方法

PHP 8.3 提供了 `json_validate()` 方法, 可以用来验证是否有语法错误：

```php
$fruitsArray = [
    [
        'name' => 'Avocado',
        'fruit' => '🥑',
        'wikipedia' => 'https://en.wikipedia.org/wiki/Avocado',
        'color' => 'green',
        'rating' => 8,
    ],
    [
        'name' => 'Apple',
        'fruit' => '🍎',
        'wikipedia' => 'https://en.wikipedia.org/wiki/Apple',
        'color' => 'red',
        'rating' => 7,
    ],
    [
        'name' => 'Banana',
        'fruit' => '🍌',
        'wikipedia' => 'https://en.wikipedia.org/wiki/Banana',
        'color' => 'yellow',
        'rating' => 8.5,
    ],
    [
        'name' => 'Cherry',
        'fruit' => '🍒',
        'wikipedia' => 'https://en.wikipedia.org/wiki/Cherry',
        'color' => 'red',
        'rating' => 9,
    ],
];

if (json_validate($jsonString)) {
    echo "Valid JSON syntax.";
} else {
    echo "Invalid JSON syntax.";
}
```

PHP 8.3 之前的版本，可以使用 `symfony/polyfill-php83` 包提供的 `json_validate()` 方法：

```shell
composer require symfony/polyfill-php83
```

但是 `json_validate()` 方法只能验证是否有 JSON 语法错误，要进一步验证 JSON 数据类型等，可以使用 JSON schema：

## `swaggest/json-schema`

### 安装包

```shell
composer require swaggest/json-schema
```

### 定义 Scheme

```php
$schemaJson = <<<'JSON'
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "type": "array",
    "items" : {
        "type": "object",
        "properties": {
            "name": {
                "type": "string"
            },
            "fruit": {
                "type": "string"
            },
            "wikipedia": {
                "type": "string"
            },
            "color": {
                "type": "string"
            },
            "rating": {
                "type": "number"
            }
        }
    }
}
JSON;
```

### 验证

```php
require 'vendor/autoload.php';

use Swaggest\JsonSchema\Schema;

try {
     $schemaObject = Schema::import(
         json_decode($schemaJson),
     )->in(
         json_decode($jsonString),
     );
     echo "JSON is valid according to the schema.";

 } catch (\Swaggest\JsonSchema\Exception\ValidationException $e) {
     echo "JSON validation error: " . $e->getMessage();
 } catch (\Swaggest\JsonSchema\Exception\TypeException $e1) {
     echo "JSON validation Type error: " . $e1->getMessage();
 }
```



参考：https://dev.to/robertobutti/validating-json-with-json-schema-and-php-2b4i