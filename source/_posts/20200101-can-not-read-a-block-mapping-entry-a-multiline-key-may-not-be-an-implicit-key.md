---
title: can not read a block mapping entry; a multiline key may not be an implicit key
date: 2020-01-01 17:17:17
tags:
  - hexo
categories: Hexo
---

```shell
YAMLException: can not read a block mapping entry; a multiline key may not be an implicit key at line 2, column 5:
    date: 2019-08-21 17:19:49
        ^
    at generateError (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:167:10)
    at throwError (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:173:9)
    at readBlockMapping (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:1073:9)
    at composeNode (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:1359:12)
    at readDocument (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:1519:3)
    at loadDocuments (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:1575:5)
    at Object.load (F:\Document\Blog\helloword\node_modules\js-yaml\lib\js-yaml\loader.js:1596:19)
    at parseYAML (F:\Document\Blog\helloword\node_modules\hexo-front-matter\lib\front_matter.js:80:21)
    at parse (F:\Document\Blog\helloword\node_modules\hexo-front-matter\lib\front_matter.js:56:12)
    at Promise.all.spread (F:\Document\Blog\helloword\node_modules\hexo\lib\plugins\processor\post.js:48:20)
    at tryCatcher (F:\Document\Blog\helloword\node_modules\bluebird\js\release\util.js:16:23)
    at Promise._settlePromiseFromHandler (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:509:35)
    at Promise._settlePromise (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:569:18)
    at Promise._settlePromise0 (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:614:10)
    at Promise._settlePromises (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:694:18)
    at Promise._fulfill (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:638:18)
    at PromiseArray._resolve (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise_array.js:126:19)
    at PromiseArray._promiseFulfilled (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise_array.js:144:14)
    at PromiseArray._iterate (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise_array.js:114:31)
    at PromiseArray.init [as _init] (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise_array.js:78:10)
    at Promise._settlePromise (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:566:21)
    at Promise._settlePromise0 (F:\Document\Blog\helloword\node_modules\bluebird\js\release\promise.js:614:10)
```

原因： 标题中有方括号，本来想加 `[转载]` 几个字，居然编译不成功