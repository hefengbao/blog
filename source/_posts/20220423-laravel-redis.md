---
title: Laravel 使用 Redis
date: 2022-04-23 17:17:17
tags:
  - redis
categories: Laravel
---

```php
\Redis::set('str','123');
 
$data = \Redis::get('library');
$data = \Redis::del('library');
$da = \Redis::exists('library');
\Redis::append('str','_123');
\Redis::get('str');
\Redis::strlen('str');
\Redis::rename('str','str2');
\Redis::expire('str2',10);
$data = \Redis::ttl('str2');//获取缓存时间
$data = \Redis::substr('str2',0,2);//获取第一到第三位字符，结果为123
$data = \Redis::keys('st*');//模糊搜索
$data = \Redis::lindex('str2',1);
dd($data);
```



```php
//队列
$data = [1,2,3,4,5,6,'wa','oo','op','bar1','bar0'];
\Redis::expire('set2',10);//设置过期时间为10秒
\Redis::rpush('list1','bar1');
\Redis::rpush('list1','bar0');
\Redis::rpush('list1',$data);
$data = \Redis::lpop('list1');//随机取一个值
$data = \Redis::llen('list1');//获取长度
$data = \Redis::lrange('list1',0,-1);//获取队列中所以的值
$data = \Redis::lindex('list1',9);//返回指定下标的队列元素
\Redis::ltrim('list1',0,3);//只保留队列前4个元素，其余的都删掉。
$data = \Redis::lrange('list1',0,-1);//结果显示为0，1，2，3，4
\Redis::rpush('list2','ab1');
\Redis::rpoplpush('list1','list2');//从list1中取最后一个元素，放入list2的首位
\Redis::rpoplpush('list2','list2');
\Redis::linsert('list2','before','ab1','123');//在队列list2中的ab1之前插入123
\Redis::linsert('list2','after','ab1','456');//在队列list2中的ab1之后插入456
$data = \Redis::lrange('list2',0,-1);
```

```php
//set无序集合操作
\Redis::sadd('set1','ab');
\Redis::sadd('set1','cd');
\Redis::sadd('set1','ef');
\Redis::srem('set1','ef');//移除set1集合中的ef这个元素
\Redis::smove('set1','set2','ab');//移动set1中的ab到set2返回true或者false
$data = \Redis::smembers('set2');//返回无序集合的所有值
```

```php
//set有序集合操作
\Redis::zadd('zset1',1,'ab');
\Redis::zadd('zset1',2,'cd');
\Redis::zadd('zset1',10,'ef');
\Redis::zrem('zset1','ef');//移除ef这个元素
$data = \Redis::zrangebyscore('zset1',2,9);//返回cd，返回的是2到9这个区间的值
$data = \Redis::zcard('zset1');//统计元素的个数
$data = \Redis::zscore('zset1','ef');//获取ef这个元素的下标
\Redis::zremrangebyscore('zset1',0,2);//删除下标0到2之间的元素   返回ef
$data = \Redis::zrange('zset1',0,-1);//返回有序集合的所有值
```

```php
// hash表操作
\Redis::hset('hash1','key1',123);
\Redis::hdel('hash1','key1');//删除key1这个key对应的元素
$data = \Redis::hget('hash1','key1');//取相应key对应的值
$data = \Redis::hlen('hash1');//返回hash1元素个数
\Redis::hsetnx('hash1','key1','v2');//增加一个元素，但不能重复
\Redis::hmset('hash1',$data);//添加数组
$data1 = [0,1,2,3,4,5,6,7,8,9];
$data = \Redis::hmget('hash1',$data1);//查询数组格式
$data = \Redis::hget('hash1','key1');
$data = \Redis::hgetall('hash1');//返回整个hash表元素
$data = \Redis::hvals('hash1');//返回hash表中的所有value值
\Redis::select(2);
\Redis::set('foo','bar');
\Redis::move('foo',2);
$data = \Redis::get('foo');
```

```php
// 事务
\Redis::multi();
\Redis::set('book-name','Mastering C++ in 21 days');
\Redis::get('book-name');
\Redis::sadd('tag','c++','Programming','Mastering Series');
\Redis::smembers('tag');
\Redis::exec();
dd($data);
```


1. 普通得set/get操作，set操作，如果键名存在，则会覆盖原有得值

```php
$redis = app("redis.connection");

$redis->set('library' , 'phpredis');//存储key为library ，值phpredis得记录

$redis->get("library");//获取key为library得记录值

set/get 多个key-value

$mkv = array(
	"user:001"=>'First user',

	"user:002"=>"Second user",

	"user:003"=>"Third user"
);

$redis->mset($mkv); // 存储多个key对应的value

$retval = $redis->mget( array_keys($mkv) );//获取多个key对应的value
```

 2.  `setex` 存放带存储时效的记录

