#  Redis

```shell
#启动Redis服务端
[root@CentOS7 bin]# ./redis-server myredisconfig/redis.conf 

#查看相关进程
[root@CentOS7 bin]# ps -ef|grep redis

#用客户端进行连接
[root@CentOS7 bin]# ./redis-cli -p 6379

#相关进程
[root@CentOS7 ~]# ps -ef|grep redis
root       7880      1  0 Dec27 ?        00:00:00 ./redis-server 127.0.0.1:6379  #服务端
root       7994   2696  0 Dec27 pts/0    00:00:00 ./redis-cli -p 6379   #客户端
root       8201   8175  0 00:01 pts/1    00:00:00 grep --color=auto redis

#关闭redis
127.0.0.1:6379> shutdown
not connected> exit

info memory


#关闭后进程
[root@CentOS7 ~]# ps -ef|grep redis
root       8291   8175  0 00:03 pts/1    00:00:00 grep --color=auto redis




```

### 测试性能

#### redis-benchmark 官方自带的性能测试工具

```xml
redis 性能测试工具可选参数如下所示：

序号	选项	描述	默认值
1	-h	指定服务器主机名	127.0.0.1
2	-p	指定服务器端口	6379
3	-s	指定服务器 socket	
4	-c	指定并发连接数	50
5	-n	指定请求数	10000
6	-d	以字节的形式指定 SET/GET 值的数据大小	2
7	-k	1=keep alive 0=reconnect	1
8	-r	SET/GET/INCR 使用随机 key, SADD 使用随机值	
9	-P	通过管道传输 <numreq> 请求	1
10	-q	强制退出 redis。仅显示 query/sec 值	
11	--csv	以 CSV 格式输出	
12	-l（L 的小写字母）	生成循环，永久执行测试	
13	-t	仅运行以逗号分隔的测试命令列表。	
14	-I（i 的大写字母）	Idle 模式。仅打开 N 个 idle 连接并等待。	
```

>测试
>
>redis-benchmark -h localhost -p 6379 -c 100 -n 100000

```shell
Summary:
  throughput summary: 66313.00 requests per second   #每秒66313.00个请求
  latency summary (msec):    #完成50%所需时间      完成99%所需时间
          avg       min       p50       p95       p99       max
        1.051     0.560     1.047     1.447     1.623     3.127
====== SET ======                                                   
  100000 requests completed in 1.56 seconds     #10万个请求进行写入测试
  100 parallel clients  #100个并发客户端
  3 bytes payload    #每次写入3个字节
  keep alive: 1      #只有一台服务器处理
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no

```



### 1、基础知识

- redis默认有16个数据库，默认使用的是第0个，可以使用select来切换数据库

```shell
# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
```

常用命令：http://www.redis.cn/commands.html（官方文档）

```shell
127.0.0.1:6379> select 3  #切换数据库
OK

127.0.0.1:6379[3]> DBSIZE #显示数据库大小
(integer) 0

127.0.0.1:6379[3]> set name bobo #设置内容
OK

127.0.0.1:6379[3]> dbsize
(integer) 1

127.0.0.1:6379[3]> keys * #查看所有内容
1) "name"

127.0.0.1:6379[3]> flushdb#清空当前数据库
OK

127.0.0.1:6379> flushall  #清空全部数据库内容
OK

127.0.0.1:6379> EXPIRE name 10 #设置过期时间（秒）
(integer) 1

127.0.0.1:6379> ttl name #查看剩余时间
(integer) 4

127.0.0.1:6379> EXISTS name #判断该key是否存在
(integer) 1

127.0.0.1:6379> move name 1 #移动数据到数据库1
(integer) 1

127.0.0.1:6379> type age  #查看当前key的类型
string



```

>redis是单线程的

Redis是基于内存操作的，CPU并不是Redis性能瓶颈，而是内存和网络带宽决定的。

### 2、五大数据类型

- Redis-key
- String
- List
- Set
- Hash
- Zset

> String（字符串）

