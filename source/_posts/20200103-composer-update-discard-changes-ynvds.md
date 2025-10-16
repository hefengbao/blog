---
title: "composer update: Discard changes [y,n,v,d,s,?]?"
date: 2020-01-03 17:17:17
tags:
  - composer
categories: Composer
---
`composer update` 出现如下错误： 

```shell
Package operations: 3 installs, 26 updates, 0 removals
  - Updating nesbot/carbon (2.27.0 => 2.28.0):     The package has modified files:
    M bin/carbon
    Discard changes [y,n,v,d,s,?]? 
```

y - discard changes and apply the update 

n - abort the update and let you manually clean things up 

v - view modified files d - view local modifications (diff) 

s - stash changes and try to reapply them after the update

查看源码：

https://github.com/composer/composer/blob/master/src/Composer/Downloader/GitDownloader.php#L269-L310

可选择 `y`, 然后运行 `composer require nesbot/carbon --prefer-dist` 重新安装。

参考：

https://www.e-learn.cn/content/wangluowenzhang/604410
