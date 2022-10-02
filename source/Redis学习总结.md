# Redis学习总结

## 产生原因

1.用户量越来越多. 2.由此产生的同时在线人数多(并发量高),

究其原因,IO磁盘速度慢,承受不住,读取,存入,消耗大,导致宕机,蹦了.还有一点是数据库关系复杂

## 解决思路

减少磁盘读写,使用高速的内存

减少数据库关系,使用非关系数据库

## 解决方案

使用Nosql,指的是一类非关系型数据库,高数据,高并发下使用,优点是扩展性强,大数据下性能高,

常见非关系数据库有Redis,memcache,HBase,MongoDB

## Redis操作

常规数据类型有 string hash list set  sorted_set

1. 使用`redis-server.exe`开启服务

2. 使用`redis-cli.exe`操作redis

   

### string类型

类似于java中的`Map<String,String> `,实际上会转化为`List<String>`

```java
set key value  //设置值
get key  //得到值
mset key1 value1 key2 value2 key3 value3  //添加多组
mget key1 key2 key3 //获取多组
strlen key //对应值的字符串长度
append key value  //拼接字符串 比如原来key:value是name:aaa  追加 name:bbb 最终得到aaabbb字符串没有的话就相当于set的作用
incr key //类似于mysql自增主键 ,会得到key对应的值使之+1 ,原来value是3 那么变成4
incr key increment //设置步长 ,比如每次+10
incrbyfloat key increment //同理 每次加float类型的数字 比如1.5 原来value是3 那么变成4.5
setex key seconds value  //设置某个键值的有效时间(秒),如果添加相同的键,那么会被覆盖,也就是失效了
persist //时效性转化为永久的
ttl key //剩余时间适合所有数据类型,为-2表示key不存在了,-1表示已过期
psetex key milliseconds value //顾名思义,有效时间毫秒为单位 
```

#### 举例子

一个主播,有多少关注,有多少粉丝

```java
set user:id:324:focus 300  //设置id为324的用户有300个关注
set user:id:324:fans 30000  //设置id为324的用户有300个粉丝
set user:id:324 {id:324,focus:300,fans,30000} //设置为json的格式
incr user:id:324:fans  //自增一个粉丝
```

### hash类型

类似于java中的`Map<String,HashMap<String,String>>` ,实际上会转化为`Map<String,String>` `

```java
hset key field value  //设置某个键下的其中一个键值对,已存在则覆盖
hsetnx key field value  //设置某个键下的其中一个键值对,已存在则无效
hmset key field1 value1 field2 value2//设置某个键下的多个键值对,有则覆盖,没有则添加
hget key field // 获取某个键下的某个键的值
hgetall key //获取键下的所有键值对
hdel key field //删除某个键下的某个键值对
hget key field1 field2  //获取某个键下的某几个键对应的值
hlen key //得到当前键下有多少组键值对
hexists key field//判断当前键下是否有某个字段
hkeys key//获取键下的所有键值对的键
hvals key//获取键下的所有键值对的值
hincreby key field increment//设置某个键下的其中一个键的值加(int)
hincrebyfloat key field increment//同理,表示加上float类型的数字
    
```

![image-20200321201234999](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200321201234999.png)

![image-20200321201336615](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200321201336615.png)

![image-20200321201129330](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200321201129330.png)

![image-20200321202045655](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200321202045655.png)

![image-20200321202354556](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200321202354556.png)

**注意点**

比如user下可以存储2的32次-1 组键值对,而且**不能嵌套**,类似于java的结构

```
Map<String,HashMap<String,HashMap<String,String>>> 
```

最好不要滥用,因为键值对过多便利遍历效率就非常低,因此就失去了高效性

![image-20200321212035882](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200321212035882.png)

这种方法,没有得到商品的具体信息,只存储了一个商品主键和数量而已

##### 优化方案

一组键值对存储,商品id和数量,一组存储商品的整个对象信息,

数量信息的key用`商品id:nums`表示, 商品信息的key用`商品id:info`表示

比如用户id为003下的购物车g01编号的商品为100个 ,信息就是一个json字符串

![image-20200322111418148](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200322111418148.png)

![image-20200322114227983](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200322114227983.png)

hset原先没有,新增成功返回1  ,原先有,覆盖成功返回0

hsetnx原先没有,新增成功返回1,原先有,则该命令无效返回0

比如上边的hsetnx修改数量400,并没有覆盖,因为原先就有所以返回0 

## list

类似于java的LinkedList底层使用双向链表结构

```java
lpush key value1 value2 value3  ....//从左边插入
rpush key value1 value2 value3  ....//从右边插入 
lrange key start end //从左边start下标开始遍历到end下标,包含开始结尾end如果是-1表示倒数第一个,所以 0 -1 表示列出全部数据
lindex key index //返回制定下标的元素
llen key //返回列表长度
lpop key //左边开始得到元素,并移除
rpop key //右边开始得到元素,并移除
lrem key count value //从左删除列表是value的count个元素
```

![image-20200323161913252](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200323161913252.png)

![image-20200323163533736](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200323163533736.png)

![image-20200323165408611](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200323165408611.png)

## set

类似于java的`Set<String>`集合,但他是hash的拓展完全一致,存取大量数据,顺序不一致,元素不允许重复,查询效率高.重点是随机,因此常用于,新闻推荐,热点歌单推荐,热点新闻推荐,

```java
sadd key value //给集合添加值
smembers key//遍历
srem key key//删除集合的元素
scard key //集合个数
sismember key value //集合key中是否包含value这个元素,有则返回1

