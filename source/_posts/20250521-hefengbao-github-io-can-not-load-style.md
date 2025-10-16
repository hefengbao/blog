---
title: hefengbao.github.io 无法加载样式
date: 2025-05-21 17:17:17
tags:
  - GithubPage
  - vitepress
categories: Github
---
在 https://github.com/hefengbao/hefengbao.github.io 仓库使用 `vitepress`  建立站点，部署到 Github Page 后，访问 https://hefengbao.github.io 不能加载样式,，因为使用 vitepress 建立过 https://hefengbao.github.io/knowledge 等站点，所以配置是没问题的，做了一些测试，发现无法加载 `assets` 目录下的文件，然而 https://hefengbao.github.io/knowledge 站点下却是没问题的😓。

![](images/445063428-3d491ea2-7ec7-4a78-b912-68a9a265858f.png)


![](images/445063500-619ac4e2-590c-4508-a34d-7929bb74eba2.png)

![](images/445063560-449b2411-8607-4e8e-9fae-3ad9f25bf3f0.png)


于是想有没有办法修改打包后静态资源输出的目录名称，查找 vitepress 的文档，果然可以设置：https://vitepress.dev/zh/reference/site-config#assetsdir。

```js
export default {
  assetsDir: 'static'
}
```