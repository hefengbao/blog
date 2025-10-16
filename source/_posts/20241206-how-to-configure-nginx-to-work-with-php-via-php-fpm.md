---
title: " 如何通过 PHP-FPM 配置 Nginx 与 PHP 协同工作"
date: 2024-12-06 17:17:17
tags:
  - php
  - nginx
  - php-fpm
categories: PHP
---
## 1.安装 Nginx

### Ubuntu / Debian

```shell
sudo apt install nginx
```

### CentOS / AlmaLinux / Rocky Linux

[Extra Packages for Enterprise Linux (EPEL)](https://docs.fedoraproject.org/zh_CN/epel/)

```shell
sudo yum install epel-release
```

```shell
sudo yum install nginx
```

### Fedora

```shell
sudo dnf install nginx
```

## 2.安装 PHP-FPM

### Ubuntu / Debian

```shell
sudo apt install php-fpm
```

### CentOS

**CentOS 7**

```shell
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum-config-manager --enable remi-php74
sudo yum install php php-fpm
```

**CentOS 8**

```shell
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum-config-manager --enable remi-php74
sudo yum install php php-fpm
```

## 3. 配置 Nginx 通过 PH-FPM 执行 PHP

创建 nginx 配置文件

```shell
sudo vim /etc/nginx/sites-available/example.com
````

编辑内容：

```shell
server {
        listen 80;
        root /var/www/html;
        index index.php index.html index.htm;
        server_name example.com;
 
        location / {
            try_files $uri $uri/ =404;
        }
 
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        }
}
```

保存后，创建软连接到 `/etc/nginx/sites-enabled`:

```shell
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com 
```

确保 `fastcgi_pass ` 定义的 PHP  socket 文件名是正确的，例子中是 `php7.4-fpm.sock`,

```shell
ls -l /var/run/php/
```

类似如下的内容：

```shell
total 4
-rw-r--r-- 1 root     root      5 Dec  1 17:43 php7.4-fpm.pid
srw-rw---- 1 www-data www-data  0 Dec  1 17:43 php7.4-fpm.sock
lrwxrwxrwx 1 root     root     30 Dec  1 17:43 php-fpm.sock -> /etc/alternatives/php-fpm.sock
```

如果不一致，按自己实际修改即可。

重启 Nginx 服务：

```shell
sudo systemctl restart nginx.service
```

## 4. 测试配置

上述设置中，站点文件根目录 `/var/www/html`, 创建测试文件：

```
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```

测试访问 ` http://ip/info.php`.



参考：

https://linuxiac.com/how-to-configure-nginx-to-work-with-php-via-php-fpm/

https://linuxiac.com/install-and-configure-nginx/
