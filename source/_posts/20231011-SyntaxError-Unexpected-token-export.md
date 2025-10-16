---
title: "SyntaxError: Unexpected token 'export'"
date: 2023-10-11 17:17:17
tags:
  - nodejs
categories: Nodejs
---
运行 `npm run dev` 出现如下错误：

```shell
> dev
> vite


  VITE v4.4.11  ready in 358 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help

  LARAVEL v10.26.2  plugin v0.7.8

  ➜  APP_URL: http://localhost
(node:105128) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.
(Use `node --trace-warnings ...` to show where the warning was created)
[Failed to load PostCSS config: Failed to load PostCSS config (searchPath: /root/project/php/e-campus-portal): [SyntaxError] Unexpected token 'export'
/root/project/php/e-campus-portal/postcss.config.js:1
export default {
^^^^^^

SyntaxError: Unexpected token 'export'
    at internalCompileFunction (node:internal/vm:73:18)
    at wrapSafe (node:internal/modules/cjs/loader:1178:20)
    at Module._compile (node:internal/modules/cjs/loader:1220:27)
    at Module._extensions..js (node:internal/modules/cjs/loader:1310:10)
    at Module.load (node:internal/modules/cjs/loader:1119:32)
    at Module._load (node:internal/modules/cjs/loader:960:12)
    at ModuleWrap.<anonymous> (node:internal/modules/esm/translators:169:29)
    at ModuleJob.run (node:internal/modules/esm/module_job:194:25)]

Node.js v18.17.1
```

在网上一直搜 `SyntaxError: Unexpected token 'export'` 的解决办法，最终发现 `Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.` 这句才是重点，在 `package.json` 中添加：

```js
{
	...
	"type": "module"
}
```