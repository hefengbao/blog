---
title: Jetpack Compose 入门：获取 Context 、Lifecycle、UriHandler 等
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
## 获取 Context

```kotlin
val context = LocalContext.current
```

## 获取 Lifecycle

```kotlin
 val lifecycle = LocalLifecycleOwner.current.lifecycle
```

## 获取 UriHandler 

```kotlin
val uriHandler = LocalUriHandler.current
```

打开网址： 

```kotlin
 uriHandler.openUri("https://www.8ug.icu")
```

## 获取软键盘  SoftwareKeyboardController

```kotlin
val keyboard = LocalSoftwareKeyboardController.current
```

隐藏软键盘：

```kotlin
keyboard?.hide()
```

## 其他可用的

```kotlin
LocalAccessibilityManager.current
LocalClipboardManager.current
LocalDensity.current
LocalFocusManager.current
LocalFontFamilyResolver.current
LocalHapticFeedback.current
LocalInputModeManager.current
LocalLayoutDirection.current
LocalTextInputService.current
LocalPlatformTextInputPluginRegistry.current
LocalTextToolbar.current
LocalViewConfiguration.current
LocalWindowInfo.current
```