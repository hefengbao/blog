---
title: Ubuntu 搭建 git server
date: 2017-07-11 17:17:17
tags:
  - git
categories: Git
---
**假设服务器 `ip` 地址为 `192.168.1.1`**

### 1、安装 `git`:

#### 1.1 安装：

```bash
sudo apt-get update
sudo apt-get install git-core
```

#### 1.2 创建 `git` 仓库目录：

```
cd /home
mkdir git
```

#### 1.3 创建密钥存放目录：

```
cd /home/git
mkdir .ssh
```

### 2、新建 `git` 用户，用来管理  `git` 服务： 

```
sudo adduser git
```

### 3、创建证书登陆：

#### 3.1 安装 `openssh-server`:

```
sudo apt-get install openssh-server
```

通过命令 `s –e|grep ssh` ,查看ssh服务是否启动（是否安装成功）

#### 3.2 在本机（客户端）生成证书：

我用的是 Windows, 打开 `git bash`,用如下命令生成：

```
ssh-keygen –t rsa 
```

在 `C:\Users\用户名\.ssh` 目录可看到：

```
id_rsa.pub     #公钥
id_ras  #私钥
```

#### 3.3 把证书上传到 `git` 服务器：

在 `C:\Users\用户名\.ssh` 目录下打开 `git bash`:

```
scp id_rsa.pub root@192.168.1.1:/home/git/.ssh
```

#### 3.4 服务器端密钥配置：

进入 `/home/git/.ssh` 目录：

```
cd /home/git/.ssh
```

如果 `authorized_kesys` 不存在，则重新生成：

```
touch authorized_kesys
```

将客户端上传的公钥id_rsa.pub的内容追加到authroized_keys中：

```
cat id_rsa.pub >> authorized_keys
```

### 4、初始化 `git` 仓库：

修改`git`目录的用户/用户组：

```
sudo chown -R git:git git
```

初始化：

```
cd /home/git

sudo git init --bare sample.git
```

### 5、禁用 `shell` 登陆：

打开 `/etc/passwd` 文件，找到 `git`用户，把 `git:x:1000:1000::/home/git:` 修改为：

```
git:x:1000:1000::/home/git:/usr/bin/git-shell
```

### 6、克隆仓库：

在客户端打开 `git bash`:

```
git clone git@192.168.1.1:/home/git/sample.git
```

结束！！！

-----

#### 资料参考：

[搭建Git服务器 - 廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)
[在 Ubuntu 系统中部署 Git Server](http://blog.csdn.net/poisonchry/article/details/11849781)
[如何在Ubuntu上几步搭建Git Server](https://my.oschina.net/yushulx/blog/609779)
[Ubuntu上搭建Git服务器](http://www.cnblogs.com/huntaiji/p/6287707.html)
[ssh证书登录(实例详解)](http://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646346.html)