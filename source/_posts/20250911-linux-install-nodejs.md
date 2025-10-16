---
title: " Linux 安装 nodejs"
date: 2025-09-11 17:17:17
tags:
  - linux
  - nodejs
  - ubuntu
categories: Nodejs
---

```shell
# 添加 NodeSource 仓库
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

# 安装 Node.js
sudo apt-get install -y nodejs

# 验证安装版本
node -v
```