---
title: Jetpack Compose 中的剪裁与遮罩功能
date: 2025-09-22 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
剪裁使用 Modifier 的 `clip` 方法：

```kotlin
Image(
    painter = painterResource(R.drawable.avatar),
    contentDescription = null,
    modifier = Modifier
        .size(72.dp)
        .clip(CircleShape)
)
```

![](images/2025092201.webp)

## 自定义形状

官方提供了 `CircleShape`, `RoundedCornerShap`， 如果不能满足需求，则可以自定义。

```kotlin
class SquishedOvalShape : Shape {
    override fun createOutline(
        size: Size,
        layoutDirection: LayoutDirection,
        density: Density
    ): Outline {
        return Outline.Generic(
            Path().apply {
                addOval(Rect(0f, size.height / 4f, size.width, size.height))
            }
        )
    }
}
```

![](images/2025092202.webp)


## 堆叠的头像

### 1. 绘制头像

```kotlin
@Composable
fun Avatar(image: Painter, modifier: Modifier = Modifier) {
    Image(
        painter = image,
        contentDescription = null,
        modifier = modifier
            .size(48.dp)
            .clip(CircleShape)
    )
}
```

### 2. 添加边框

使用 `drawWithContent` 先绘制头像，然后使用 `BlendMode.Clear` 绘制边框：

```kotlin
modifier.drawWithContent {
    drawContent()
    drawCircle(
        color = Color.Black,
        style = Stroke(width = 4f),
        radius = size.minDimension / 2,
        blendMode = BlendMode.Clear
    )
}
```

透明混合模式会移除该区域的像素，而不是直接覆盖它们。

![](images/2025092203.webp)

### 3. 采用离屏合成技术隔离图层

若不进行图层隔离，清除操作还会破坏背景的完整性。为防止这种情况，请将每个头像包裹在独立的图形层中:

```kotlin
Modifier.graphicsLayer {
    compositingStrategy = CompositingStrategy.Offscreen
}
```


![](images/2025092204.webp)

## 渐变效果的遮罩

### 1. 定义渐变遮罩

由黑到透明的遮罩：

```kotlin
val mask = Brush.verticalGradient(
    colors = listOf(Color.Black, Color.Transparent)
)
```

### 2. 应用 Blend Mode

```kotlin
Box(
    modifier = Modifier
        .fillMaxWidth()
        .height(24.dp)
        .graphicsLayer {
            compositingStrategy = CompositingStrategy.Offscreen
        }
        .drawWithContent {
            drawContent()
            drawRect(mask, blendMode = BlendMode.DstIn)
        }
)
```
`DstIn` keeps only the destination pixels (your content) where the source (the mask) is drawn, resulting in a fade.

### 3.  使之可复用

```kotlin
@Composable
fun FadingEdgeBox(content: @Composable () -> Unit) {
    Box {
        content()
        Box(
            modifier = Modifier
                .matchParentSize()
                .graphicsLayer {
                    compositingStrategy = CompositingStrategy.Offscreen
                }
                .drawWithContent {
                    drawRect(
                        brush = Brush.verticalGradient(
                            colors = listOf(Color.Black, Color.Transparent)
                        ),
                        blendMode = BlendMode.DstIn
                    )
                }
        )
    }
}
```

Now you can pass a `LazyColumn` or any scrollable content, and it will automatically fade at the edge.

![](images/2025092205.webp)

[Clipping and Masking in Jetpack Compose by Victor Brandalise](https://victorbrandalise.com/clipping-and-masking-in-jetpack-compose/)