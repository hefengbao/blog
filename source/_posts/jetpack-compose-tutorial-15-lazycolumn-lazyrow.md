---
title: Jetpack Compose 入门：LazyColumn & LazyRow
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
LazyColumn 生成的是垂直滚动列表，而 LazyRow 生成的是水平滚动列表。

滚动状态通过 `rememberLazyListState()` 获取。

如果要嵌套列表，如果最外层是 LazyColumn,，那么嵌套的 LazyColumn 或者 LazyRow 必须指定最大高度；如果最外层是 LazyRow ，那么嵌套的 LazyColumn 或者 LazyRow 必须指定最大宽度。

`item()` 用于添加单个列表项，`items()`、`itemsIndexed()` 用于添加多个列表项：

示例代码：

```kotlin
@Composable
fun ListScreen(
    modifier: Modifier = Modifier
) {
    val lazyColumnState = rememberLazyListState()
    val lazyRowState = rememberLazyListState()

    val list = listOf(1,2,3,4,5,6,7,8,9,10)
		
    LazyColumn(
        state = lazyColumnState,
        content = {
            item {
                LazyRow(
                    modifier = modifier.height(250.dp),
                    state = lazyRowState,
                    content = {
                        itemsIndexed(
                            items = list,
                            key = {index: Int, item: Int -> index },
                        ){index,item ->
                            Text(
                                text = "Row $item",
                                modifier = modifier
                                    .padding(16.dp)
                                    .background(Color.Yellow)
                                    .width(100.dp)
                                    .fillMaxHeight(),
                                textAlign = TextAlign.Center
                            )
                        }
                    }
                )
            }

            itemsIndexed(
                items = list,
                key = { index: Int, item: Int ->  index}
            ){index: Int, item: Int ->  
                Text(
                    text = "Column itemsIndexed：index = $index,item = $item",
                    modifier = modifier
                        .padding(16.dp)
                        .background(Color.Cyan)
                        .fillMaxWidth()
                )
            }
            item {
                Divider( modifier = modifier.fillMaxWidth())
            }

            items(
                items = list,
                key = null
            ){item: Int ->
                Text(
                    text = "Column items：item = $item",
                    modifier = modifier
                        .padding(16.dp)
                        .background(Color.Green)
                        .fillMaxWidth()
                )
            }
        }
    )
}
```

参考文档 [列表和网格](https://developer.android.google.cn/jetpack/compose/lists?hl=zh-cn)。

Demo：[https://github.com/hefengbao/jetpack-compose-demo](https://github.com/hefengbao/jetpack-compose-demo) 