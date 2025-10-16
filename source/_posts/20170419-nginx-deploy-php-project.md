---
title: Nginx 服务器部署 php 项目
date: 2017-04-19 17:17:17
tags:
  - nginx
categories: Nginx
---
示例的服务器操作系统为 `ubuntu`。

默认的网站根目录 `/var/www/html`，可以把项目拷贝到该目录下，也可在`/var/www`目录下建立目录。我们把项目放在 `/var/www/`目录下： `/var/www/project_name`. 

然后将 Nginx 的用户名和用户组 `www-data` 分配给它：

```bash
#进入 /var/www 目录
cd /var/www

#分配用户名和用户组
chown -R www-data:www-data project_name
```

## 配置 Nginx:

nginx 的默认配置位于 `/etc/nginx/sites-available/default`

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
    
		# 网站根目录，根据实际情况修改
        root /var/www/project_name;

        # Add index.php to the list if you are using PHP
        index index.php index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.1-fpm.sock;
        }
}

```

如果配置一个站点，简单配置如上即可。

## Nginx 配置多站点：

1、建立多个项目：

`/var/www/project1`
`/var/www/project2`

2、在 `/etc/nginx/sites-available/` 目录下新建配置 project1（或拷贝default，重命名为 project1）：

```
server{
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;
		
		#网站域名，根据自己的域名修改，如果只是本机用来开发测试，可自定义
        server_name www.project1.com;
	
		#项目目录地址
        root /var/www/project1;
        index index.html index.php;

        location / {
                try_files $uri $uri/ = 404;

        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.1-fpm.sock;
        }
```

project2 配置类似 project1 ,修改相应的域名、项目目录即可。

3、建立 `/etc/nginx/sites-available/` 到 `/etc/nginx/sites-enable` 的软连接

```
cd  /etc/nginx/sites-available

ln -s  project1 /etc/nginx/sites-enable
ln -s  project2 /etc/nginx/sites-enable
```

`/etc/nginx/sites-enable` 文件夹中的配置为当前可用的配置

4、修改 `hosts`

```
vi /etc/hosts
```

在 `hosts` 文件中添加：

```
127.0.0.1  www.project1.com
127.0.0.1  www.project2.com
```

保存。

5、重启 Nginx

```
service nginx restart
```

在浏览器中访问 www.project1.com 、www.project2.com  测试是否配置成功。