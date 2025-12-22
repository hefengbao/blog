---
title: A failure occurred while executing app.cash.sqldelight.gradle.SqlDelightTask$GenerateInterfaces
date: 2025-12-22 11:52:20
tags:
  - KMP
---
![](images/20251222_114729_865.png)

使用 Windows 11 操作系统， Android Studio 运行这个项目，报错了：

```shell
Failed to delete old native lib
java.nio.file.AccessDeniedException: C:\WINDOWS\sqlite-3.51.0.0-37b7cb25-ef11-4aa1-a0e5-ce5c8d8443a7-sqlitejdbc.dll
	at java.base/sun.nio.fs.WindowsException.translateToIOException(Unknown Source)
	at java.base/sun.nio.fs.WindowsException.rethrowAsIOException(Unknown Source)
	at java.base/sun.nio.fs.WindowsException.rethrowAsIOException(Unknown Source)
	at java.base/sun.nio.fs.WindowsFileSystemProvider.implDelete(Unknown Source)
	at java.base/sun.nio.fs.AbstractFileSystemProvider.delete(Unknown Source)
	at java.base/java.nio.file.Files.delete(Unknown Source)
	at org.sqlite.SQLiteJDBCLoader.lambda$cleanup$2(SQLiteJDBCLoader.java:103)
```

```shell
Unexpected IOException
java.nio.file.AccessDeniedException: C:\WINDOWS\sqlite-3.51.0.0-a451af71-d148-477d-af38-571b28c999d1-sqlitejdbc.dll.lck
	at java.base/sun.nio.fs.WindowsException.translateToIOException(Unknown Source)
	at java.base/sun.nio.fs.WindowsException.rethrowAsIOException(Unknown Source)
	at java.base/sun.nio.fs.WindowsException.rethrowAsIOException(Unknown Source)
	at java.base/sun.nio.fs.WindowsFileSystemProvider.newByteChannel(Unknown Source)
	at java.base/java.nio.file.Files.newByteChannel(Unknown Source)
	at java.base/java.nio.file.Files.createFile(Unknown Source)
	at org.sqlite.SQLiteJDBCLoader.extractAndLoadLibraryFile(SQLiteJDBCLoader.java:197)
	at org.sqlite.SQLiteJDBCLoader.loadSQLiteNativeLibrary(SQLiteJDBCLoader.java:332)
```

```shell
Failed to load native library through System.loadLibrary
java.lang.UnsatisfiedLinkError: no sqlitejdbc in java.library.path: E:\Program\Android\Android Studio\jbr\bin;C:\WINDOWS\Sun\Java\bin;C:\WINDOWS\system32;C:\WINDOWS;.
	at java.base/java.lang.ClassLoader.loadLibrary(Unknown Source)
```

```shell
Execution failed for task ':core:data:generateCommonMainReaderDatabaseInterface'.
> A failure occurred while executing app.cash.sqldelight.gradle.SqlDelightTask$GenerateInterfaces
   > Failed to compile E:/Project/Demo/KmpDemo/twine/core/data/src/commonMain/sqldelight/migrations/1.sqm:208:
       INSERT INTO post_search SELECT title, description, link FROM post
```

```shell
* Exception is:
org.gradle.api.tasks.TaskExecutionException: Execution failed for task ':core:data:generateCommonMainReaderDatabaseInterface'.

Caused by: org.gradle.workers.internal.DefaultWorkerExecutor$WorkExecutionException: A failure occurred while executing app.cash.sqldelight.gradle.SqlDelightTask$GenerateInterfaces

Caused by: java.lang.UnsatisfiedLinkError: 'void org.sqlite.core.NativeDB._open_utf8(byte[], int)'
	at org.sqlite.core.NativeDB._open_utf8(Native Method)
	at org.sqlite.core.NativeDB._open(NativeDB.java:82)
	at org.sqlite.core.DB.open(DB.java:216)
	at org.sqlite.SQLiteConnection.open(SQLiteConnection.java:268)
	at org.sqlite.SQLiteConnection.<init>(SQLiteConnection.java:67)
	at org.sqlite.jdbc3.JDBC3Connection.<init>(JDBC3Connection.java:28)
	at org.sqlite.jdbc4.JDBC4Connection.<init>(JDBC4Connection.java:19)
	at org.sqlite.JDBC.createConnection(JDBC.java:106)
```

在网上找答案，尝试以管理身份打开 shell, 使用命令 `./gradlew :androidApp:assembleDebug`  运行，还是报同样的错

![](images/20251222_111204_784.png)

后来灵机一动， 关闭了所有打开的 Android Studio 窗口，然后以管理员身份打开， 然后运行项目，成功运行。👍

![](images/20251222_120947_403.png)