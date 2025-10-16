---
title: Jetpack Compose 入门：TopAppBar & BottomAppBar
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
`TopAppBar`、`MediumTopAppBar`、`LargeTopAppBar`、`CenterAlignedTopAppBar` 用于的顶部 AppBar，`BottomAppBar` 用在底部的 AppBar。TopAppBar 主要是三部分，从左至右依次是：`navigationIcon`、`title` 、`actions`，BottomAppBar 一种是自己定义要显示的内容，一种是通过 `actions` 设置，并可以设置 `floatingActionButton`。

示例代码：

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun AppBarScreen(
    modifier: Modifier = Modifier
) {
    Column (
        modifier = modifier.fillMaxWidth(),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        TopAppBar(
            title = { 
                Text(text = "8ug.icu")
            },
            navigationIcon = {
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Home, contentDescription = null)
                }
            },
            actions = {
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Share, contentDescription = null)
                }
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Settings, contentDescription = null)
                }
            }
        )
        
        MediumTopAppBar(
            title = {
                Text(text = "8ug.icu")
            }
        )

        LargeTopAppBar(
            title = {
                Text(text = "8ug.icu")
            }
        )

        CenterAlignedTopAppBar(
            title = {
                Text(text = "8ug.icu")
            }
        )

        BottomAppBar {
            OutlinedTextField(value = "", onValueChange = {})
            Button(onClick = { /*TODO*/ }) {
                Text(text = "提交")
            }
        }

        BottomAppBar(
            actions = {
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Share, contentDescription = null)
                }
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.ThumbUp, contentDescription = null)
                }
            },
            floatingActionButton = {
                FloatingActionButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Edit, contentDescription = null)
                }
            }
        )
    }
}

@Preview
@Composable
private fun TopAppBarScreenPreview() {
    AppBarScreen()
}
```

写 Preview 技巧，输入 `prev` 等出现提示按回车键。

Demo：[https://www.8ug.icu/search/tags/jetpack-compose-tutorial](https://www.8ug.icu/search/tags/jetpack-compose-tutorial) 中的 `AppBarScreen`。