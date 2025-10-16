---
title: app:transformDexArchiveWithExternalLibsDexMergerForDebug
date: 2018-03-21 17:17:17
tags:
  - AndroidStudio
categories: AndroidStudio
---
Android studio 编译项目时，出现如下错误：

```shell
Error:Execution failed for task ':app:transformDexArchiveWithExternalLibsDexMergerForDebug'.
> java.lang.RuntimeException: java.lang.RuntimeException: com.android.builder.dexing.DexArchiveMergerException: Unable to merge dex
```

从错误提示信息中很难看出问题出在哪里，去搜索也比较茫然，可对 IDE 做如下设置：

![](images/NlFoGMPAae.png)

然后重新编译，便可看到相较而言比较详细的错误信息，在这里我遇到的是 65535 问题，解决办法就是使用 `MultiDex`,官方提供的解决方案。

配置如下：

在 `build.gradle`  文件中：

```
defaultConfig {
	...
	
	multiDexEnabled true
}
```

```
dependencies {
	...
	
	implementation 'com.android.support:multidex:1.0.3'
}
```

自定义 Application 继承自 `MultiDexApplication`:

```
public class App extends MultiDexApplication{
	@Override
    public void onCreate() {
        super.onCreate();
	}
}
```

在 `AndroidManifest.xml` 中作如下修改：

```
<application
        android:name=".App"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
		
		...
		
</application>
```

###  android:name=".App"  ,这里修改为自定义的 Application 即可。