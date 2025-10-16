---
title: EditText inputType & TextField keyboardType
date: 2023-08-10 17:17:17
tags:
  - android
categories: Android
---

```kotlin
android:inputType="none" //输入普通字符

android:inputType="text" //输入普通字符

android:inputType="textCapCharacters" //输入普通字符

android:inputType="textCapWords" //单词首字母大写

android:inputType="textCapSentences"  //句子首字母大写

android:inputType="textAutoCorrect" // 自动更正

android:inputType="textAutoComplete"  //自动完成

android:inputType="textMultiLine" //多行输入

android:inputType="textImeMultiLine" //输入法多行（不一定支持）

android:inputType="textNoSuggestions" //不提示

android:inputType="textUri" //URI格式

android:inputType="textEmailAddress" //电子邮件地址格式

android:inputType="textEmailSubject" //邮件主题格式

android:inputType="textShortMessage" //短消息格式

android:inputType="textLongMessage" //长消息格式

android:inputType="textPersonName" //人名格式

android:inputType="textPostalAddress" //邮政格式

android:inputType="textPassword" //密码格式

android:inputType="textVisiblePassword" //密码可见格式

android:inputType="textWebEditText" //作为网页表单的文本格式

android:inputType="textFilter" //文本筛选格式

android:inputType="textPhonetic" //拼音输入格式

android:inputType="number" //数字格式

android:inputType="numberSigned" //有符号数字格式

android:inputType="numberDecimal" //可以带小数点的浮点格式

android:inputType="phone" //拨号键盘

android:inputType="datetime" // Datetime

android:inputType="date" //日期键盘

android:inputType="time" //时间键盘
```

在 Jetpack Compose 中设置：

```kotlin
TextField(
        modifier = modifier.fillMaxWidth(),
        value = "",
        onValueChange = {  },
        label = {
            Text(text = "密码")
        },
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password),
        visualTransformation = if (isPasswordVisible) VisualTransformation.None else PasswordVisualTransformation(
            '*'
        ),
    )
```