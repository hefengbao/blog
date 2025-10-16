---
title: "Jetpack Compose : 使用 LinkAnnotation 替代 ClickableText"
date: 2024-08-24 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
文本点击事件：

```kotlin
ClickableText(
    text = AnnotatedString("Open Link"),
    onClick = { offset ->
        // handle click
    }
)
```

使用 `buildAnnotatedString` 实现：

```kotlin
Text(buildAnnotatedString {
    append("View my ")
    withLink(LinkAnnotation.Url(url = "https://joebirch.co")) {
        append("website")
    }
})
```

默认情况下，可点击文本以下划线修饰。

可通过 `SpanStyle` 修改：

```kotlin
Text(buildAnnotatedString {
    append("View my ")
    withLink(
        LinkAnnotation.Url(
            url = "https://joebirch.co",
            style = SpanStyle(color = MaterialTheme.colorScheme.primary)
        )
    ) {
        append("website")
    }
})
```

参考：

https://joebirch.co/android/migrating-from-the-clickabletext-composable-to-linkannotation/