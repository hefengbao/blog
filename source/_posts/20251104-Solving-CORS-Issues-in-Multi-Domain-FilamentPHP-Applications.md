---
title: Solving CORS Issues in Multi-Domain FilamentPHP Applications
date: 2025-11-04 11:24:13
tags:
  - CORS
categories: Laravel
permalink: 20251104-solving-cors-issues-in-multi-domain-filamentphp-applications
---
静态资源跨域, 没有经过 Laravel 的 Middleware， 需单独配置：

```shell
location ~ \.(bmp|cur|gif|ico|jpe?g|png|svgz?|webp|pdf)$ {
    add_header Access-Control-Allow-Origin *;
}
```

[Solving CORS Issues in Multi-Domain FilamentPHP Applications](https://filamentapps.com/blog/solving-cors-issues-in-multi-domain-filamentphp-applications)