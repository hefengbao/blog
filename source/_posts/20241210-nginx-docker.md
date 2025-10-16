---
title: Docker 容器中运行 Nginx
date: 2024-12-10 17:17:17
tags:
  - docker
  - nginx
categories: Docker
---
## 前置条件

1. 操作系统已安装 Docker
2. 有 root 账户或者可以运行 sudo 命令的账户

## 安装设置

### 1. 拉取镜像

```shell
sudo docker pull nginx
```

### 2. 运行 Ngnix 的 Docker 容器

```shell
sudo docker run -d -p 80:80 --name my-nginx-server nginx
```

`-d`: 以分离模式启动容器（容器在后台运行）。

`-p`: 绑定容器到主机的端口（将主机 8 0端口的请求导到容器的 80 端口）。

`-name`: Docker 容器的名字（实例中是my-nginx-server）

现在可以打开浏览器，使用本机 IP 访问：如 `http://127.0.0.1`。应该能看到 Nginx 的默认页面。

### 3. 查看 Docker 容器列表

```shell
sudo docker container ls
```

![](https://hefengbao.github.io/assets/images/202412100951298.png)

根据上图中的输出，可以使用两种方法停止 Nginx 的 Docker 容器：

```shell
sudo docker stop 19411f8b3f35
sudo docker stop my-nginx-server
```
查看所有的容器，包含未运行的，需要使用 `-a` 参数： 

```shell
sudo docker container ls -a
```

### 4. 容器和主机之间共享数据

默认情况下，在容器内创建的任何数据只能在容器内使用，并且只能在容器运行时使用。但是容器可能被销毁，所以接下来要做的是让容器使用保存在主机中的数据。为了实现这一功能，我们将使用Docker中的绑定挂载功能。当您使用绑定挂载时，主机上的文件或目录会被挂载到容器中。

```shell
mkdir ~/www
```

```shell
vim ~/www/index.html
```

复制粘贴如下示例内容

```html
<html>
<head>
<title>Nginx Docker</title>
</head>
<body>
<h1>My static page.</h1>
</body>
</html>
```

接下来，我们将运行Nginx Docker容器，并将容器 `/usr/share/nginx/html` 的目录映射到保存index.html文件的主机www目录上。

```shell
docker run -d -p 80:80 -v ~/www:/usr/share/nginx/html/ --name my-nginx-server nginx
```

Nginx 容器默认使用的文件目录是 `/usr/share/nginx/html/`，使用 `-v` 参数绑定 `/usr/share/nginx/html/` 目录到主机的 `~/www` 目录。

**Docker使用冒号符号（：）将主机路径与容器路径分开。记住，主机路径总是第一位的。**