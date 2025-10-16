---
title: Jetpack Compose 入门：LazyVerticalGrid & LazyHorizontalGrid
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
`LazyVerticalGrid`、 `LazyHorizontalGrid` 用于显示 Grid 列表；

`GridCells.Fixed()` 设置要显示的列数；

`rememberLazyGridState()` 获取滚动状态；

`horizontalArrangement`、`verticalArrangement` 设置间距。

```kotlin
@Composable
fun GridScreen(
    modifier: Modifier = Modifier
) {
    val lazyVerticalGridState = rememberLazyGridState()
    val lazyHorizontalState = rememberLazyGridState()

    val list = listOf(1,2,3,4,5,6,7,8,9,10)

    Column(
        modifier = modifier.fillMaxWidth()
    ) {
        LazyHorizontalGrid(
            modifier = modifier.height(120.dp),
            rows = GridCells.Fixed(2),
            state = lazyHorizontalState,
            horizontalArrangement = Arrangement.spacedBy(16.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
            content = {
                items(
                    items = list,
                ){item ->  
                    Text(
                        text = "Grid $item",
                        modifier = modifier
                            .height(50.dp)
                            .width(100.dp)
                            .background(Color.Yellow),
                    )
                }
            }
        )
        
        LazyVerticalGrid(
            columns = GridCells.Fixed(2),
            state = lazyVerticalGridState,
            horizontalArrangement = Arrangement.spacedBy(16.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp),
            content = {
                items(
                    items = list,
                ){item ->
                    Text(
                        text = "Grid $item",
                        modifier = modifier
                            .height(200.dp)
                            .width(100.dp)
                            .background(Color.Green),
                    )
                }
            }
        )
    }
}
```

参考文档 [列表和网格](https://developer.android.google.cn/jetpack/compose/lists?hl=zh-cn)。

Demo：[https://github.com/hefengbao/jetpack-compose-demo](https://github.com/hefengbao/jetpack-compose-demo)
