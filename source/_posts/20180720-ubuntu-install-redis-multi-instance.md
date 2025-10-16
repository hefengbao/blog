---
title: Ubuntu redis 多实例安装
date: 2018-07-20 17:17:17
tags:
  - redis
  - ubuntu
categories: Ubuntu
---
安装 redis:

```
apt-get install redis-server
```

安装成功后，默认端口为 `6379`.

接下来完成一个端口为 `6380` 的服务端：

修改 redis 配置文件：

```
cd /etc/redis

cp redis.conf redis-6380.conf

vi redis-6380.conf
```

作如下修改：

```
port 6380

pidfile /var/run/redis/redis-server-6380.pid

logfile /var/log/redis/redis-server-6380.log

dbfilename dump-6380.rdb

```

修改 redis 启动文件：

```
cd /etc/init.d/

cp redis-server redis-server-6380

vi redis-server-6380
```

作如下修改：

```
DAEMON_ARGS=/etc/redis/redis-6380.conf

DESC=redis-server-6380

PIDFILE=$RUNDIR/redis-server-6380.pid

```

添加 service:

```
cp /lib/systemd/system/redis-server.service /lib/systemd/system/redis-server-6380.service

vi /lib/systemd/system/redis-server-6380.service
```

作如下修改：

```
ExecStart=/usr/bin/redis-server /etc/redis/redis-6380.conf

PIDFile=/var/run/redis/redis-server-6380.pid
```

```
cd /etc/systemd/system
ln -s  /lib/systemd/system/redis-server-6380.service redis-6380.service
```
启动 redis-server-6380:

```
service redis-server-6380 start 

ps -ef | grep redis
```

```
redis     5885     1  0 Jul20 ?        00:00:40 /usr/bin/redis-server 127.0.0.1:6379
redis     9339     1  0 09:20 ?        00:00:00 /usr/bin/redis-server 127.0.0.1:6380
```