```shell
127.0.0.1:6379> set key v1
OK
127.0.0.1:6379> get key
"v1"
127.0.0.1:6379> APPEND key “bobo”  #往key中追加字符串，若不存在则新建，返回值为长度
(integer) 6
127.0.0.1:6379> get key
"v1bobo"
127.0.0.1:6379> STRLEN key  #获取字符串的长度
(integer) 6
========================================
127.0.0.1:6379> set view 0
OK
127.0.0.1:6379> get view
"0"
127.0.0.1:6379> INCR view #自增一
(integer) 1
127.0.0.1:6379> INCR view
(integer) 2
127.0.0.1:6379> get view
"2"
127.0.0.1:6379> DECR view #自减一
(integer) 1
127.0.0.1:6379> DECR view
(integer) 0
127.0.0.1:6379> get view
"0"
127.0.0.1:6379> INCRBY view 10 #指定步长增加
(integer) 10
127.0.0.1:6379> DECRby view 5  #指定步长减少
(integer) 5
127.0.0.1:6379> 
=========================================
#字符串范围
127.0.0.1:6379> set key hello
OK
127.0.0.1:6379> GETRANGE key 0 2 #截取字符串
"hel"
127.0.0.1:6379> GETRANGE key 0 -1
"hello"
#替换
127.0.0.1:6379> set name bobo
OK
127.0.0.1:6379> SETRANGE name 3 i #从指定位置开始替换字符串
(integer) 4
127.0.0.1:6379> get name
"bobi"
127.0.0.1:6379> 
==========================================
#setex（set with expire）设置key对应字符串value，并且设置key在给定的seconds时间之后超时过期
#SET mykey value
#EXPIRE mykey seconds

#setnx （set if not exit） 将key设置值为value，如果key不存在，这种情况下等同SET命令。 当key存在时，什么也不做
===========================================
127.0.0.1:6379> MSET k1 v1 k2 v2 k3 v3  #批量设置 k-v
OK
127.0.0.1:6379> keys *
1) "k2"
2) "k3"
3) "k1"
127.0.0.1:6379> MGET k1 k2 k3  #批量获取value
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> MSETNX k1 v k4 v4 #msetnx是原子性操作，要么一起成功，要么都不执行
(integer) 0
127.0.0.1:6379> get k4
(nil)
==============================================
#对象
#方法一
set user:1{name:bobo,age:23} #设置一个user：1对象 值为json字符串
#方法二 user：{id}：{filed}
127.0.0.1:6379> mset user:1:name bobo user:1:age 23
OK
127.0.0.1:6379> keys *
1) "k2"
2) "k3"
3) "user:1:name"
4) "k1"
5) "user:1:age"
127.0.0.1:6379> mget user:1:name user:1:age
1) "bobo"
2) "23"
127.0.0.1:6379> 
===============================================
#先get，并且输出值（不存在值返回nil）后再set getset
127.0.0.1:6379> GETSET temp redis
(nil)
127.0.0.1:6379> GETSET temp mysql
"redis"
127.0.0.1:6379> GETSET temp "sql server"
"mysql"
127.0.0.1:6379> get temp
"sql server"
```

> List 相当于链表 可以选择从左侧或右侧进行插入操作

```shell
127.0.0.1:6379> LPUSH mylist one two three
(integer) 3
127.0.0.1:6379> LRANGE mylist 0 -1
1) "three"
2) "two"
3) "one"

127.0.0.1:6379> RPUSH mylist02 one two three
(integer) 3
127.0.0.1:6379> LRANGE mylist02 0 -1
1) "one"
2) "two"
3) "three"
127.0.0.1:6379> 

#左进右出相当于队列，左进左出为栈
```

> set 不重复

