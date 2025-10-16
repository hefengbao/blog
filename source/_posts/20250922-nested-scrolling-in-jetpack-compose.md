---
title: Jetpack compose 嵌套滚动
date: 2025-09-22 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---

- **`Modifier.nestedScroll(connection, dispatcher?)`**:将组件附加到嵌套滚动链上。
- **`NestedScrollConnection`**: 重写 `onPreScroll`/`onPostScroll` 方法，以在子级处理滚动前后消耗或响应滚动偏移量。
- **`NestedScrollDispatcher`**: 在构建自定义可滚动组件时，向父级（上游）派发滚动/抛掷事件。

```kotlin
val nestedConnection = remember {
    object : NestedScrollConnection {
        override fun onPreScroll(
            available: Offset,
            source: NestedScrollSource
        ): Offset {
            // intercept before child scrolls
            return Offset.Zero
        }
        override fun onPostScroll(
            consumed: Offset,
            available: Offset,
            source: NestedScrollSource
        ): Offset {
            // react after child scrolls
            return Offset.Zero
        }
    }
}
```

This connection lets you consume or react to scroll deltas before and after the child handles them.

After that link your connection to a parent container using `Modifier.nestedScroll`, so it joins the nested‑scroll chain.

```kotlin
Box(
    Modifier
        .fillMaxSize()
        .nestedScroll(nestedConnection)
) {
    // Place child scrollable(s) here
}
```

Inside `onPreScroll`, adjust a header’s height state and return how much you consumed. For example:

```kotlin
val toolbarHeight = remember { mutableStateOf(maxHeightDp) }
val connection = remember {
    object : NestedScrollConnection {
        override fun onPreScroll(
            available: Offset,
            source: NestedScrollSource
        ): Offset {
            val deltaY = available.y
            val newHeight = (toolbarHeight.value + deltaY)
                .coerceIn(minHeightDp, maxHeightDp)
            val consumed = newHeight - toolbarHeight.value
            toolbarHeight.value = newHeight
            return Offset(0f, consumed)
        }
    }
}
```

This ensures the header collapses as you scroll up and expands on scroll down.

Wrap a `LazyColumn` and your header in a `Box` with `Modifier.nestedScroll(connection)`. Tie the header’s `height` and `offset` modifiers to the state updated in `onPreScroll`:

```kotlin
@Composable
fun CollapsingToolbarScreen() {
    val minHeight = 56.dp
    val maxHeight = 200.dp
    val maxHeightPx = with(LocalDensity.current) { maxHeight.toPx() }
    val offsetY = remember { mutableStateOf(0f) }

    val connection = remember {
        object : NestedScrollConnection {
            override fun onPreScroll(
                available: Offset,
                source: NestedScrollSource
            ): Offset {
                val delta = available.y
                val newOffset = (offsetY.value + delta)
                    .coerceIn(-maxHeightPx, 0f)
                val consumed = newOffset - offsetY.value
                offsetY.value = newOffset
                return Offset(0f, consumed)
            }
        }
    }

    Box(
        Modifier
            .fillMaxSize()
            .nestedScroll(connection)
    ) {
        LazyColumn(contentPadding = PaddingValues(top = maxHeight)) {
            items(100) {
                Text("Item #$it", Modifier.padding(16.dp))
            }
        }
        TopAppBar(
            title = { Text("Title") },
            modifier = Modifier
                .height(maxHeight + offsetY.value.toDp())
                .offset { IntOffset(0, offsetY.value.roundToInt()) }
        )
    }
}
```


[Nested Scrolling in Jetpack Compose by Victor Brandalise](https://victorbrandalise.com/nested-scrolling-in-jetpack-compose/)