# Redis



```
计划：
redis->kafka->flink
各种olap数据库：ApacheIceBerg  Doris  StarRocks
spark scala项目
spark webUI学习
```



```
20241209：01-15
20241210：16-19-  22 - 实战10
20241212：实战02-优惠券秒杀2 没看完
20241217：看09 分布式锁66-95 实战没看  从96分布式缓存看的
20241223：100-114
20241223：未看高级篇-多级缓存-04-JVM进程缓存--高级篇-多级缓存-21-课程总结
```

根据以上总结未看：

```
分布式锁66-95
高级篇-多级缓存-04-JVM进程缓存--高级篇-多级缓存-21-课程总结
```





[TOC]



## 1 基础

基于内存的键值型NoSQL数据库。

### 1.1 初识Redis

#### 1.1.1 认识Nosql

NoSQL库，即NoSQL数据库，是“Not Only SQL”的缩写，泛指非关系型的数据库。

#### 1.1.2 认识Redis

特征：

- 键值型key-value，value支持多种不同的数据结构
- 单线程，每个命令具备原子性。安全的
- 低延迟、速度快（基于内存、IO多路复用）
- 支持数据的持久化
- 支持主从集群、分片集群



#### 1.1.3 安装Redis

```
windows下安装的链接为：https://blog.csdn.net/qq_24718237/article/details/79147025?ops_request_misc=&request_id=&biz_id=102&utm_term=redis%E5%AE%89%E8%A3%85&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-6-79147025.142^v100^pc_search_result_base5&spm=1018.2226.3001.4187

桌面客户端：https://github.com/RedisInsight/RedisDesktopManager
免费版桌面链接客户端：https://github.com/lework/RedisDesktopManager-Windows/releases
```





### 1.2 Redis常用命令



![](image\redis_image\re1.png)



#### 1.2.1 Redis数据结构介绍

key-value型  key一般是String类型，value的类型多种多样。

![](image\redis_image\re2.png)



#### 1.2.2 Redis通用命令

```
https://redis.io/docs/latest/commands/

查看通用命令： help @generic
```

![](image\redis_image\re3.png)



以下为常用的key的展示： **keys del exists expire TTL** 

```
keys：查看符合模板的所有key  生产环境不建议使用，单线程占用很大资源
redis> KEYS *name*
1) "lastname"
2) "firstname"
redis> KEYS a??
1) "age"
redis> KEYS *
1) "lastname"
2) "age"
3) "firstname"

del：删除一个或多个指定的key
> SET key1 "Hello"
"OK"
> SET key2 "World"
"OK"
> DEL key1 key2 key3
(integer) 2

exists：判断key是否存在
127.0.0.1:6379> set key1 k-1
OK
127.0.0.1:6379> set key2 k-2
OK
127.0.0.1:6379> exists key1 key2 key3
(integer) 2
127.0.0.1:6379>

expire：给key设置有效期，到期自动删除
ttl：查看key的剩余有效期 time to live ttl
没有设置有效期的key，通过ttl查看时会为-1，代表永久有效不会删除，建议增加有效期。


```



#### 1.2.3 String类型

![](image\redis_image\re4.png)

![](image\redis_image\re5.png)

#### 1.2.3 key的层级

Redis没有类似mysql中Table的概念，应该怎么区分不同类型的key呢？比如商品id和用户id

Redis中的key允许多个单词形成层级结构，多个单词之间使用:隔开，比如： 项目名:业务名:类型:id

比如用户和产品的key设置为： heima:user:1 和 heima:product:1

可以将java中的对象序列化成为json格式存储到value中

```
127.0.0.1:6379> set heima:user:1 {"id":1,"name":"heima-user1"}
Invalid argument(s)
127.0.0.1:6379> set heima:user:1 '{"id":1,"name":"heima-user1"}'
OK
127.0.0.1:6379> keys *
1) "key2"
2) "a"
3) "age"
4) "name"
5) "heima:user:1"
127.0.0.1:6379> get heima*
(nil)
127.0.0.1:6379> get heima:user:1
"{\"id\":1,\"name\":\"heima-user1\"}"
127.0.0.1:6379>

```

