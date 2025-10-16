---
title: Ubuntu iptables
date: 2020-04-30 17:17:17
tags:
  - iptables
categories: Ubuntu
---
Ubuntu 编写的 iptables 规则，在重启后会失效；使用 `iptables-persistent` 持久化保存。

```
sudo apt install iptables-persistent
```

编写完规则后，执行：

```
sudo netfilter-persistent save
 
sudo netfilter-persistent reload
```



参考 ：

1、[ubuntu/debian iptables规则持久化，iptables-persistent](https://zorz.cc/post/ubuntu-debian-iptables-persistent.html) 

2、 https://help.ubuntu.com/community/IptablesHowTo#Solution_.233_iptables-persistent



添加防火墙过滤规则步骤如下;

1、查看现有防火墙过滤规则：

    iptables -nvL --line-number

2、添加防火墙过滤规则（设置白名单）：

       1）添加白名单
    
            iptables -I INPUT 3 -s 136.6.231.163 -p tcp --dport 1521 -j ACCEPT
    
            命令详解：
    
                -I：添加规则的参数  
    
                    INPUT：表示外部主机访问内部资源
    
                    3：表示添加到第三行（可以任意修改）
    
                -s：指定作为源地址匹配，这里不能指定主机名称，必须是IP；
    
                -p: 用于匹配协议的（这里的协议通常有3种，TCP/UDP/ICMP）
    
                    --dport: 用于匹配端口号
    
                -j: 用于匹配处理方式：
    
                    常用的ACTION：


	             DROP：悄悄丢弃
		        一般我们多用DROP来隐藏我们的身份，以及隐藏我们的链表
	             REJECT：明示拒绝
	             ACCEPT：接受
	    2）查看添加结果
	
	        iptables -nvL --line-number

原文链接：https://blog.csdn.net/qq_37837701/java/article/details/80578807

https://blog.csdn.net/sanve/article/details/80881380

IP地址网段表示法](https://www.cnblogs.com/amyzhu/p/11396136.html)

172.12.34.0/25

1. 子网掩码：用于表示IP地址中的多少位用来做主机号。因为"其中值为1的比特留给网络号和子网号，为0的比特留给主机号"（TCP/IP V1）。
2. 172.12.34.0/25含义：
   1. 172.12.34.0——表示一个子网号
   2. 25——表示采用子网掩码中国呢的前25位为有效未，即用 32-25=7big 来表示主机号，该子网可以容纳 2^7-2=126台主机。
   3. 因此172.12.34.0/25 表示IP范围为 172.12.34.1～172.12.34.126





https://blog.csdn.net/csfreebird/article/details/8132362

