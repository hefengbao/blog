---
title: " Jetpack Compose 入门：AlertDialog"
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
`AlertDialog` 是 material3 组件 `androidx.compose.material3.AlertDialog`, 用于显示对话框。

`onDismissRequest` : 点击 AlertDialog 之外的屏幕，一般情况下是 AlertDialog 消失；

`confirmButton` 确认按钮;

`dismissButton` 取消按钮；

`title` 标题；

`text` 内容区域，这里面可以放置 `Text` 、`TextField` 等各种组件，而不仅仅是 `Text`;


示例代码：

```kotlin
@Composable
fun AlertDialogScreen(
    modifier: Modifier = Modifier
) {
    var showDialog by remember { mutableStateOf(false) }

    Button(onClick = { showDialog = true }) {
        Text(text = "显示对话框")
    }

    if (showDialog){
        AlertDialog(
            modifier = modifier,
            onDismissRequest = { showDialog = false },
            confirmButton = {
                Button(onClick = { showDialog = false }) {
                    Text(text = "确认")
                }
            },
            dismissButton = {
                TextButton(onClick = { showDialog = false }) {
                    Text(text = "取消")
                }
            },
            icon = {
                Icon(imageVector = Icons.Default.PrivacyTip, contentDescription = null)
            },
            title = {
                Text(text = "用户协议")
            },
            text = {
                Column {
                    Text(
                        text = """
                        1、关于我们；
                        2、账号安全；
                        3、隐私政策
                        4、生效时间
                    """.trimIndent()
                    )
                    
                    Row(
                        verticalAlignment = Alignment.CenterVertically
                    ) {
                        Checkbox(checked = false, onCheckedChange = {})
                        Text(text = "请阅读并同意用于协议")
                    }
                }
            },
        )
    }
}
```

Demo：[https://github.com/hefengbao/jetpack-compose-demo](https://github.com/hefengbao/jetpack-compose-demo) 