![](image\redis_image\re6.png)



#### 1.2.4 Hash类型

hash类型也叫散列，其value是一个无序字典，类似于java中的HashMap结构。

![](image\redis_image\re7.png)

![](image\redis_image\re8.png)





#### 1.2.5 List类型

Redis中的List类型和Java中的LinkedList类似，可以看作是一个双向链表结构。既可以正向检索也可以反向检索。

特征也和LinkedList类似：

- 有序
- 元素可以重复
- 插入删除快
- 查询速度一般

![](image\redis_image\re9.png)





#### 1.2.6 Set类型

与Java中的HashSet类似，可以看作是一个value为null的HashMap。因为也是一个hash表，因此具备与hashSet类似的特征：

- 无序
- 元素不可重复
- 查找快
- 支持交集、并集、差集等功能。

![](image\redis_image\re10.png)

![](image\redis_image\re11.png)





#### 1.2.7 SortedSet类型

可排序的set集合。SortedSet中的每一个元素都带有一个score属性，可以基于score属性对元素进行排序，底层实现是一个跳表+hash表。

sortedSet具备以下特性： 由于可排序性，经常被用来实现**排行榜**这样的功能。

- 可排序
- 元素不可重复
- 查询速度快

![](image\redis_image\re12.png)





### 1.3 Redis的Java客户端

```
https://redis.io/docs/latest/develop/clients/

以Java为例：
Jedis：以redis命令作为方法名称，简单实用。但是Jedis的实例是线程不安全的，多线程环境下需要基于连接池来使用。
lettuce：是基于Netty实现的，支持同步、异步和响应模式，是线程安全的。支持redis的哨兵模式、集群模式和管道模式。
Redisson：基于Redis实现的分布式可伸缩的Java数据结构集合。

SpringDataRedis： 整合了Jedis和lettuce
```



#### 1.3.1  Jedis

```
https://github.com/redis/jedis
```

##### 1.3.1.1 基本使用过程

```
基本使用步骤：
1.引入依赖
2.创建jedis对象，建立连接
3.操作
4.释放资源

package com.heima.test;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import redis.clients.jedis.Jedis;

import java.util.HashMap;
import java.util.Map;

public class JedisTest {

    private Jedis jedis;

    @BeforeEach
    void setUp(){
        jedis = new Jedis("127.0.0.1",6379);

        // 设置密码
        //jedis.auth("123321");

        jedis.select(0);
    }

    @Test
    void hashTest(){
        long hset = jedis.hset("jedis-hash-test1", "kasha", "aaaa");
        System.out.println(hset);
        Map<String, String> stringStringMap = jedis.hgetAll("jedis-hash-test1");
        System.out.println(stringStringMap);
    }
    @Test
    void stringTest(){
        String res = jedis.set("jedistest", "20241210-first-test");
        System.out.println(res);
        String jedistest = jedis.get("jedistest");
        System.out.println(jedistest);
    }

    @AfterEach
    void tearDown(){
        if(jedis != null){
            jedis.close();
        }
    }
}

```

##### 1.3.1.2 Jedis连接池

Jedis是线程不安全的，所以建议使用Jedis连接池代替Jedis直连方式。

```
package com.heima.jedis.util;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisConnectionFactory {

    private static final JedisPool jedisPool;

    static{

        // 配置连接池
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();

        jedisPoolConfig.setMaxTotal(8); // 最大连接数
        jedisPoolConfig.setMaxIdle(8);  // 最大空闲连接
        jedisPoolConfig.setMinIdle(0);  // 最小空闲连接
        jedisPoolConfig.setMaxWaitMillis(1000); // 连接池中无连接可用时，最大等待市场，-1代表无限等待
        // 创建连接池对象
        jedisPool = new JedisPool(jedisPoolConfig,"127.0.0.1",6379);
    }

    public static Jedis getJedis(){
        return jedisPool.getResource();
    }
}

```



