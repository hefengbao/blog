---
title: Jetpack Compose 入门：Scaffold & Snackbar
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
Scaffold（脚手架），提供一个基本的布局：

```kotlin
Scaffold(
        topBar = {},
        bottomBar = {},
        floatingActionButton = {},
        snackbarHost = {}
    ) { paddingValues ->
        Column(
            modifier = modifier.padding(paddingValues)
        ) {

        }
    }
```

示例代码：

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ScaffoldScreen(
    modifier: Modifier = Modifier
) {
    val snackbarHostState = remember { SnackbarHostState() }
    var showSnackbar by remember { mutableStateOf(false) }

    Scaffold(
        topBar = {
            TopAppBar(
                title = {
                    Text(text = "ww.8ug.icu")
                },
                navigationIcon = {
                    IconButton(onClick = { /*TODO*/ }) {
                        Icon(imageVector = Icons.Default.ArrowBack, contentDescription = null)
                    }
                },
                actions = {
                    IconButton(onClick = { /*TODO*/ }) {
                        Icon(imageVector = Icons.Default.Search, contentDescription = null)
                    }
                }
            )
        },
        bottomBar = {
            BottomAppBar {
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Undo, contentDescription = null)
                }
                IconButton(onClick = { /*TODO*/ }) {
                    Icon(imageVector = Icons.Default.Redo, contentDescription = null)
                }
            }
        },
        floatingActionButton = {
            FloatingActionButton(onClick = { /*TODO*/ }) {
                Icon(imageVector = Icons.Default.Send, contentDescription = null)
            }
        },
        snackbarHost = { SnackbarHost(snackbarHostState)}
    ) { paddingValues ->
        Column(
            modifier = modifier.padding(paddingValues)
        ) {
            Button(onClick = { showSnackbar = true }) {
                Text(text = "Show Snackbar")
            }
        }

        LaunchedEffect(showSnackbar){
            if (showSnackbar){
                val result = snackbarHostState.showSnackbar(
                    message = "欢迎来到 www.8ug.icu",
                    actionLabel = "关闭",
                    duration = SnackbarDuration.Indefinite,
                    withDismissAction = true
                )

                showSnackbar = when(result){
                    SnackbarResult.Dismissed -> {
                        false
                    }

                    SnackbarResult.ActionPerformed -> {
                        false
                    }
                }
            }
        }
    }
}
```

`showSnackbar()` 中的 `duration` 设为 `SnackbarDuration.Indefinite` （不自动关闭），最好把 `withDismissAction` 设为 `true`，另外还有 `SnackbarDuration.Long` `SnackbarDuration.Short`，一段时间后会自行关闭。

参考文档：[https://developer.android.google.cn/jetpack/compose/layouts/material?hl=zh-cn](https://developer.android.google.cn/jetpack/compose/layouts/material?hl=zh-cn)

Demo：[https://github.com/hefengbao/jetpack-compose-demo.git](https://github.com/hefengbao/jetpack-compose-demo.git) 中的 `ScaffoldScreen` 。