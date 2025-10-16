---
title: "Parameter type must not include a type variable or wildcard: java.util.Map<java.lang.String, ? extends okhttp3.RequestBody>"
date: 2019-04-20 17:17:17
tags:
  - android
  - retrofit
categories: Android
---
Android (kotlin 开发) 应用访问接口，出现如下错误： 

```bash
java.lang.IllegalArgumentException: Parameter type must not include a type variable or wildcard: java.util.Map<java.lang.String, ? extends okhttp3.RequestBody> (parameter #1)
        for method StoryService.addStory
```

接口：

```kotlin
@Multipart
@POST("story")
fun addStory(@PartMap params: Map<String, RequestBody>): Call<Story>
```

解决办法，在 `RequestBody` 前添加注解 `@JvmSuppressWildcards` ：

```kotlin
@Multipart
@POST("story")
fun addStory(@PartMap params: Map<String, @JvmSuppressWildcards RequestBody>): Call<Story>
```



参考：

<https://stackoverflow.com/questions/45785874/multipart-request-with-retrofit-partmap-error-in-kotlin-android>