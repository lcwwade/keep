#### 一、服务器启动

``` shell
1.redis-server

//在后台启动
2.redis-server &

// 使用配置文件：端口等信息可以在conf.xml中指定，所以推荐启动带配置文件
3.redis-server &{redis.conf}

//指定端口
4.redis-server --port ${port}
```



#### 二、客户端启停

```shell
1.redis-cli

2.redis-cli -p ${port}

// -host 默认是本地 127.0.0.0
3.redis-cli -h ${ip}

// -a auth认证
4.redis-cli -a ${password}

//组合使用
5.redis-cli -p ${port} -h ${ip} -a ${password}

//关闭(默认关闭端口6379)
6.redis-cli shutdown 

//指定端口关闭
7.redis-cli -p ${port} shutdown

8.redis-cli -p ${port} -h ${ip} shutdown
```

 

#### 三、常用命令

```shell
//测试连接
0.ping

//查看信息信息
1.info

//清除当前DB 键值信息
2.flushdb

//删除所有
3.flushall

//查看大小
4.dbsize

//手动触发持久化；注：shutdown时候会进行持久化，直接终止服务端不会持久化
5.save

//退出
6.quit

//redis Select 命令用于切换到指定的数据库，数据库索引号index用数字值指定，以0作为起始索引值
7.select index

```



#### 四、键(Key)命令

``` shell
//查看所有的键值
1.key *

//设置
2.set key value

//获取
3.get key

//删除key，1成功，0失败
4.del 

//判断值是否存在，1存在，0不存在
5.exists (key)

//查看key剩余生存时间，单位秒；-1表示无过期时间，-2表示不存在
6.ttl key

//设置a的过期时间为10s，例如用于模拟session的留存期
7.expire a 10

//查询key的类型，返回值如”string“
8.type key 

//随机key
9.randomkey

//把a重命名为d，不管d是否已存在，直接覆
10.reaname a d 

//以nx结尾的命令内部都有一些逻辑判断
11.renamenx a b (假设b已经存在，重命名就失败)

//select
```

#### 五、String类型

```shell
//切换命名空间
select 0

//设置{”key“:”val“}
1.set key val 

//设置expire时间，时间单位：秒
2.setex key 10 val

//设置expire时间，时间单位：毫秒
3.psetex key 10000 val

//获取值
4.get key

//获取一个闭区间的值 [0, 2],如key =”wordword“
5.getrange key 0 2
返回：”wor“

//先get, 后重新set，如：key =”val1“
6.getset key val2
返回：val1

//批量添加，key-val形式
7.mset key1 val1 key2 val2

//批量获取
8.mget key1 key2

//当前key不存在时才会成功，已存在则失败
9.setnx key newvalue

//有一个已存在，全都失败，具有原子性
10.msetnx key1 val1 key2 val2

//字符串的长度
11.strlen key

//自动增加/减少1，key需要为Integer
12.incr key / decr key

//每次增加/减少指定数值，如下面100
13.incrby key 100  /decrby key 100

//append追加，类似java append "str"+"appendstr" = "strappendstr"
14.append key appendstr

```



#### 六、Hash类型

> Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象

```shell
//切换命名空间
select 1
 
//设置值,key表示hash表
1.hset hashtable key value

//判断是否存在
2.hexists hashtable key

//获取hash值
3.hget hashtable key

//获取所有的 hash键值对
4.hgetall hashtable

//获取所有的keys
5.hkeys hashtable

//获取所有的vals
6.hvals hashtable

//获取key的个数
7.hlen hashtable

//批量指定key获取值
8.hmget hashtable key1 key2

//批量插入
9.hmset hashtable key1 value1 key2 value2

//批量删除
10.hdel hashtable key1 key2 ...

//判断不存在的才set
11.hsetnx hashtable key1 value


```

#### 七、List类型

```
	//切换命名空间
  select 2

	//往”list“里插入”1“”2“”3“”4“”5“”6“六个值 -> 6 5 4 3 2 1 
	1.lpush list 1 2 3 4 5 6
	- keys * =>  "list"
	
	//list的长度，此处返回6
	2.llen list
	
	//范围中取，最后放的在最前排
	3.lrange list 0 2 
	-> 6 5 4 
	
	//指定位置插入值.
	4.lset list 0 100
	-> 100 5 4 3 2 1 
	
	//获取索引位置的值
	5.lindex list 0
	-> 100
	
	//移除最左的
	6.lpop list
  -> 此处移除100
  
	//移除最右的
	7.rpop list
	-> 此处移除1
	
```



#### 八、Set类型

无序集合

```shell
//切换命名空间
select 3

//往集合set1里添加 ‘a’ ‘b’ ‘c’ ‘d’
1.sadd set1 a b c d 

//集合的数量
2.scard set1

//重命名set集合
3.rename set1 setNew

//查看元素成员
4.smembers setNew

//比较差异，返回 key1中独有的
//sadd set1 a b c d 
//sadd set2 c e d f 
5.sdiff set1 set2
->"b" "a" 返回set1中独有的
sdiff set2 set1
->"f" "e" 返回set2中独有的

//获取交集
6.sinter set1 set2

//获取并集
7.sunion set1 set2

//随机获取set1中n个值
8.srandmember set1 n

//判断某个值是否集合中的元素，判断a是否set1中元素
9.sismember set1 a

//移除1个或多个成员
10.srem set1 a b ..

//移除并返回一个随机的值；
11.spop set2

```



#### 九、Sorted Set集合

类似linkedHashSet

>Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。
>
>不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
>
>有序集合的成员是唯一的,但分数(score)却可以重复。
>
>集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。

```shell
//切换命名空间
select 4

// 往sortset集合中添加
1.zadd sortset1 100 a 200 b 300 c

// 重命名为sortset
2.rename sortset1 sortset

//查看个数
3.zcard sortset
-> 3

//查看具体key-value
4.zscore sortset a
->100
zscore sortset b
->200

//统计指定区间的个数,[0,220]
5.zcount sortset 0 220
->2

//返回指定序列的索引
6.zrank sortset a
->0

//a增加1000分 
7.zincrby sortset 1000 a
->1100

//取索引 0,100 的元素，即101个元素 （withscores）添加则同时返回分数
8.zrange sortset 0 100 （withscores）
->'a','b','c'  （此处仅有三个，都返回）

```