```shell
127.0.0.1:6379> SADD myset "hello"
(integer) 1
127.0.0.1:6379> SADD myset "bobo"
(integer) 1
127.0.0.1:6379> SADD myset "java"
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "java"
2) "bobo"
3) "hello"
127.0.0.1:6379> SADD myset "java" #重复的则不添加
(integer) 0
127.0.0.1:6379> SMEMBERS myset
1) "java"
2) "bobo"
3) "hello"
127.0.0.1:6379> SISMEMBER myset one #判断是否为set内成员
(integer) 0
127.0.0.1:6379> SISMEMBER myset bobo
(integer) 1
127.0.0.1:6379> 
=======================================
127.0.0.1:6379> SCARD myset   #set内成员个数
(integer) 3
127.0.0.1:6379> SREM myset bobo #移除某个set成员
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "java"
2) "hello"
127.0.0.1:6379> SRANDMEMBER myset  #随机抽选一个元素
"hello"
127.0.0.1:6379> SRANDMEMBER myset
"java"
127.0.0.1:6379> SRANDMEMBER myset 2 #随机抽选2个元素
1) "java"
2) "hello"
127.0.0.1:6379> SPOP myset   #随机弹出元素
"hello"
==========================================
#从一个set移动元素到另一个set
127.0.0.1:6379> SMEMBERS myset
1) "one"
2) "three"
3) "C"
4) "A"
5) "two"
6) "java"
7) "B"
127.0.0.1:6379> SMEMBERS set
1) "bobo"
127.0.0.1:6379> SMOVE myset set C
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "one"
2) "three"
3) "A"
4) "two"
5) "java"
6) "B"
127.0.0.1:6379> SMEMBERS set
1) "C"
2) "bobo"
127.0.0.1:6379> 
================================================
#差集、交集、并集
127.0.0.1:6379> sadd myset01 a b c d
(integer) 4
127.0.0.1:6379> sadd myset02 a b f g
(integer) 4
127.0.0.1:6379> SDIFF myset01 myset02  #差集
1) "d"
2) "c"
127.0.0.1:6379> SINTER  myset01 myset02 #交集
1) "b"
2) "a"
127.0.0.1:6379> SUNION myset01 myset02 #并集
1) "b"
2) "d"
3) "c"
4) "a"
5) "g"
6) "f"

```

> Hash(哈希)

Map集合，key-map，值是一个map集合

```shell
127.0.0.1:6379> HSET myhash name bobo age 23 #存值，值为键值对
(integer) 2
127.0.0.1:6379> HGET myhash name   #取值
"bobo"
127.0.0.1:6379> HGETALL myhash  #显示所有
1) "name"
2) "bobo"
3) "age"
4) "23"
127.0.0.1:6379> HDEL myhash name #删除指定内容
(integer) 1
127.0.0.1:6379> HGETALL myhash
1) "age"
2) "23"
127.0.0.1:6379> HGETALL myhash
1) "age"
2) "23"
3) "name"
4) "bobo"
5) "password"
6) "123456"
127.0.0.1:6379> HLEN myhash  #获取键值对个数
(integer) 3
127.0.0.1:6379> HEXISTS myhash name #判断指定value是否在hash中
(integer) 1
127.0.0.1:6379> HEXISTS myhash hash
(integer) 0
127.0.0.1:6379> 
127.0.0.1:6379> HKEYS myhash #获取所有key
1) "age"
2) "name"
3) "password"
127.0.0.1:6379> HVALS myhash #获取所有值
1) "23"
2) "bobo"
3) "123456"
 

```

> zset  有序集合可以排序，在set的基础上增加了一个值  zset k1 flag v1

### 3、三个特殊数据类型

> geospatial 地理空间  底层使用的是zset

应用场景：附近的人，计算城市距离等。

> hyperloglogs 

优点：占用内存是固定的，2^64不同的元素基数，只要12KB内存

基数：

A={1,3,5,7,9}

B={1,2,3,4,5}

基数（不重复的元素）=7（可以接受误差，0.81%的错误）

应用场景：访客人数（一个人访问网站多次，但只算一个人）

传统方式：在set中保存用户id，然后统计set中的元素个数，但这种方式保存了大量用户id，比较麻烦，因为并不需要记录用户的id

```shell
127.0.0.1:6379> PFADD mykey a b c d e f g h i j k l m n  #创建第一组元素
(integer) 1
127.0.0.1:6379> PFCOUNT mykey #统计第一组基数数量
(integer) 14
127.0.0.1:6379> PFADD mykey2 x y z
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2
(integer) 3
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2 #合并两组数据到mykey3 相当于并集
OK
127.0.0.1:6379> PFCOUNT mykey3 #查看并集数量
(integer) 17
127.0.0.1:6379> PFADD test a a a a a a b
(integer) 1
127.0.0.1:6379> PFCOUNT test #同一个元素只计数一次
(integer) 2

```

#### 不允许容错的话使用set或其他数据类型

> bitmap  位图，操作二进制位来记录数据，就只要0,1两个状态

统计用户信息（活跃、不活跃|登陆，未登录|365天打卡）两个状态的都可以使用bitmaps

