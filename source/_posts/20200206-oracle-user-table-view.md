---
title: Oracle 查看某个账户下的表、视图
date: 2020-02-06 17:17:17
tags:
  - oracle
categories: Oracle
---
select * from all_tab_comments -- 查询所有用户的表,视图等。

select * from [user](https://www.baidu.com/s?wd=user&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)_tab_comments -- 查询本用户的表,视图等。

select * from all_col_comments --查询所有用户的表的列名和注释。

select * from [user](https://www.baidu.com/s?wd=user&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)_col_comments -- 查询本用户的表的列名和注释。

select * from all_tab_columns --查询所有用户的表的列名等信息。

select * from [user](https://www.baidu.com/s?wd=user&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)_tab_columns --查询本用户的表的列名等信息。



**扩展资料**

[ORACLE](https://www.baidu.com/s?wd=ORACLE&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)下有三个视图

[DBA](https://www.baidu.com/s?wd=DBA&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)_TABLES 拥有[DBA](https://www.baidu.com/s?wd=DBA&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)角色的用户可以查看系统中的所有表

USER_TABLES 登录数据库的当前用户拥有的所有表

ALL_TABLES 登录数据库的当前用户有权限查看的所有表

参考资料：[百度百科-Oracle](https://baike.baidu.com/item/甲骨文公司/430115?fromtitle=Oracle&fromid=301207&fr=aladdin) 



来源：

https://zhidao.baidu.com/question/2011096311632088908.html