---
title: Jetpack Compose 入门：使用 Coil （异步）加载（网络） Image （图片）以及圆角处理
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
  - Coil
categories: JetpackCompose
---
Coil 是一个 Android 图片加载库，通过 Kotlin 协程的方式加载图片。

官方文档：[https://coil-kt.github.io/coil/README-zh/](https://coil-kt.github.io/coil/README-zh/)

首先引入 Coil 包：

```kotlin
implementation("io.coil-kt:coil:2.4.0")
implementation("io.coil-kt:coil-compose:2.4.0")
```

Coil 提供了 `AsyncImage` 控件，直接使用即可：

```kotlin
@Composable
fun ImageScreen(
    modifier: Modifier = Modifier
) {
    Column(
        modifier = modifier
            .fillMaxWidth()
            .scrollable(rememberScrollState(), Orientation.Vertical)
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        AsyncImage(
            model = "https://pic.616pic.com/ys_bnew_img/00/28/60/6p82GlZ565.jpg",
            contentDescription = null,
            modifier = modifier
                .size(100.dp)
                .clip(CircleShape),
            onState = { state ->
                when(state){
                    AsyncImagePainter.State.Empty -> {
                        // 如果图片链接不存在，这里可以设置提示
                    }
                    is AsyncImagePainter.State.Error -> {
                        // 如果加载出错，这里可先设置显示错误提示或图片
                    }
                    is AsyncImagePainter.State.Loading -> {
                        // 比如显示加载进度条等
                    }
                    is AsyncImagePainter.State.Success -> {
                        // 比如隐藏加载进度条等
                    }
                }
            },
            contentScale = ContentScale.Inside,
            alpha = 0.1f,
        )

        AsyncImage(
            model = "https://pic.616pic.com/ys_bnew_img/00/28/60/6p82GlZ565.jpg",
            contentDescription = null,
            modifier = modifier
                .size(100.dp)
                .clip(CircleShape),
            placeholder = painterResource(id = R.drawable.ic_launcher_background), // 占位符，即加载时显示的图像，可选
            error = painterResource(id = R.drawable.ic_launcher_foreground), // 加载图片出错时显示的图像，可选
            fallback = painterResource(id = R.drawable.ic_launcher_foreground), // 加载的图像链接不存在时指定要显示的图像，可选
            onLoading = {
                // 显示进度条等
            },
            onSuccess = {
                // 隐藏进度条等
            },
            onError = {
                // 隐藏进度条,错误提示等
            },
            alignment = Alignment.Center, // 图像对其位置,默认居中显示
            contentScale = ContentScale.Fit, // 图像填充模式
            alpha = 0.5f, // 设置透明度
        )
    }
}
```

Coil 加载图片时的圆角处理，使用 `Modifier` 的 `clip()` 方法，官方提供了 `CircleShape` 处理显示为圆形，还可以使用 `RoundedCornerShape(16.dp)` 指定圆角大小。

示例代码：[https://github.com/hefengbao/jetpack-compose-demo.git](https://github.com/hefengbao/jetpack-compose-demo.git)  中的 `ImageScreen.kt` 。