---
title: How To Redirect www To non-www And Vice Versa with Nginx
date: 2020-05-22 17:17:17
tags:
  - nginx
categories: Nginx
---
In this short tutorial I’ll show you how to make permanent redirect from a *www* URL to *non-www* and vice versa. I’ll assume that you have superuser privileges, sudo or root access and **Nginx** already configured, as well as DNS records. More specifically, you need to have an A records for www.yourdomain.com and yourdomain.com .

## Redirect non-www to www

To redirect users from a plain, *non-www* domain to a *www* domain, you need to add this snippet in your **Nginx** domain configuration file:

```shell
server {
    server_name    yourdomain.com
    return         301 http://www.yourdomain.com$request_uri;
}
```



Save your configuration and exit. Before restarting Nginx make sure to test your configuration:

```shell
root@secure:~# nginx -t
```



You should have something like this as output:

```shell
root@secure:~# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```



Now when everything is checked you can restart Nginx:

```shell
root@secure:~# service nginx restart
```



Now, if you curl your plain domain, you should get a 301 Moved Permanently response:

```shell
root@secure:~# curl -I yourdomain.dev
HTTP/1.1 301 Moved Permanently
Server: nginx/1.8.0
Date: Fri, 26 Jun 2015 08:36:15 GMT
Content-Type: text/html
Content-Length: 184
Connection: keep-alive
Location: http://www.yourdomain.dev/
```



## Redirect www to non-www

In the previous example you saw how to redirect users from a plain *non-ww* domain to a *www* domain. However, if you want to redirect from a *www* to a plain *non-www* domain you need to add following snippet in your domain configuration file:

```shell
server {
        server_name www.yourdomain.com;
        return 301 $scheme://yourdomain.com$request_uri;
}
```



After any change in **Nginx** configuration files you should test it for syntax errors:

```shell
root@secure:~# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```



And if you curl the *www* domain you should get same 301 Moved Permanently response:

```
root@secure:~# curl -I www.yourdomain.dev
HTTP/1.1 301 Moved Permanently
Server: nginx/1.8.0
Date: Fri, 26 Jun 2015 08:52:26 GMT
Content-Type: text/html
Content-Length: 184
Connection: keep-alive
Location: http://yourdomain.dev/
```



And that’s it. You have properly configured permanent redirect.



##### REFERENCES:

[NGINX 301 Redirects](http://jeffsebring.com/2012/10/25/nginx-301-redirects/)

[Nginx rewrite non-www-prefixed domain to www-prefixed domain](https://stackoverflow.com/a/15517045/975850)

[Nginx config for www to non-www and non-www to www redirection](https://rtcamp.com/tutorials/nginx/www-non-www-redirection/)

[How To Redirect www to Non-www with Nginx on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-redirect-www-to-non-www-with-nginx-on-centos-7)



来源：

https://bosnadev.com/2015/06/26/how-to-redirect-www-to-non-www-and-vice-versa-with-nginx/