#### 1.3.2 SpringDataRedis

```
https://spring.io/projects/spring-data-redis
```

##### 1.3.2.1 介绍

SpringData是Spring中数据操作的模块，包含了对各种数据库的集成，其中对redis的集成模块叫为SpringDataRedis.

![](image\redis_image\re13.png)

![](image\redis_image\re14.png)

##### 1.3.2.2 SpringDataRedis的序列化方式

RedisTemplate可以接收任意Object作为值写入redis，只不过写之前会把Object序列化成为字节形式，默认采用JDK序列化。

缺点：可读性差，占用内存大。

为了节省内存空间，并不会使用JSON序列化器来处理Value，而是统一使用String序列化器，要求只能存储String类型的key和value。当需要存储Java对象时，手动完成对象的序列化和反序列化。





## 2 实战



### 2.1 短信登陆

> redis的共享session应用



### 2.2 商店查询缓存

> 企业的缓存使用技巧：缓存雪崩、缓存穿透

#### 2.2.1 缓存

缓存就是数据交换的缓冲区，cache。存储数据的地方，一般读写性能较高。



#### 2.2.2 添加Redis缓存

![](image\redis_image\re15.png)



#### 2.2.3 缓存更新策略

主要有三种：内存淘汰、超时剔除、主动更新

![](image\redis_image\re16.png)



操作缓存和数据库时有三个问题需要考虑？

- 删除缓存还是更新缓存？

  ：更多选择删除缓存

- 如何保证缓存和数据库的操作同时成功或者失败？原子性

  ：单体系统，将缓存和数据库放在一个事务

  ​    分布式系统，利用TCC等分布式事务方案

- 先操作缓存还是先操作数据库？

  ：从线程安全角度，选择先操作数据库再删除缓存。

  

#### 2.2.4 缓存穿透、雪崩、击穿

**缓存穿透**：客户请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库。

**缓存雪崩**：在同一时间段大量的缓存key同时失效或者redis服务宕机，导致大量的请求到达数据库，造成巨大压力。

**缓存击穿**：也称为热点key的问题，就是一个被高并发访问并且缓存重建业务较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大冲击。



**缓存穿透的解决方案：**

- 缓存空对象：把值设置为空存到redis。简单粗暴，但是有额外内存消耗，很多垃圾信息，可以加TTL。
- 布隆过滤：在客户端和redis之间加一层布隆过滤器。内存占用少。实现相对复杂，存在误判可能。

![](image\redis_image\re17.png)

**缓存穿透的解决方案：**

- 给不同的key的TTL增加随机值，不同时过期-key失效
- 利用redis集群提高服务的可用性 -redis宕机
- 给业务添加多级缓存

**缓存击穿的解决方案：**

- 互斥锁：写入redis加入互斥锁
- 逻辑过期：在redis的value中加入expire字段，设置逻辑过期时间



![](image\redis_image\re18.png)

![](image\redis_image\re19.png)





### 2.3 达人探店

> 基于list的点赞列表，基于sortedSet的点赞排行榜



### 2.4 优惠券秒杀活动

> redis的计数器、lua脚本的redis 分布式锁 三种消息队列



#### 2.4.1 分布式锁

什么是分布式锁：满足分布式系统或者集群规模下多进程可见并且互斥的锁。

- 多进程可见：比如分布式下多个jvm可见

- 互斥：
- 高可用：
- 高性能/高并发：
- 安全性：

![](image\redis_image\re20.png)



#### 2.4.2 基于redis实现分布式锁

1. 获取锁：互斥 基于redis的：setnx lock thread
2. 释放锁：【手动释放 基于redis的：del lock】 【还有超时释放，增加ttl expire lock 5】

上边考虑到获取锁时，宕机，还未释放锁。所以增加了超时释放，即使释放锁失败，还有超时释放兜底。但是现在如果获取锁后，超时释放也没来得及做，怎么办？原子性：让获取锁和ttl都成功。

