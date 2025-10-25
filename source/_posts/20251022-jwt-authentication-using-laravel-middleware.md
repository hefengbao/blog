---
title: Laravel 使用中间件实现 JWT 认证
date: 2025-10-22 13:55:24
tags:
  - laravel
  - jwt
categories: Laravel
permalink: 20251022-jwt-authentication-using-laravel-middleware
---
## JSON Web Tokens（JWT）

JSON Web 令牌 （JWT） 是紧凑的、URL 安全的令牌，用于在各方之间安全地传输信息作为 JSON 对象。JWT 由标头（Header）、有效负载（Payload）和签名（Signature）三部分组成，包含元数据、声明和签名，以确保完整性和真实性。

JWT token 示例：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

使用逗号分隔：

1. **Header**: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
2. **Payload**: eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ
3. **Signature**: SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c


## Laravel 中的 JSON Web 令牌实现

这里使用 `firebase/php-jwt` :

```shell
composer require firebase/php-jwt
```


```shell
php artisan make:middleware JsonWebTokenAuthentication
```

```php
<?php
 
namespace App\Http\Middleware;
 
use Closure;
use Illuminate\Http\Request;
use Firebase\JWT\JWT;
use Firebase\JWT\SignatureInvalidException;
use Firebase\JWT\BeforeValidException;
use Firebase\JWT\ExpiredException;
use DomainException;
use InvalidArgumentException;
use UnexpectedValueException;
 
class JsonWebTokenAuthentication
{
    public function handle(Request $request, Closure $next)
    {
        $bearerToken = $request->bearerToken() ?? '';
        $key = config('PATH_TO_YOUR_KEY');
 
        try {
            $decoded = JWT::decode($bearerToken, $key);
        } catch (InvalidArgumentException $e) {
            // provided key/key-array is empty or malformed.
        } catch (DomainException $e) {
            // provided algorithm is unsupported OR
            // provided key is invalid OR
            // unknown error thrown in openSSL or libsodium OR
            // libsodium is required but not available.
        } catch (SignatureInvalidException $e) {
            // provided JWT signature verification failed.
        } catch (BeforeValidException $e) {
            // provided JWT is trying to be used before "nbf" claim OR
            // provided JWT is trying to be used before "iat" claim.
        } catch (ExpiredException $e) {
            // provided JWT is trying to be used after "exp" claim.
        } catch (UnexpectedValueException $e) {
            // provided JWT is malformed OR
            // provided JWT is missing an algorithm / using an unsupported algorithm OR
            // provided JWT algorithm does not match provided key OR
            // provided key ID in key/key-array is empty or invalid.
        }
 
        return $next($request);
    }
}
```


[JWT authentication using Laravel middleware - HiBit](https://www.hibit.dev/posts/169/jwt-authentication-using-laravel-middleware)