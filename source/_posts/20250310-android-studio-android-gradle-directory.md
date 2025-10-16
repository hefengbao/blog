---
title: Android Studio 修改 .android 和 .gradle 目录
date: 2025-03-10 17:17:17
tags:
  - AndroidStudio
categories: AndroidStudio
---
Windows 系统安装 Android Studio 后，`.gradle` 用来保存下载的 Gradle 依赖的包，`.android` 用来保存模拟器，都比较占地方。默认位置为 `C:\Users\<用户名>\.gradle` 、`C:\Users\<用户名>\.android` ,如果 C 盘空间不够用，可考虑把这两个目录移到其他盘。首先把这两个目录移到其他盘，我这里移到了 `D:\Program\AndroidDev` 目录下。

![](https://hefengbao.github.io/assets/images/202503081544919.png)

添加环境变量，`.gradle` 需要添加的是 `GRADLE_USER_HOME `:

![](https://hefengbao.github.io/assets/images/202503081546644.png)

`.android` 需要添加的是 `ANDROID_SDK_HOME `:

![](https://hefengbao.github.io/assets/images/202503161838719.png)