---
title: Laravel 配置额外的环境文件
date: 2024-08-27 17:17:17
tags:
  - laravel
categories: Laravel
---
在Laravel中，你可以配置加载其他环境文件而不是`.env`文件。此功能有助于测试，您可以在测试中加载 `.env.testing` 环境文件，而不是默认文件。您通常不需要使用此功能，但通过设置 `APP_ENV` 环境变量，Laravel可以检测自定义配置。

## CLI Example

此功能最直接了当的例子是运行 `Artisan` 命令或 `phpunit` 命令时使用不同的环境变量文件。

使用 `Artisan` 命令，你可以使用 `--env` 参数指定不同的 `.env` 文件或者定义 `APP_ENV`。例如，下面的例子使用 `.env.demo`:

```shell
# Set up `.env.demo`
cp .env .env.demo
echo "\nEXAMPLE_SETTING=demo" >> .env.demo
 
# Use the `demo` env
 
php artisan tinker --env=demo
 
# Or set APP_ENV
APP_ENV=demo php artisan tinker
```

如果查询到， Laravel 会加载 `.env.demo` 文件而不是 `.env` 文件。


![Example using .env.demo instead of .env](https://hefengbao.github.io/assets/images/202405271433389.png)

## 在 PHPUnit 测试中使用 `.env.testing`

Building on what we know about the Laravel framework loading a specific ENV file if it exists, running Laravel feature tests in PHPUnit will use the `.env` file by default. Using `.env` for tests and local development could quickly cause issues such as configuring a separate database for testing. You could define database connection details in `phpunit.xml`, but let's also look at setting them in `.env.testing`.

PHPUnit defines an `APP_ENV` environment variable in `phpunit.xml`, which means that Laravel looks for a `.env.testing` file when bootstrapping Feature tests because the `phpunit.xml` file defines `APP_ENV` before the Laravel framework gets bootstrapped in Feature tests:

```xml
<env name="APP_ENV" value="testing"/>
```

That means we can copy the stock `.env` file to `.env.testing` and avoid mixing the two files during testing:

```shell
cp .env .env.testing
 
echo "\nEXAMPLE_SETTING=testing" >> .env.testing
```

You can configure environment variables in `phpunit.xml`. However, I like using the `.env.testing` file to ensure a clean environment specifically for testing. It's also up to you whether you version control `.env.testing` or ignore it in `.gitignore`.

After copying the `.env` file, you can verify that `.env.testing` is loaded by adding the following to a test in your `tests/Feature` folder. Tests in the `tests/Unit` folder won't bootstrap the Laravel framework:

```php
/**
 * A basic test example.
 */
public function test_the_application_returns_a_successful_response(): void
{
    logger('Which environment file is Laravel using?', [
        'file' => $this->app->environmentFile()
    ]);
 
    $response = $this->get('/');
 
    $response->assertStatus(200);
}
```

When I run `phpunit`, I get the following log confirming that I'm using the `.env.testing` file:

```shell
[2024-05-24 00:22:42] testing.DEBUG: Which environment file is Laravel using? {"file":".env.testing"}
```

If you ignore this file in your VCS, you could add an example file `.env.testing.example` with your team's conventions or let them decide how to configure tests locally. I recommend setting system-level environment variables in CI to configure things like test databases.

Check out the [Laravel Documentation](https://laravel.com/docs/11.x/configuration#additional-environment-files) for more details on environment configuration. If you're curious how this works at the framework level, check out the [setEnvironmentFilePath](https://github.com/laravel/framework/blob/40ebbeda850cf821aca42f4e1b2dd50780d8a621/src/Illuminate/Foundation/Bootstrap/LoadEnvironmentVariables.php#L67-L76) method and [checkForSpecificEnvironmentFile](https://github.com/laravel/framework/blob/40ebbeda850cf821aca42f4e1b2dd50780d8a621/src/Illuminate/Foundation/Bootstrap/LoadEnvironmentVariables.php#L41-L58) in the Laravel framework source code.