srandmember key count //随机获取集合指定count个的元素
spop key //随机从集合中拿出来一个元素,原集合会删除该元素,类似于栈的pop操作
```



![image-20200330110524406](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330110524406.png)



![image-20200330112438106](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330112438106.png)



set还常用于比如qq的共同好友,经常在好友添加中会出来,我们有几个共同好友,微信公众号中有几位朋友共同关注

```
sinter key1 key2  //求key1和key2的交集,也就是相同的部分,集合(1,2,3)交集(3,4,5)就是(3)
sunion key1 key2  //求key1和key2的并集,把两个集合加在一起,集合(1,2,3)加(3,4,5)就是(1,2,3,4,5)
sdiff key1 key2  //求key1和key2的差集,key1减去key2,比如集合(1,2,3)减去(3,4,5)就是(1,2)

sinterstore key3 key1 key2 //把key1和key2的交集存储到集合key3中
sunionstore key3 key1 key2 //把key1和key2的并集存储到集合key3中
sdiffstore key3 key1 key2 //把key1和key2的差集存储到集合key3中

smove key1 key2 value //把key1集合的value元素添加到key2集合中
```

![image-20200330114215328](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330114215328.png)

set还可用于权限判断,网站访问统计

PV:网站点击次数  uv:用户的访问次数一个账号算一次  IP :不同ip的访问次数

因为set具有不允许元素重复,因此同一个ip下次再进入时set会添加失败

![image-20200330132233552](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330132233552.png)



## sorted_set

这个和set和hash都很相似, set是由hash转化来的 ,而sorted_set是由set转化来的,仅仅是多了一个排序的score字段,可以用来存值,但是排序才是这个字段的存在意义

![image-20200330134419380](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330134419380.png)



```
zadd key score value //给集合key添加value序号为score (如果有则会替换掉score,value是不会替换的)
zrange key start end //从start开始排序遍历到end ,0到-1表示下标为0开始到倒数第一个也就是整个集合
zrange key start end withscores //表示遍历时加上score这个字段
zrank key value //获取几个key的value值的下标 (注意不是按照添加顺序的下标)而是score排好序后的下标
zscore key value //获取集合key的指定value的score
zincrby key 数字 value //集合key的指定value的score加上几
zcard key//集合的元素有几个
zcard key min max //在min到max这个范围内有几个元素
time //获取当前系统时间 会得到两列,上面一个是秒,下面一个是秒更小的单位
```



![image-20200330134922364](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330134922364.png)

```
zrangebyscore key 范围 withscores //查询集合中 某个范围内的数据 升序
zrevrangebyscore key 范围 withscores //查询集合中 某个范围内的数据 降序
zrangebyscore key 范围 limit 范围 withscores //查询集合中某个范围内的数据,之后再从这个数据拿出指定个数的数据