```shell
127.0.0.1:6379> SETBIT sign 0 1  #记录一周打卡天数，周一：0，周二：1.。。。。
(integer) 0
127.0.0.1:6379> SETBIT sign 1 1
(integer) 0
127.0.0.1:6379> SETBIT sign 2 0
(integer) 0
127.0.0.1:6379> SETBIT sign 3 1
(integer) 0
127.0.0.1:6379> SETBIT sign 4 1
(integer) 0
127.0.0.1:6379> SETBIT sign 5 1
(integer) 0
127.0.0.1:6379> SETBIT sign 6 0
(integer) 0
#查看某天是否打卡
127.0.0.1:6379> GETBIT sign 3
(integer) 1
127.0.0.1:6379> GETBIT sign 6
(integer) 0
#统计操作，统计打卡天数，即值为1的
127.0.0.1:6379> BITCOUNT sign
(integer) 5
```

### 4、事务

Redis事务本质：一组命令的集合

一个事务中的所有命令都会被序列化，事务执行过程中会按照顺序执行

Redis单条命令是保证原子性的，但是事务不保证原子性。

Redis的事务没有隔离级别的概念，所有命令在事务中并没有直接被执行，只有发起执行命令时才会执行Exec

redis事务：

- 开启事务（MULTI）
- 命令入队
- 执行事务（EXEC）

> 正常执行事务！

```shell
127.0.0.1:6379> MULTI  #开启事务
OK
127.0.0.1:6379(TX)> set k1 v1 #命令入队
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> get k2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> EXEC #执行事务
1) OK
2) OK
3) "v2"
4) OK

```

> 放弃事务

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k4 v4
QUEUED
127.0.0.1:6379(TX)> DISCARD #放弃事务，队列中的命令都不会执行
OK
127.0.0.1:6379> get k4
(nil)

```

> 编译型异常（代码有问题，命令有错），事务中所有命令都不会执行

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> getset k3 #错误的命令
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379(TX)> set k4 v4
QUEUED
127.0.0.1:6379(TX)> EXEC #执行事务失败
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k1   #命令都未执行
(nil)
127.0.0.1:6379> 

```

> 运行时异常(比如1/0)，如果队列中存在语法性错误，但命令是正确的，其他命令仍会执行，错误命令会抛出异常

```shell
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> INCR k1 #语法正确，但字符串无法加一，编译时没问题，但运行时会报错
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> EXEC
1) (error) ERR value is not an integer or out of range #其他命令执行成功，出错的抛出异常
2) OK
3) OK
127.0.0.1:6379> get k2
"v2"

```

> 监控 watch

悲观锁：

- 很悲观，认为什么时候都会出问题，无论做什么都会加锁（消耗资源）

乐观锁：

- 很乐观，认为什么时候都不会出现问题，所以不会上锁，更新数据的时候去判断，在此期间是否有人修改过数据 
- 获取version
- 更新时比较version

```shell
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> WATCH money #监控money若被其他线程改变则告知事务，事务不会执行成功，返回nil
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 20
QUEUED
127.0.0.1:6379(TX)> INCRBY out 20
QUEUED
127.0.0.1:6379(TX)> EXEC
(nil)
127.0.0.1:6379> get money
"100"
===========================================
#执行成功
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 20
QUEUED
127.0.0.1:6379(TX)> INCRBY out 20
QUEUED
127.0.0.1:6379(TX)> EXEC
1) (integer) 80
2) (integer) 20
============================================
127.0.0.1:6379> UNWATCH
OK
127.0.0.1:6379> WATCH money
OK
```

使用watch相当于redis的乐观锁操作，改变后要先unwatch 在watch获取最新数据

### 5、Jedis

> 官方推荐的java连接开发工具

- 导入依赖

```xml
 <!--jedis-->
        <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>

<!--fastjson-->
        <!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.75</version>
        </dependency>
```

- 连接数据库

```java
public class TestPing {
    public static void main(String[] args) {
        // Jedis对象
        Jedis jedis = new Jedis("192.168.188.100",6379);
        //jedis所有命令就是之前学习的所有指令,指令===》方法
        System.out.println(jedis.ping());
   }
}
```

- 操作命令

```java
		System.out.println("增加键值对=="+jedis.set("name", "bo"));
        System.out.println("所有key=="+jedis.keys("*"));
        System.out.println("取值=="+jedis.get("money"));
```

事务操作

```java
        Transaction multi = jedis.multi();   //开启事务
        String user = jsonObject.toJSONString();

        try {
            multi.set("user1",user);
            multi.set("user2",user);

            int i = 1/0;                    //抛出异常，事务执行失败
            
            multi.exec();                   //执行事务
        } catch (Exception e) {
            multi.discard();                //如果出现异常放弃执行事务
            e.printStackTrace();
        } finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            jedis.close();                   //关闭连接
        }
```



