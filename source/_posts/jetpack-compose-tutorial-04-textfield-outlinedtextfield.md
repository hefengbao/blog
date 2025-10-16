---
title: Jetpack Compose 入门： TextField & OutlinedTextField
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
compose 版本：

https://developer.android.google.cn/jetpack/androidx/releases/compose?hl=zh-cn

> implementation platform('androidx.compose:compose-bom:2023.05.00')

**TextField** 是 `androidx.compose.material3.TextField`  中的控件，**OutlinedTextField** 是 `androidx.compose.material3.OutlinedTextField`中的控件，用来编辑文本等。

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun TextFieldScreen(
    modifier: Modifier = Modifier
) {
    var account by rememberSaveable { mutableStateOf("") }
    var password by rememberSaveable { mutableStateOf("") }

    Column(
        modifier = modifier.fillMaxWidth(),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        TextField(
            value = account,
            onValueChange = { account = it},
            label = {
                Text(text = "账号")
            }
        )
        TextField(
            value = password,
            onValueChange = { password = it },
            label = {
                Text(text = "密码")
            },
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password),
            visualTransformation =  PasswordVisualTransformation('*')
        )
        TextField(value = "", onValueChange = {}, enabled = false)
        OutlinedTextField(
            value = "",
            onValueChange = {},
            maxLines = 15,
            placeholder = {
                Text(text = "请输入...")
            }
        )
    }
}

@Preview
@Composable
private fun TextFieldScreenPreview() {
    TextFieldScreen()
}
```

知识点：

1、状态管理，文档：https://developer.android.google.cn/jetpack/compose/state?hl=zh-cn ，在上面的示例代码中， ` var account by rememberSaveable { mutableStateOf("") }` 用来初始化和保存账号数据，如果是编辑的话，可能就是这样：` var name by rememberSaveable { mutableStateOf("8ug_icu") }`。

2、`label` 设置标签，`placeholder` 设置提示。

3、`enabled` 设置是否可编辑，实际使用中，也是通过状态管理来处理逻辑：

```kotlin
  var enabled by rememberSaveable { mutableStateOf(false) }
	
	// 校验逻辑
	
 enabled = true
```

4、密码输入框：

```kotlin
keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password),
visualTransformation = PasswordVisualTransformation('*')
```

示例代码： https://github.com/hefengbao/jetpack-compose-demo.git