zremrangebyrank key start stop //删除 指定下标范围内(包括两端)的元素 0 1 表示删除下标0和1的元素
zremrangebyscore key min max //删除 指定score范围内(包括两端)的所有元素 10 30 表示删除这个范围内的所有元素,
```



![image-20200330140854483](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330140854483.png)



```
//在集合key中添加value1序号为score1 ,添加value2序号为score2,添加value3序号为score3
zadd key score1 value1 score2 value2 score3 value3 
//在集合key中添加(key1和key2和key3的交集) ,个数(比如3后边就需要加3个集合)
zinterstore key 个数 key1 key2 key3 
//只筛选出最大的那个,比如筛选图中三个集合 会得出 bb 60 aa 70的结果
zinterstore key 个数 key1 key2 key3 aggregate max 
```

如图所示  aa和bb是三个集合中共有的 因此会把score值相加

![image-20200330143815623](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200330143815623.png)

把时间作为score那么可以应用于定时任务执行顺序管理



## 持久化

### RDB

数据快照形式,就是一段时间内保存一次,就算出故障也只会损失一点,恢复数据速度更快

#### 具体操作

- 在redis.cli中使用save命令,这是单线程操作,会出现阻塞
- 使用bgsave命令,异步操作,比第一种好,由于另外的线程,因此需要额外消耗

- 在Redis.conf中

```java
dir 路径 //快照文件存放路径
dbfilename dump.rdb //快照保存文件
rdbcompression yes //开启压缩
rdbchecksum yes //开启加载检测
// save 10 4 表示每10秒写入(key变化)了4次那就保存(bgsave)数据一次保存到dump.rdb文件中
save second changes  
```

### 缺点

每次读写全部数据,效率低,大数据量情况下IO性能低下,由于bgsave会创建额外线程,内存消耗更大,系统异常时候虽然丢失数据并没有那么大了,但终究还是会丢失数据。

### AOF

日志形式:不保存数据,只保存操作过程的记录,类似于SVN和Git版本控制的记录

这个是Redis的主流方式,而且是实时性的

- always(每次):只要有写入操作就记录,虽然可以零误差但是性能降低
- everysec(每秒):每次将缓冲区(**存放一秒内的操作记录**)的指令写到AOF文件中,准确性也较高,也只会丢失缓冲区的数据,性能高(默认采用方式)
- no(系统控制):不可控,不采纳

```
dir 路径 //日志文件存放路径,与快照文件保持一致就行
appendfilename appendonly.aof //日志文件,建议修改文件名为appendonly-端口号.aof
appendonly yes //开启aof
appendfsync everysec  //每秒写入方式
```

### AOF重写

随着不断操作,AOF文件会越来越大,因此将采用重写机制压缩文件,比如运行3次`inc key `等价于1次的 inc key3

此时3行命令就转化为了1行,这就是压缩的原理

- bgrewriteaof(手动重写)顾名思义:后台重写aof
- auto-aof-rewrite-percentage 超过多少百分比重写
- auto-aof-rewrite-min-size 超过多少大小重写
- 上边两者满足一个则触发bgrewriteaof
- 举例子`auto-aof-rewrite-percentage 50  auto-aof-rewrite-min-size 1mb   `  此时aof文件达到1mb触发了,1.5mb又会触发,2.25mb又会触发,因为(2.25-1.5)/1.5=50%,也就是超过上一次文件大小的150%就会触发



## 总结

| 持久化方式   | RDB              | AOF                      |
| ------------ | ---------------- | ------------------------ |
| 占用存储空间 | 小               | 大(因为每秒存储)         |
| 存储速度     | 慢               | 快(只存储指令)           |
| 恢复速度     | 快(数据直接恢复) | 慢(指令要转化)           |
| 数据安全性   | 会丢失           | 丢失小(默认方式最多一秒) |
| 资源消耗     | 大               | 小                       |
| 启动优先级   | 低               | 高(默认启动)             |



如果对数据敏感,采用AOF,如果追求恢复速度快,

可以承受短时的数据丢失,那shiyongRDB,灾难恢复常用方式

redis两者都开启,优先使用AOF恢复数据 

### 应用场景

抢购,限购,限量发放,最新消息展示,黑名单白名单设定,按此结算的服务

## 事务

```java
multi//开启事务
exec //事务结束,并输出之前所有命令的执行结果
watch key1 key2 .... //监听key,如果变了,则会撤回事务中的命令,永远返回OK
unwatch key key1 key2 ....//取消监听
```



如果语法错误,执行,那么之前的命令全部出撤销

![image-20200406151623231](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200406151623231.png)

如果语法正确,执行时报错,那么输入exec后,正确的命令会执行,该条错误命令则不执行

![image-20200406151648619](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200406151648619.png)

第一个客户端监听了name和age,如果第二个客户端同时也在修改name或者age,那么事务会回滚,操作清空

![image-20200406151455302](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200406151455302.png)

`

## 分布式锁

redis是单线程,多个客户端对同一个数据操控,就得用到`setnx lock-key value`比如`setnx lock-name 1`其中的value随意设置,执行后返回value,如果返回的是0那么就是设置失败了,因为有另一个客户端也在操作这把锁,而且没有释放,使用`del  lock-name`就可以删除name的锁,另外的客户端也就能操作了

这里有一个问题如果加了锁没有释放,那么其他客户端就永远不能操作了,因此需要给锁加上一个有效时间,时间到了就删除key 

#### 这里有三种删除策略



```java
expire lock-key time //每次setnx设置完锁之后就需要设置有效时间(单位秒),时间用完自动释放锁
peexpire lock-key millisecondss //单位毫秒
```

只要设置了expire,Redis就会设置一个expire空间,地址为key,有效时间为value,地址是唯一的,通过地址可以找到数据

##### 定时删除

时间到达则立即删除该key

定时器优点:节约内存,到时就删除,释放不必要的内存占用

定时器缺点:CPU压力大,会一直占用cpu

时间(cpu响应时间)换空间(存储空间)

##### 惰性删除

