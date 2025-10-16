# Jetpack Compose 入门：打开相册选择（单选/多选）图片

` ActivityResultContracts.PickVisualMedia()` 和 `ActivityResultContracts.PickMultipleVisualMedia()` 来打开相册，不需要权限请求那一块。

`PickVisualMediaRequest()` 指定要选择的图片还是视频，默认是 `ActivityResultContracts.PickVisualMedia.ImageAndVideo` ,另外还有：

`ActivityResultContracts.PickVisualMedia.ImageOnly` 只选照片；

`ActivityResultContracts.PickVisualMedia.VideoOnly` 只选影片；

`ActivityResultContracts.PickVisualMedia.SingleMimeType()` 选择某一类型的照片，例如 `ActivityResultContracts.PickVisualMedia.SingleMimeType("image/gif")`

简单示例：

```kotlin
@Composable
fun OpenAlbumScreen(
    modifier: Modifier = Modifier,
) {
    val singleLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.PickVisualMedia(),
        onResult = {
            //TODO
        }
    )
    
    val multiLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.PickMultipleVisualMedia(9),
        onResult = {
            //TODO
        }
    )

    Column(
        modifier = modifier.fillMaxWidth(),
        verticalArrangement = Arrangement.Top,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Button(
            onClick = {
                singleLauncher.launch(PickVisualMediaRequest())
            }
        ) {
            Text(text = "打开相册（单选）")
        }

        Button(
            onClick = {
                multiLauncher.launch(PickVisualMediaRequest())
            }
        ) {
            Text(text = "打开相册（多选）")
        }
    }
}
```

Demo：[https://github.com/hefengbao/jetpack-compose-demo](https://github.com/hefengbao/jetpack-compose-demo) 