- 断开连接

### 6、Springboot整合redis

springboot操作数据：spring-data jpa jdbc

- 添加启动器

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

springboot2.X以后jedis被替换为lettuce

- jedis采用的直连，多个线程操作的话是不安全的，想要避免的话可以采用pool
- lettuce：采用netty，实例可以在多个线程中共享，不存在线程不安全的情况，可以减少线程数量

自动配置类：RedisAutoConfiguration

```java
@ConditionalOnMissingBean(name = "redisTemplate")
```

可以自定义redisTemplate类，默认配置如下：

```java
@Bean
	@ConditionalOnMissingBean(name = "redisTemplate")
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //没有太多的配置，后期需要进行序列化的配置
        //使用的是<Object, Object>使用时要进行类型强转
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}
```



配置文件:RedisProperties,可以进行配置的配置项

```java
public class RedisProperties {

	/**
	 * Database index used by the connection factory.
	 */
	private int database = 0;

	/**
	 * Connection URL. Overrides host, port, and password. User is ignored. Example:
	 * redis://user:password@example.com:6379
	 */
	private String url;

	/**
	 * Redis server host.
	 */
	private String host = "localhost";

	/**
	 * Login username of the redis server.
	 */
	private String username;

	/**
	 * Login password of the redis server.
	 */
	private String password;

	/**
	 * Redis server port.
	 */
	private int port = 6379;

	/**
	 * Whether to enable SSL support.
	 */
	private boolean ssl;

```

- 配置连接

```yaml
spring:
  redis:
    host: 192.168.188.100
    port: 6379
```

- 测试

```java
/*opsForValue 操作字符串
        * opsForHash() 操作hash
        * opsForList() 操作list
        * opsForSet()  操作set
        * opsForZSet() 操作zset
        * opsForGeo()  操作地图
        * opsForHyperLogLog() 操作HyperLogLog*/
//数据库相关的操作如清空数据库需要使用连接来操作
 RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
 connection.flushDb();

/*调用RedisTemplate实例来使用redis*/
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void redis_test(){
        redisTemplate.opsForValue().set("java","redis");
        System.out.println(redisTemplate.opsForValue().get("java"));

    }
```

```shell
127.0.0.1:6379> keys *
1) "user2"
2) "\xac\xed\x00\x05t\x00\x04java"
3) "user1"
4) "money"
5) "out"
6) "name"

```

2) "\xac\xed\x00\x05t\x00\x04java" 如果不序列化的话key前面有乱码

默认的序列化方式是jdk自带的JdkSerializationRedisSerializer，我们可以自定义一个配置类来修改序列化方式。

```java
if (this.defaultSerializer == null) {
            this.defaultSerializer = new JdkSerializationRedisSerializer(this.classLoader != null ? this.classLoader : this.getClass().getClassLoader());
        }

        if (this.enableDefaultSerializer) {
            if (this.keySerializer == null) {
                this.keySerializer = this.defaultSerializer;
                defaultUsed = true;
            }

            if (this.valueSerializer == null) {
                this.valueSerializer = this.defaultSerializer;
                defaultUsed = true;
            }
```

自定义redis  template

```java
package com.yzu.config;


import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnSingleCandidate;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {


    /*编写我们自己的配置类从而自定义RedisTemplate*/
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);

        //Jackson序列化
        Jackson2JsonRedisSerializer<Object> serializer = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        serializer.setObjectMapper(objectMapper);

        /*string的序列化*/
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        /*配置template各种属性*/
        template.setKeySerializer(stringRedisSerializer);  //设置key序列化方式为string

        template.setHashKeySerializer(stringRedisSerializer);//设置hash-key序列化方式

        template.setValueSerializer(serializer);

        template.afterPropertiesSet();

        return template;
    }
}

```

#### 工作中一般不直接用，而是封装成工具类

### 7、持久化

在指定的时间间隔内将内存中的数据库状态保存到磁盘中（内存的信息是断电即消失的），生成快照文件，恢复时是将快照文件读到内存

- #### RDB（默认）

- #### AOF

> RDB (Redis DataBase)

会单独创建一个子进程来进行持久化，会将数据先写入到一个临时文件中，待持久化结束后，再用临时文件来替换上次持久化好的文件

#### 保存的文件：dump.rdb

```shell
[root@CentOS7 bin]# ls
dump.rdb  myredisconfig  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server

```