时间就算到了也不会删除,直到你去获取他,发现已经过期了,才会删除,在获取数据之前都会有一个函数判断`expireIfNeeded()`检查数据是否已过期

优点:节约CPU性能,需要删除时才删除

缺点:浪费内存空间,数据将长期占用内存

空间换时间

##### 定期删除

折中方案,固定时间每秒随机挑选某一个expires[index]的n个key,n是自定义的,过期则删除, 当过期key除以n大于25%,说明过期的key较多,那么再来一次随机挑选,直到小于25%为止,换到下一个expires[index]空间,这个函数是`activeExpireCycle()`,因为这个函数也是很有执行时间的,所以要用current_db记录上次循环到哪个expires空间了,下次函数来时接着循环.

![image-20200412124722968](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200412124722968.png)





![image-20200412125213492](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200412125213492.png)

## 逐出算法

当新数据进来时Redis内存不够使用,要用`freeMemoryIfNeeded()`判断,就需要删除部分数据,才能放得下新数据,这个删除部分数据的策略就是逐出算法,这个过程也许会失败,那么就要反复执行,当所有数据执行后都不行,那么会返货错误信息,其中有8种策略,常使用的是最近较少被访问的删除策略volatile-lru(Least Recently Used)



## 日志配置

```java
loglevel debug|verbose|notice|warning  //4中日志级别
logfile 端口号.log  //日志记录文件
```

开发期期间,设置为verbose,生产环境中设置为notice 

## 高级数据类型

### bitmaps

这个数据类型类似于boolean,只有0和1

1 1 1 1 1 1 1 1 1 1 1 1

```java
setbit key offset value //设置键为key 的下标为offset 的值为value
getbit key offset  //获取下标offset的值
bitcount key  //计算1的数量
```

offset可以是很大比如10000000,那么就需要预设这么多位,消耗性能

![image-20200412141510373](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200412141510373.png)

### HyperLogLog

只存储数量,不存储数据,带去重功能,添加相同数量时,添加不进去

```java
pfadd key element .... //添加数据
pfcount key1  ..... //统计数据
pfmerge destkey sourcekey //合并数据
```

## GEO

用于坐标计算

```java
geoadd key 横坐标 纵坐标 点的名称 //添加键的坐标
geopos key 点的名称//获取点的坐标,得到的值看不懂,以后再深究
    getdist key 点1 点2  //获取两点间距离 的那位可以是m,km
georadius key 经度  纬度  radius//以指定坐标(经度,纬度)为中心求范围(radius)内的所有点
georadiusbymenmber key member radius//根据点(member)为中心求范围(radius)内的所有点
geohash key member //获取指定点对应的坐标hash值
```

![image-20200412151353551](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200412151353551.png)

## 主从复制

当项目数据量十分庞大时一台Redis服务器已经无法满足,因此需要多条服务器,一台写,多台读

用于写的Redis数据库是master主库,读的多台数据库是slave从库,**主库中写入的数据要与其他多台服务器同步**,这就是需要解决的问题

![image-20200412154430052](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200412154430052.png)

这样做有什么好处呢?读写分离可以提高服务器的负载能力,也就是负载均衡.

如果master坏了会从读服务器中选一个作为master

### 主从复制工作流程

slave服务器发送指令请求建立连接 `slaveof ip port `,master接受指令,如果ip和port都正确了,就响应对方,并保存master 的IP和端口`masterhost`和`masterport`之间建立了socket连接后会创建命令缓冲区,生成的RDB快照文件就可以进行信息的交换,期间还会不停的ping对方,为了证明对方连接中,slave就会接受执行RDB同步过程,完成后发送master表达已经同步完成操作,`info`可以查看相关连接信息信息,使用`slaveof no one`断开连接



![image-20200412171502822](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200412171502822.png)

第二步数据的同步,master会创建复制缓冲区,然后发送,slave就接受数据,此期间**会造成服务器阻塞或者数据不同步**因此次期间需要关闭对外服务`slave-serve-stale-data yes|no`,

![image-20200418095748657](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200418095748657.png)

传输过程中可能会存在不可避免的物理因素:**断网**,这就需要一个身份码`runid`来进行断点重传用`offset` 来标记

![image-20200418101358772](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200418101358772.png)

![image-20200418105046925](C:\Users\25006\AppData\Roaming\Typora\typora-user-images\image-20200418105046925.png)

### 心跳机制

上边说了一个为了确定对方是否在线而ping的过程,其中有两个关键变量ping 的时间间隔和响应时间,直接决定是否关闭连接

### 哨兵

顾名思义就是监控系统也是redis服务器只是不对外提供服务,对整一个主从结构进行监控,观察是否各个服务器是否正常,异常就会通知其他客户端,并选择一个slave作为master

`redis-sentinel sentinel-端口号.conf`