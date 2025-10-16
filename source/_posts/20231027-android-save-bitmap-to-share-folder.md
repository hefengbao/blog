---
title: " Android 保存 bitmap 到共享目录"
date: 2023-10-27 17:17:17
tags:
  - android
  - android-bitmap
categories: Android
---

```kotlin
object FileUtil {
    fun saveImageToStorage(
        context: Context,
        bitmap: Bitmap,
        filename: String,
        mimeType: String = "image/jpeg",
        directory: String = Environment.DIRECTORY_PICTURES,
        mediaContentUri: Uri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
    ): Boolean {
        val contentResolver: ContentResolver = context.contentResolver

        val imageOutStream: OutputStream

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
            val values = ContentValues().apply {
                put(MediaStore.Images.Media.DISPLAY_NAME, filename)
                put(MediaStore.Images.Media.MIME_TYPE, mimeType)
                put(MediaStore.Images.Media.RELATIVE_PATH, directory)
            }

            contentResolver.run {
                val uri =
                    contentResolver.insert(mediaContentUri, values)
                        ?: return false
                imageOutStream = openOutputStream(uri) ?: return false
            }
        } else {
            val imagePath = Environment.getExternalStoragePublicDirectory(directory).absolutePath
            val image = File(imagePath, filename)
            imageOutStream = FileOutputStream(image)
        }

        imageOutStream.use { bitmap.compress(Bitmap.CompressFormat.JPEG, 100, it) }

        return true
    }
}
```

使用示例：

```kotlin
val localDateTime = LocalDateTime.now()
val dateTimeFormatter = DateTimeFormatter.ofPattern("yyyyMMddHHmmss")

if (FileUtil.saveImageToStorage(
				context,
				imageBitmap,
				"jingmo_${dateTimeFormatter.format(localDateTime)}.jpg"
		)
) {
		Toast.makeText(context, "保存成功", Toast.LENGTH_SHORT).show()
} else {
		Toast.makeText(context, "保存失败", Toast.LENGTH_SHORT).show()
}
```

参考：

https://androidexplained.github.io/android/android11/scoped-storage/2020/09/29/file-saving-android-11.html

https://zhuanlan.zhihu.com/p/172493773

https://www.bilibili.com/video/BV1Fj411z7HS/