```xml
# Enables or disables full sanitation checks for ziplist and listpack etc when
# loading an RDB or RESTORE payload. This reduces the chances of a assertion or
# crash later on while processing commands.
# Options:
#   no         - Never perform full sanitation
#   yes        - Always perform full sanitation
#   clients    - Perform full sanitation only for user connections.
#                Excludes: RDB files, RESTORE commands received from the master
#                connection, and client connections which have the
#                skip-sanitize-payload ACL flag.
# The default should be 'clients' but since it currently affects cluster
# resharding via MIGRATE, it is temporarily set to 'no' by default.
#
# sanitize-dump-payload no

# The filename where to dump the DB
dbfilename dump.rdb
```

持久化时间间隔

```xml
# Save the DB to disk.
#
# save <seconds> <changes>
#
# Redis will save the DB if both the given number of seconds and the given
# number of write operations against the DB occurred.
#
# Snapshotting can be completely disabled with a single empty string argument
# as in following example:
#
# save ""
#
# Unless specified otherwise, by default Redis will save the DB:
#   * After 3600 seconds (an hour) if at least 1 key changed
#   * After 300 seconds (5 minutes) if at least 100 keys changed
#   * After 60 seconds if at least 10000 keys changed
#
# You can set these explicitly by uncommenting the three following lines.
#
# save 3600 1     
# save 300 100
# save 60 10000
```

#### save 3600 1    在3600秒内有1次修改就进行持久化 
#### save 300 100   在300秒内有100次修改就进行持久化 
#### save 60 10000   在60 秒内有10000次修改就进行持久化 

> 触发条件

- 满足save规则的情况下如一分钟修改10000次，会自动触发rdb规则
- 执行flushall命令，也会触发，生成rdb文件
- 退出redis，也会生成rdb文件

自动生成dump.rdb文件

> 恢复RDB文件

只需将rdb文件放到配置文件中设置的相应目录下，redis启动时会自动检查dump.rdb文件，恢复其中的数据

```shell
#查看存放目录
127.0.0.1:6379> CONFIG GET dir
1) "dir"
2) "/usr/local/bin"

```

### 优点：

- 适合大规模的数据恢复，父进程仍处理请求，由子进程负责处理持久化，效率和速度更快
- 如果对数据完整性要求不高可以使用（可能会发生数据丢失）

### 缺点：

- 需要一定时间间隔来进行操作，如果在时间间隔内宕机了，最后一次修改的数据就会发生丢失
- fork进程时会占用一定内存空间

> AOF (Append Only File)

将我们的所有命令都记录下来，恢复的时候就把这个文件全部执行一遍

以日志的形式来记录每个写操作（读操作不记录），只许追加不可以改写文件

#### 保存的文件：appendonly.aof

```shell
[root@CentOS7 bin]# ls
appendonly.aof  dump.rdb  myredisconfig  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server

```



```xml
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check https://redis.io/topics/persistence for more information.

appendonly no   

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"

```

默认不开启，需要进行配置 appendonly yes

持久化规则：

```xml
# appendfsync always
appendfsync everysec  #每秒都进行操作
# appendfsync no

```



执行操作：

```shell
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> set k2 v2
OK
127.0.0.1:6379> set k3 v3
OK
127.0.0.1:6379> 

```

记录的内容：

```xml
[root@CentOS7 bin]# vim appendonly.aof 

*2
$6
SELECT
$1
0
*3
$3
set
$2
k1
$2
v1
*3
$3
set
$2
k2
$2
v2
*3
$3
set
$2
k3
$2
v3

```

#### 如果aof文件被破坏，可以使用redis-check-aof来进行修复，但错误数据会被删除

### 8、发布订阅

发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发布消息，接收者（sub）接收消息

```shell
SUBSCRIBE channel_study   #订阅频道

PSUBSCRIBE pattern    #订阅符合给定模式的频道

PUBLISH channel message  #往channel频道发送消息message

```

```shell
127.0.0.1:6379> SUBSCRIBE bobo   #订阅bobo频道
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "bobo"
3) (integer) 1

```

```shell
127.0.0.1:6379> PUBLISH bobo hello,word  #发送消息
(integer) 1
127.0.0.1:6379> 

```

```shell
127.0.0.1:6379> SUBSCRIBE bobo
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "bobo"
3) (integer) 1
1) "message"       #自动接收消息
2) "bobo"
3) "hello,word"

```

