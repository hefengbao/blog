---
title: Oracle对where条件中有null值字段的处理
date: 2020-05-18 17:17:17
tags:
  - oracle
categories: Oracle
---
假如有如下数据（Users）：

| ID    | NAME | IS_ADMIN |
| ----- | ---- | -------- |
| 10000 | 贺一 | 是       |
| 10001 | 贺二 |          |
| 10002 | 贺三 |          |



查询 `admin`:

```
select * from users where is_admin = '是';
```

查询不是 `admin`:

如果使用下面的 sql, 查不到任何数据

```
select * from users where is_admin != '是';
```

可使用如下 sql:

```
select * from users where is_admin is null;
```



https://blog.csdn.net/qq_33666602/article/details/80280122