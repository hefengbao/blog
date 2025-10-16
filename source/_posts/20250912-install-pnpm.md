---
title: 安装 pnpm
date: 2025-09-12 17:17:17
tags:
  - pnpm
  - nodejs
categories: Nodejs
---

```shell
npm i -g pnpm
```

修改 `pnpm` 保存目录, 示例：

```shell
pnpm config set store-dir "E:\AppData\.pnpm-store"
```

验证：

```shell
pnpm store path  
```

结果示例：

```shell
E:\AppData\.pnpm-store\v10
```
