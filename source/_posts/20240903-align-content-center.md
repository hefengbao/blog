---
title: CSS 使用 align-content:center 实现 div 居中
date: 2024-09-03 17:17:17
tags:
  - css
categories: CSS
---

以前实现 div 居中比较方便的方法有：

```css
display: flex; 
justify-content: center; 
align-items: center;
```

```css
display: grid; 
place-content: center;
```

新实现的属性 `align-content: center;` ,浏览器支持查看[🔗](https://caniuse.com/mdn-css_properties_align-content_block_context)

```css
align-content: center;
```

示例代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div style="display: flex; justify-content: center; align-items: center; height: 200px; background-color:aqua;">
        <div style="background-color:antiquewhite; height: 100px;width: 200px;">
            display: flex
        </div>
    </div>
    <div style="display: grid; place-content: center; height: 200px; background-color: aquamarine;">
        <div style="background-color: antiquewhite; height: 100px; width: 200px;">
            display: grid
        </div>
    </div>
    <div style="align-content: center; height: 200px; background-color:blueviolet;">
        <div style="background-color:antiquewhite; height: 100px; width: 200px;  margin: 0 auto;">
            align-content: center
        </div>
    </div>
</body>
</html>
```

示例效果：

![](https://hefengbao.github.io/assets/images/202408241704411.png)

参考：

https://build-your-own.org/blog/20240813_css_vertical_center/

https://www.bilibili.com/video/BV1V7iNeWE9m