```php
$redis->setex("library" , 10 , 'phpredis');  //存储key为library,值为phpredis的记录，有效时长为10秒
```

`add` 操作，不会覆盖已有值

```php
$redis->setnx("foo" , 12); //返回true, 添加成功  存在不做任何操作  否则创建

$redis->setnx('foo' , 34); //返回false ，添加失败，因为存在键名foo的记录
```

 3.  getset 是 set 的变种，结果返回替换前的值

```php
$redis->getset('foo' , 56);//返回12；如果之前不存在记录，则返回null
```

4. incrby/incr/decrby/decr 对值得递增和递减

```php
   $redis->incr('foo'); //返回57 ，递增 阶梯为1

   $redis->incrby('foo' , 2); //返回59 递增 阶梯为2
```

5.  exists 检测是否存在  存在返回1 否则返回 0

```php
$redis->exists("foo");
```

6. type 类型检测，字符串返回 string ,列表返回 list , set表返回 set/zset ，hash表返回 hash

```php
$redis->type('foo');
```

7.  append 连接到已存在字符串

```php
$redis->get('str');//返回test

$redis->append('str' , "_123");
```

8. setrange 部分替换操作，并返回字符串长度

```php
$redis->setrange('str' , 0 , 'abc'); //返回3，第2个参数为0等同于set操作

$redis->setrange('str' , 2 , 'cd'); //返回4，表示从第2个字符后替换，这时‘str’ 为 ‘abcd’
```

9. substr 部分获取操作

```php
$redis->substr('str' , 0 , 2);//返回abc 表示从第0个起，取到第2个字符串

$redis->strlen('str'); // 返回4 此时‘str’ 为‘abcd’
```

10.  setbit 位存储

```php
  $redis->setbit('library' , 31 ,1); // 表示在第31位存入1
```

   getbit 位获取

```php
$redis->getbit('library' , 31); //返回1
```

 11.  keys 模糊查找功能，支持 * 号 以及 ？号 （匹配一个字符）

```php
$redis->set('foo1',123);

$redis->set('foo2' , 456);

$redis->keys('foo*'); //返回foo1和foo2的array

$redis->keys('f?0?'); // 同上
```

  12.  randomkey  随机返回一个key

```php
$redis->randomkey(); //可能是返回‘foo1’ 或者是foo2 及其它任何已存在的key
```

 13. rename/renamenx 方式对key进行改名，所不同的是renamenx不允许改成已存在的key

```php
$redis->rename('str','str2'); // 把原先命名为 str 的key改成了 str2
```

14. expire 设置key-value的时效性

```php
ttl  获取剩余有效期

persist  重新设置为永久存储

$redis->expire('foo' , 10);//设置有效期为10秒

$redis->ttl('foo'); // 返回剩余有效期值10秒

$redis->persist("fool");//取消有效期，变为永久存储
```

15. dbsize 返回redis当前数据库的记录总数

```php
  $redis->dbsize();
```

 16. 队列操作

  rpush/rpushx 有序列表操作，从队列后插入元素；

  lpush/lpushx 和 rpush/rpushx 的区别是插入到队列的头部，同上,‘x’含义是只对已存在的key进行操作

```php
 $redis->rpush('foolist' , 'bar1'); //返回列表长度1

 $redis->rpush('foolist' , 'bar0'); // 返回列表长度2

 $redis->rpushx('foolist' , 'bar2'); // 返回3 ， rpushx只对已存在的队列做添加，否则返回0

 $redis->llen('foolist'); //返回 3
```

17. lrange 返回队列中一个区间的元素

```php
$redis->lrange('foolist' , 0 , 1); //返回数组包含第0个至第1个，共2个元素

$redis->lrange('foolist' , 0 , -1);//返回第0个至倒数第一个，相当于返回所有元素  
```

18. lindex 返回指定顺序位置的list元素

```php
$redis->lindex('foolist' , 1); //返回bar1
```

19. lset 修改队列中指定位置的value 

```php
$redis->lset('foolist' , 1 ,'123'); // 修改位置1的元素，返回true
```

20. lrem 删除队列中左起指定数量的字符

```php
$redis->lrem("foolist" , 1 , '_'); //删除队列中左起（右起使用-1）1个字符‘_’（若有）
```

21. lpop/rpop 类似栈结构地弹出（并删除）最左或最右的一个元素

