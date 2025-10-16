---
title: widget has an unresolved type,and thus it was upcasted to android.view.View
date: 2019-04-06 17:17:17
tags:
  - android
categories: Android
---
使用 `Kotlin` 的 `View Binding` 来绑定 `View`, 对于框架提供的控件没有任何问题，但是对自定义控件会出现如下错误：

比如，有自定义控件 `com.example.customText`

`xx.xml`

```xml
<com.example.customText
	adnroid:id='@+id/custom_text'
	...
/>
```



 如果直接使用

```
custom_text.text = 'Hello'
```



会有如下错误

```bash
widget has an unresolved type 'com.example.customText',and thus it was upcasted to 'android.view.View'
```



需要先做类型转换：

```kotlin
val customtext as customText
customtext.text = 'Hello'
```