set lock thread1 EX 10 NX



#### 2.4.3 Lua脚本

Redis提供Lua脚本功能，在一个脚本中编写多条redis命令，确保多条命令执行的原子性。

可以参考网站：https://www.runoob.com/lua/lua-tutorial.html

```
redis的调用函数：
redis.call('set','name','Kasha')
local name = redis.call('get','name')
return name

写好上述脚本后，需要用redis命令调用： 后边的0代表脚本需要的key类型的参数个数
EVAL "return redis.call('set','name','Kasha')" 0
```

redis脚本中参数的类型分为两种：一种是key一种是其他。key类型参数会放入KEYS数组，其他参数会放入ARGV数组，再脚本中可以从这些数组中获取这些参数。

lua脚本中数组的下标是从1开始的，以下为一个案例

```
EVAL "return redis.call('set',KEYS[1],ARGV[1])" 1 name Kasha
上述lua脚本代表有一个key参数，其余为其他参数。那么KEYS[1]就是name
```



#### 2.4.4 Redisson

Redisson是在Redis的基础上实现的Java驻内存数据网络。提供了一系列的分布式的Java常用对象，还提供了分布式服务，包含各种分布式锁的实现。

github链接：https://github.com/redisson/redisson





### 2.5 好友关注

> 基于set集合的关注、取关、共同关注、消息推送等功能



### 2.6 用户签到

> redis的bitmap数据统计功能



### 2.7 uv统计

> redis的hyperLogLog的统计功能



## 3 分布式缓存

单节点redis遇到的



### 3.1 redis持久化

- RDB：Redis DataBase Backup file - 是将全部数据记录定时dump到磁盘上进行持久化，恢复数据通过读取原始数据加载到内存恢复数据，数据恢复快，效率高，但是数据容易丢失（数据同步有时间间隔）.快照文件称为RDB文件，默认是保存在当前运行目录。
- AOF：Append Only File追加文件，将操作日志追加形式写入文件，恢复数据通过命令恢复数据，缓存一致性更高，牺牲性能.



```
## RDB的执行
127.0.0.1:6379> save
OK
127.0.0.1:6379> bgsave
Background saving started

bgsave是后台执行。开启子进程执行RDB。避免对主进程造成影响。
```

![](image\redis_image\re21.png)



![](image\redis_image\re22.png)

优先级来说：AOF>RDB 

RDB也可以作为备份存在，容灾



### 3.2 redis主从

#### 3.2.1 架构



![](image\redis_image\re23.png)





#### 3.2.2 数据同步原理



![](image\redis_image\re24.png)

![](image\redis_image\re25.png)



![](image\redis_image\re26.png)

主从第一次同步是全量同步，但是如果slave重启后同步，则执行增量同步。

以下是增量同步原理

![](image\redis_image\re27.png)

增量同步失败的场景：slave宕机太久，repl_backlog超过了上限进行了覆盖，则只能进行全量同步然后再增量同步。



```
什么时候执行全量同步？：
	1.slave节点第一次连接master节点时
	2.slave节点断开时间太久，repl_backlog中的offset已经被覆盖时
什么时候执行增量同步？：
	1.slave节点断开并恢复，并且在repl_backlog中能找到offset时
```



### 3.3 redis哨兵

#### 3.3.1 哨兵的作用和原理

redis提供了哨兵Sentinel机制来实现主从集群的自动故障恢复。

![](image\redis_image\re28.png)

![](image\redis_image\re29.png)

选取新的mater

![](image\redis_image\re30.png)

故障转移的实现：

![](image\redis_image\re31.png)



### 3.4 redis分片集群

![](image\redis_image\re32.png)



![](image\redis_image\re33.png)

![](image\redis_image\re34.png)



![](image\redis_image\re35.png)



## 4 多级缓存



多级缓存方案

![](image\redis_image\re36.png)



### 4.1 JVM进程缓存



### 4.2 Lua语法入门



### 4.3 多级缓存



### 4.4 缓存同步策略