```php
$redis->lpop('foolist');//左侧返回

$redis->rpop('foolist'); // 右侧返回
```

22. ltrim 队列修改，保留左边起若干元素，其余删除

```php
$redis->ltrim('foolist' , 0 , 1);   //  保留左边起第0个至第1个元素
```

23. rpoplpush 从一个队列中pop元素并push到另一个队列

```php
$redis->rpush('list1' , 'ab0');

$redis->rpush('list1','ab1');

$redis->rpush('list2' , 'ab2');

$redis->rpush('list2' , "ab3");

$redis->rpoplpush('list1' , "list2"); 

$redis->rpoplpush('list2' , 'list2'); 
```

24. linsert在队列的中间指定元素前或后插入元素

```php
$redis->linsert('list2' , 'before' , 'ab1' , '123');//表示在元素 ‘ab1’ 之前插入‘123’

$redis->linser('list2' , 'after' , 'ab1' , "456");//表示在元素 ‘ab1’ 之后插入
```

25. blpop/brpop 阻塞并等待一个队列不为空时，在pop出最左或最右的一个元素（这个功能在php以外可以说非常好用）

```php
$redis->blpop('list3' , 10) ; //如果list3 为空则一直等待，知道不为空时将第一个元素弹出，10秒后超时
```

26. set集合操作

sadd增加set集合元素，返回true，重复返回false

```php
$redis->sadd('set1' , 'ab');

$redis->sadd('set1' , 'cd');

$redis->sadd('set1' , 'ef');

$redis->smembers("set1");  // 查看集合元素
```

27. srem 移除指定元素

```php
$redis->srem('set1' , 'cd');//删除‘cd’ 元素
```

28. spop弹出首元素

```php
 $redis->spop("set1");//返回‘ab’
```

29. smove移动当前set集合的指定元素到另一个set集合

```php
$redis->sadd("set2",'123');

$redis->smove('set1','set2','ab');//移动set1中的ab到set2 ,返回true or false;此时 set1 集合不存在 ab 这个值
```

30. scard 返回当前set表元素个数

```php
$redis->scard('set2');//返回2
```

31. sismember判断元素是否属于当前set集合

```php
$redis->sismember('set2','123'); //返回true or false
```

32. smembers返回当前set集合的所有元素

```php
$redis->smember('set2'); //返回array(123,ab)
```

33. sinter/sunion/sdiff 返回两个表中的交集/并集/补集

```php
$redis->sadd('set1' , 'ab');

$redis->sinter('set2' , 'set1');//返回array('ab');
```

sinterstore/sunionstore/sdiffstore 将两个表交集/并集/补集元素copy到第三个表中

```php
$redis->set('foo' , 0);

$redis->sinterstore('foo' , 'set1');//等同于将set1 的内容copy到foo中，并将foo转为set表

$redis->sinterstore('foo' , array('set1' , 'set2'));//将set1和set2中相同的元素copy到foo表中，覆盖foo原有内容
```

srandmember 返回表中一个随即元素

```php
$redis->srandmember('set1');
```

有序 set 表操作

zadd 增加元素，并设置序号，成功返回true，重复返回false

```php
$redis->zadd("zset1" , 1 , 'ab');

$redis->zadd('zset1' , 2 , 'cd');

$redis->zadd('zset1' , 3 , 'ef');
```

zincrBy对指定元素索引值的增减，改变元素排序次序

```php
$redis->zincryBy('zset1' , 10 , 'ab');  //返回11
```

zrem 移除指定元素

```php
$redis->zrem('zset1' , 'ef');//返回true  or  false
```

 zrange按位置次序返回表中指定区间的元素

```php
$redis->zrange("zset1" , 0 , 1);//返回位置0 和 1 之间（两个）的元素

$redis->zrange('zset1' , 1 , -1);//返回位置0和倒数第一个元素之间的元素（相当于所有元素）

```
zrevrange同上，返回表中指定区间的元素，按次序倒排

```php
$redis->zrevrange('zset1' , 0 ,-1);//元素顺序和zrange相反
```

zrangeByscore/zrevrangeByscore 按顺序/降序返回表中指定索引区间的元素

```php
$redis->zadd('zset1' , 3 , 'ef');

$redis->zadd('zset1' , 5 , 'gh');

$redis->zrangeByscore('zset1' , 2, 9);//返回索引值2-9之间的元素array('ef' , 'gh');

$redis->zrangeByscore('zset1' , 2 ,9 ,array('withscores'=>true, 'limit'=>array(1,2)));//返回索引值2-9之间的元素，withscores=>true表示包含索引值；limit=>array(1,2),表示偏移1，返回2条，结果为array(array('ef',3),array('gh',5))
```

zcount统计一个索引区间的元素个数

```php
$redis->zcount('zset1' , 3 , 5);//返回2

$redis->zcount('zset1' , '(3' , 5 ) );//’（3‘ 表示索引的值在3-5之间但不含3，同理也可以使用’（5‘ 表示上限为5但不含5
```

zcard 统计元素个数

```php
$redis->zcard('zset1');//返回4
```

zremrangeByscore删除一个索引区间的元素

```php
$redis->zremrangeByscore('zset1' , 0 ,  2);//删除索引在0-2之间的元素（ab ,  cd），返回删除元素个数2
```

zrank/zrevrank返回元素所在表顺序/降序的位置（不是索引）

```php
$redis->zrank('zset1' , 'ef');//返回0，因为它是一个元素；zrevrank则返回1（最后一个）
```

zremrangeByrank删除表中指定位置区间的元素

```php
$redis->zremrangeByrank('zset1' , 0  ,  10);//删除位置为0-10的元素，返回删除的元素个数2
```

hash表操作

```php
$redis->hset('hash1' , 'key1' , 'v1');//将key为key1,value为v1的元素存入hash1表

$redis->hset("hash1" , 'key2' , 'v2');

$redis->hget('hash1' , 'key1');//取出表hash1中的key   key  key1的值，返回v1
```

hexists返回hash表中的指定key是否存在

```php
$redis->hexists("hash1" , 'key1');//true 或 false
```

hdel 删除hash表中指定key的元素

```php
$redis->hdel('hash' , 'key2');//true  or  false
```

hlen 返回hash表元素个数

```php
$redis->hlen('hash1'); // 返回1
```

hsetnx增加一个元素，但不能重复

```php
$redis->hsetnx('hash1' , 'key1' , 'v2');

$redis->hsetnx('hash1' , 'key2' , 'v2');
```

hmset/hmget存取多个元素到hash表

```php
$redis->hmset( 'hash1' , array('key3'=>'v3' , 'key4'=>'v4' ) );

$redis->hmget( 'hash1' , array('key3' , 'key4') );//返回响应的值 array('v3' , 'v4');
```

hincryby 对指定key进行累加

```php
$redis->hincryBy('hash1' , 'key5' ,  3); //不存在，则存储并返回3 ；存在，即返回原有值 +3

$redis->hincryBy("hash1" , 'key5' , 10);//返回13
```

hkeys返回hash表中的所有key

```php
$redis->hkeys('hash1'); // 返回array('key1' , 'key2' , 'key3' , 'key4' , 'key5');
```

hvals 返回hash表中的所有value

```php
$redis->hvals('hash1'); // 返回array('v1' , 'v2' , 'v3' , 'v4' , 13);
```

hgetall返回整个hash表元素

```php
$redis->hgetall('hash1');//返回hash1所有表元素
```

排序操作

sort排序

```php
$redis->rpush('tab' , 3);

$redis->rpush('tab' , 2);

$redis->rpush('tab' , '17');

$redis->sort('tab');//返回array(2,3,17);

$redis->sort('tab' , array('sort'=>'desc'));//降序排序，返回array(17 , 3, 2)

$redis->sort('tab' , array('limit'=>array(1,2)));//返回顺序位置中1的元素2个（这里的2是指个数，而不是位置），返回array(3,17)

$redis->sort('tab' , array('limit'=>array('alpha'=>true)));//按首字符排序返回array(17 , 2 , 3 )，因为17的首字符是 1 所以排首位置

$redis->sort('tab' , array('limit'=>array('store'=>'ordered')));//表示永久性排序，返回元素个数

$redis->sort('tab' , array("limit"=>array('get'=>'pre_*')));//使用了通配符 * 过滤元素，表示只返回以pre开头的元素
```

### Redis 管理操作

info显示服务当状态信息

```php
$redis->info();
```

select指定要操作的数据库

```php
$redis->select(4)；//指定数据库的下标
```

flushdb清空当前库

```php
$redis->flushdb();
```

move移动当库的元素到其它数据库

```php
$redis->set('tomove' , 'bar');

$redis->move('tomove' , 4);
```

slaveof 配置从服务器

```php
$redis->slaveof('127.0.0.1' , 80);//配置127.0.0.1端口80的服务器为从服务器

$redis->slaveof();//消除从服务器
```

同步保存服务器数据到磁盘

```php
$redis->save();
```

异步保存服务器数据到磁盘

```php
$redis->bgsave()
```

返回最后更新磁盘的时间

```php
$redis->lastsave();
```

