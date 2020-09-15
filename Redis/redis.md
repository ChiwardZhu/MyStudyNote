





# Linux安装

1. 下载

2. 解压

3. 安装c++环境

   ```bash
   # 如果redis版本为5那么则只需执行以下命令
   yum install gcc-c++
   
   # 如果redis版本为6那么则需要升级gcc版本到gcc9才行
   yum -y install centos-release-scl
   yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
   
   echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
   #最后重启服务器
   ```

4. 执行make与make install命令

   ```bash
   make
   make install
   ```

   ![image-20200623104520643](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623104520643.png)

5. redis默认安装路径```/usr/local/bin```

6. 在默认安装路径下新建一个目录，将配置文件复制到里面

   ![image-20200623104824394](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623104824394.png)

7. redis默认不是后台启动，修改配置文件```vim redis.conf```

   ![image-20200623105137695](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623105137695.png)

8. 启动redis服务

   ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623113112092.png)

9. 连接redis

   ![image-20200623110846978](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623110846978.png)

10. 查看redis的进程

    ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623113315335.png)

11. 如何关闭redis服务

    ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623113351362.png)





# 测试性能



# 基础知识

> 数据库的个数：16个

![image-20200623115736948](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200623115736948.png)

默认使用的是第0个，可以用select进行数据库的切换

```bash
select 数字	# 切换数据库
dbsize	# 查看数据库的大小
keys *	# 查看该数据库的所有键
flushdb	# 清空当前数据库
flushall	# 清除空所有数据库
```



> Redis是单线程的（Redis 在 6.0 之后加入了多线程）

Redis很快，它是基于内存操作，CPU不是Redis性能瓶颈，Redis的瓶颈是根据及其的内存和网络带宽，既然可以使用单线程来实现就使用单线程了 

Redis是C语言编写的

**Redis为什么用单线程？为什么单线程还那么快？**

https://www.cnblogs.com/blogtech/p/11742057.html

1. 多线程不一定比单线程 效率高。

2. Redis将所有数据放进内存中，单线程操作效率是最高的。原因：多线程的本质就是CPU模拟出来多个线程的情况，这种模拟出来的情况就有一个代价，就是上下文的切换，对于一个内存的系统来说，它没有上下文的切换就是效率最高的。

   因为一次CPU上下文的切换大概在 1500ns 左右。

   从内存中读取 1MB 的连续数据，耗时大约为 250us，假设1MB的数据由多个线程读取了1000次，那么就有1000次时间上下文的切换，

   那么就有1500ns * 1000 = 1500us ，我单线程的读完1MB数据才250us ,你光时间上下文的切换就用了1500us了，我还不算你每次读一点数据 的时间



# 五大数据类型

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作==数据库==、==缓存==和==消息中间件==。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [Bitmap](http://www.redis.cn/topics/data-types-intro.html#Bitmap)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。



## Redis-Key

```bash
keys *			# 查看所有的key
set	key名 值		# 设置key-value
exists key名		# 判断这个key是否存在
move key名 数据库下标		# 将这个key移动到指定数据库中
expire key名 时间		# 设置key的过期时间，单位是秒
ttl key名		# ttl（time to live）查看当前key的剩余时间
type key名		# 查看key的数据类型
```



## String

```bash
set	key value	# 设置key-value
get	key 		# 获取value值
keys *			# 获取所有的key
exists key		# 判断这个key是否存在
append key "追加的字符串"	# 追加字符串，如果key不存在，那么就相当于set key-value
strlen key		# 获取key相应value字符串的长度

###########################################################

incr key	# 对key所对应的value自增1
decr key	# 对key所对应的value自减1
INCRBY key	# 对key所对应的value设置增量
DECRBY key	# 对key所对应的value设置减量

###########################################################

GETRANGE key 下标1 下标2	# 截取字符串[下标1，下标2]下标从0开始
SETRANGE key 下标1 下标2	# 替换字符串[下标1，下标2]下标从0开始

###########################################################

setex（set with expire）
setnx（set if not exist）
setex key 时间 value	# 设置key-value同时设置过期时间
setnx key value	# 如果不存在该key就set key-value（在分布式锁中经常使用）

###########################################################

mset k1 v1 k2 v2 k3 v3...	# 同时设置多个值
mget k1 k2 k3...	# 同时获取多个值
msetnx k1 v1 k4 v4	# msetnx是原子性操作：要么一起成功，要么一起失败

###########################################################

# 对象
set user:1 {name:zhangsan,age:3}	# 设置一个user:1对象，值为json字符串

mset user:1:name zhangsan user:1:age 2	# user:{id}:{field}
mget user:1:name user:1:age

###########################################################

getset key value	# 先get再set；当key不存在时，就返回nil，并且set key-value；当						key已存在时，返回原来的value；并且set新的value
```



## List

```bash
lpush list名 元素	# 将一个或多个值，插入到列表首部
rpush list名 元素	# 将一个或多个值，插入到列表尾部

lrange list名 下标1 下标2	#获取该list[下标1,下标2]区间内的元素，并且

lpop list名	# 移除list第一个元素
rpop list名	# 移除list最后一个元素

lindex list名 下标	# 获取list指定下标的元素

llen list名	# 获取指定list的长度

lrem list名 个数 元素	# 移除该list中指定个数的元素

ltrim list名 下标1 下标2		# 截取list[下标1,下标2]区间内的元素，并且修改该list

rpoplpush list1 list2	# 移动list1的最后一个元素到list2中

lset list名 下标 元素	# 将list指定下标的值替换为另一个元素；如果不存在list或不存在该下标都会报错

# 插入一个元素到指定元素的前面或后面；如果不存在list或者指定的元素不存在都会报错
linsert list名 before(after) list中存在的元素 要插入的元素		
```

> 小结

* 实际上是一个链表，可以添加一个元素到列表的头部（左边）或者尾部（右边）
* 移除所有的元素，那么这个list就不存在了
* 在两边插入或改变值效率最高，插入或修改中间元素效率较低
* 可以做消息队列；队列（lpush、rpop）；栈（rpush、lpop）



## Set（不能重复）

```bash
sadd set名 元素		# 在指定set中添加元素

smembers set名		# 查看指定set中的所有元素

sismember set名 元素	# 判断set中是否存在该元素

scard set名			# 获取set中元素的个数

srem set名 元素		# 移除set中指定的元素

srandmember set名 个数（默认为1）	# 随机抽取指定个数的元素

spop set名	# 随机删除一个元素

smove set1 set2 元素	# 将set1中的元素移动到set2中

########################################################################

# 差集交集并集
sdiff set1 set2			#差集
sinter set1 set2		#交集
sunion set1 set2		#并集
```



## Hash（适合存对象）

```bash
hset key field value			# 将field-value对设置到哈希表key中
hsetnx key field value			# 如果field不存在哈希表key中，那么就将field-value对设置到哈希表key中
hget key field 					# 获取存储在哈希表中指定key的值。
hmset key f1 v1 f2 v2 f3 v3...	# 同时将多个field-value对设置到哈希表 key中。
hmget key f1 f2 f3... 			# 获取存储在哈希表中指定多个key的值。
hgetall key						# 获取哈希表key中的所有field-value

hdel key field					# 删除哈希表key中的指定的field-value

hlen key						# 获取哈希表key中field-value的数量

hexists key field				# 判断哈希表key中指定的字段是否存在

hkeys key						# 获取所有的field
hvalues key						# 获取所有的value

hincrby key field 数字（n）		# 将哈希表key中的field增加n
```



## Zset（有序）

```bash
ZADD key score1 member1 [score2 member2]	# 向有序集合添加一个或多个成员，或者更新已存在成员的分数

# 显示全部的用户并按从小到大顺序排列（-inf为-∞，+inf为+∞）
zrangebyscore key -inf（可为数字） +inf（可为数字，但必须前面小后面大）		
zrevrange key +inf -inf	# 与上述相似，但从大到小排序

zrem key member	# 移除有序集合中指定的成员

zcard key	# 获取有序集合中的成员数

zcount key member1 member2	# 获取指定区间有序集合中的成员数
```



## 三种特殊数据类型

### geospatial地理位置

朋友的位置，附近的人，打车距离计算

```bash
# geoadd 添加地理位置；一般都是直接下载城市数据并用java程序一次性导入
geoadd key 经度 纬度 member		# 添加一个或多个地理位置元素到一个key中 

# geopos 返回一个或多个位置的经纬度信息
geopos key member		

# GEODIST 返回一个key中指定两个位置之间的距离
GEODIST key member1 member2 [unit]	# unit可以指定长度单位：m(米)，km(千米)，ft(英尺)，mi(英里) 默认为m

# GEORADIUS  以给定位置为中心，半径不超过给定半径的附近所有位置
# longitude(经度) latitude(纬度) radius(半径) 
# [WITHCOORD]	返回在距离内他人的定位信息
# [WITHDIST]	返回距离
# [WITHHASH]	
# [COUNT count]	显示个数
GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]

# GEORADIUSBYMEMBER 和GEORADIUS相似，只是中心点不是指定经纬度，而是指定已添加的某个成员作为中心
GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]

# GEOHASH  返回一个或多个位置元素的 Geohash 表示，Geohash是一种经纬度散列算法(将二维的经纬度转为一维的字符串)
GEOHASH key member [member ...]
```

> geospatial的底层是Zset，我们可以使用Zset命令来操作geospatial



### HyperLogLog（可做：统计页面访问人数）

> 简介

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

#### **什么是基数?**

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。



```bash
# 添加指定元素到 HyperLogLog 中。
PFADD key element [element ...]

# 返回给定 HyperLogLog 的基数估算值(不重复元素的个数)。
PFCOUNT key [key ...]

# 将多个 HyperLogLog 合并为一个 HyperLogLog
PFMERGE destkey sourcekey [sourcekey ...]
```



### Bitmap

> 位存储

统计用户信息：是否登录；是否打卡。两个状态的，都可以使用 Bitmap

```bash
setbit key offset value		# 设置Bitmap
getbit key offset			# 获取指定Bitmap的值
bitcount key [start] [end]	# 获取Bitmap指定范围中值为1的个数
```



## 事务

**Redis 事务可以一次执行多个命令， 并且带有以下三个重要的保证：**

- 批量操作在发送 EXEC 命令前被放入队列缓存。
- 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
- 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

==Redis事务没有隔离级别的概念！==

==Redis单条命令保证原子性，但是事务不保证原子性！==

**一个事务从开始到执行会经历以下三个阶段：**

- 开始事务。(multi)
- 命令入队。()
- 执行事务。(exec)



**取消事务:	discard**



> 编译型异常（代码有问题，命令有错）

```bash
127.0.0.1:6379[3]> multi
OK
127.0.0.1:6379[3]> set k1 v1
QUEUED
127.0.0.1:6379[3]> set k2 v2
QUEUED
127.0.0.1:6379[3]> set k3 v3
QUEUED
127.0.0.1:6379[3]> getset k3	# 错误的命令
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379[3]> set k4 v4
QUEUED
127.0.0.1:6379[3]> exec		# 执行事务报错
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379[3]> get k1	#所有的命令都不会被执行
(nil)
```



> 运行时异常，如果事务队列中存在语法性错误，那么执行时，其他命令都可正常执行，错误命令抛出异常

```bash
127.0.0.1:6379[3]> set k1 "v1"
OK
127.0.0.1:6379[3]> multi
OK
127.0.0.1:6379[3]> incr k1	# 错误的命令
QUEUED
127.0.0.1:6379[3]> set k2 v2
QUEUED
127.0.0.1:6379[3]> set k3 v3
QUEUED
127.0.0.1:6379[3]> get k3
QUEUED
127.0.0.1:6379[3]> exec
1) (error) ERR value is not an integer or out of range	# 虽然命令有错误，但是其他命令依旧执行成功
2) OK
3) OK
4) "v3"
127.0.0.1:6379[3]> get k2
"v2"
```



> 监控：watch

**悲观锁：**很悲观，认为什么时候都会出问题，无论做什么都会加锁

**乐观锁：**很乐观，认为什么时候都不会出问题，所以不会上锁！但是更新数据的时候



> Redis监控测试

```bash
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money		# 监视 money 
OK
127.0.0.1:6379> multi		# 事务正常结束，数据期间没有发生变动，这个时候就正常执行
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

**测试多线程修改值，使用watch可以当做redis的乐观锁操作！**

```shell
# 线程 A
127.0.0.1:6379> watch money		# 监视 money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 10
QUEUED
127.0.0.1:6379> INCRBY out 10
QUEUED
127.0.0.1:6379> exec	# 执行之前，线程 B 修改了money（我们监视的变量），那么线程A的事务执行就会失败
(nil)

# 线程 B
127.0.0.1:6379> set money 1000	# 修改了money（我们监视的变量）
OK
```

**如果修改失败，就解锁获取最新的值**

```shell
127.0.0.1:6379> UNWATCH		# 如果发现事务执行失败，就先解锁
OK
127.0.0.1:6379> WATCH money		# 获取最新的值，再次监视
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> INCRBY money 10
QUEUED
127.0.0.1:6379> DECRBY out 10
QUEUED
127.0.0.1:6379> exec
1) (integer) 1010
2) (integer) 10
```



# Jedis

1. 导入依赖

```xml
<dependencies>
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.67</version>
    </dependency>
</dependencies>
```

2. 测试连接

   ```java
   public class TestPing {
       public static void main(String[] args) {
           Jedis jedis = new Jedis("127.0.0.1",6379);
           System.out.println(jedis.ping());
       }
   }
   //输出：PONG
   ```



# Springboot整合

springboot2.x 之后，原来使用的 jedis 被替换为 lettuce

**jedis：**采用直连，多个线程操作的话，是不安全的，如果要避免不安全的情况，需要使用jedis pool连接池，更像BIO模式

**lettuce：**采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据，更像NIO模式

**源码分析：**

```java
@Bean
@ConditionalOnMissingBean(name = "redisTemplate")// 我们可以自定义一个redisTemplate来替换这个默认的redisTemplate
public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
    throws UnknownHostException {
    // 默认的redisTemplate 没有过多的设置，redis 对象都需要序列化！
    // 两个泛型都是Object，我们需要使用强制转换<String,Object>
    RedisTemplate<Object, Object> template = new RedisTemplate<>();
    template.setConnectionFactory(redisConnectionFactory);
    return template;
}

@Bean
@ConditionalOnMissingBean// 由于String是redis中最常用的数据类型，所以单独提出一个Bean
public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
    throws UnknownHostException {
    StringRedisTemplate template = new StringRedisTemplate();
    template.setConnectionFactory(redisConnectionFactory);
    return template;
}
```

> 整合测试

1. 导入依赖

   ```xml
   <dependency>
   			<groupId>org.springframework.boot</groupId>
   			<artifactId>spring-boot-starter-data-redis</artifactId>
   		</dependency>
   ```

2. 配置连接

   ```properties
   spring.redis.host=127.0.0.1
   spring.redis.port=6379
   ```

3. 测试

   ```java
   @Autowired
   RedisTemplate redisTemplate;
   
   @Test
   void contextLoads() {
       //		redisTemplate.opsForValue();
       //		redisTemplate.opsForList();
       //		redisTemplate.opsForSet();
       //		redisTemplate.opsForHash();
       //		redisTemplate.opsForZSet();
       //		redisTemplate.opsForGeo();
       //		redisTemplate.opsForHyperLogLog();
   
       redisTemplate.opsForValue().set("k1","v1");
       System.out.println(redisTemplate.opsForValue().get("k1"));
   }
   ```

   ![image-20200625121734098](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625121734098.png)

   ![image-20200625121930830](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625121930830.png)

   

   **传入对象，需要序列化！**

   ![image-20200625103409044](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625103409044.png)

   

4. **RedisConfig**

   ```java
   package com.zhu.config;
   
   import com.fasterxml.jackson.annotation.JsonAutoDetect;
   import com.fasterxml.jackson.annotation.JsonTypeInfo;
   import com.fasterxml.jackson.annotation.PropertyAccessor;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import com.fasterxml.jackson.databind.jsontype.impl.LaissezFaireSubTypeValidator;
   import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.data.redis.connection.RedisConnectionFactory;
   import org.springframework.data.redis.core.RedisTemplate;
   import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
   import org.springframework.data.redis.serializer.StringRedisSerializer;
   
   import java.net.UnknownHostException;
   
   @Configuration
   public class RedisConfig {
       @Bean
       @SuppressWarnings("all")
       public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
               throws UnknownHostException {
           RedisTemplate<String, Object> template = new RedisTemplate<>();
           template.setConnectionFactory(redisConnectionFactory);
   
           //Json序列化配置
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
           ObjectMapper om=new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.activateDefaultTyping(LaissezFaireSubTypeValidator.instance,ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.WRAPPER_ARRAY);
           jackson2JsonRedisSerializer.setObjectMapper(om);
           //String的序列化
           StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
   
           // key采用String的序列化方式
           template.setKeySerializer(stringRedisSerializer);
           // hash的key采用String的序列化方式
           template.setHashKeySerializer(stringRedisSerializer);
           // value序列化方式采用Jackson
           template.setKeySerializer(jackson2JsonRedisSerializer);
           // hash的value采用Jackson的序列化方式
           template.setKeySerializer(jackson2JsonRedisSerializer);
           template.afterPropertiesSet();
           return template;
       }
   }
   ```

5. **RedisUtil**

   ```java
   package com.zhu.util;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.data.redis.core.RedisTemplate;
   import org.springframework.util.CollectionUtils;
   
   import java.util.List;
   import java.util.Map;
   import java.util.Set;
   import java.util.concurrent.TimeUnit;
   
   @Configuration
   public class RedisUtil {
       @Autowired
   
       private RedisTemplate<String, Object> redisTemplate;
   
       // =============================common============================
   
       /**
        * 26
        * 指定缓存失效时间
        * 27
        *
        * @param key  键
        *             28
        * @param time 时间(秒)
        *             29
        * @return 30
        */
   
       public boolean expire(String key, long time) {
   
           try {
   
               if (time > 0) {
   
                   redisTemplate.expire(key, time, TimeUnit.SECONDS);
   
               }
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 44
        * 根据key 获取过期时间
        * 45
        *
        * @param key 键 不能为null
        *            46
        * @return 时间(秒) 返回0代表为永久有效
        * 47
        */
   
       public long getExpire(String key) {
   
           return redisTemplate.getExpire(key, TimeUnit.SECONDS);
   
       }
   
       /**
        * 53
        * 判断key是否存在
        * 54
        *
        * @param key 键
        *            55
        * @return true 存在 false不存在
        * 56
        */
   
       public boolean hasKey(String key) {
   
           try {
   
               return redisTemplate.hasKey(key);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 67
        * 删除缓存
        * 68
        *
        * @param key 可以传一个值 或多个
        *            69
        */
   
       @SuppressWarnings("unchecked")
   
       public void del(String... key) {
   
           if (key != null && key.length > 0) {
   
               if (key.length == 1) {
   
                   redisTemplate.delete(key[0]);
   
               } else {
   
                   redisTemplate.delete(CollectionUtils.arrayToList(key));
   
               }
   
           }
   
       }
   
       // ============================String=============================
   
       /**
        * 83
        * 普通缓存获取
        * 84
        *
        * @param key 键
        *            85
        * @return 值
        * 86
        */
   
       public Object get(String key) {
   
           return key == null ? null : redisTemplate.opsForValue().get(key);
   
       }
   
       /**
        * 92
        * 普通缓存放入
        * 93
        *
        * @param key   键
        *              94
        * @param value 值
        *              95
        * @return true成功 false失败
        * 96
        */
   
       public boolean set(String key, Object value) {
   
           try {
   
               redisTemplate.opsForValue().set(key, value);
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 109
        * 普通缓存放入并设置时间
        * 110
        *
        * @param key   键
        *              111
        * @param value 值
        *              112
        * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
        *              113
        * @return true成功 false 失败
        * 114
        */
   
       public boolean set(String key, Object value, long time) {
   
           try {
   
               if (time > 0) {
   
                   redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
   
               } else {
   
                   set(key, value);
   
               }
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 130
        * 递增
        * 131
        *
        * @param key   键
        *              132
        * @param delta 要增加几(大于0)
        *              133
        * @return 134
        */
   
       public long incr(String key, long delta) {
   
           if (delta < 0) {
   
               throw new RuntimeException("递增因子必须大于0");
   
           }
   
           return redisTemplate.opsForValue().increment(key, delta);
   
       }
   
       /**
        * 143
        * 递减
        * 144
        *
        * @param key   键
        *              145
        * @param delta 要减少几(小于0)
        *              146
        * @return 147
        */
   
       public long decr(String key, long delta) {
   
           if (delta < 0) {
   
               throw new RuntimeException("递减因子必须大于0");
   
           }
   
           return redisTemplate.opsForValue().increment(key, -delta);
   
       }
   
       // ================================Map=================================
   
       /**
        * 157
        * HashGet
        * 158
        *
        * @param key  键 不能为null
        *             159
        * @param item 项 不能为null
        *             160
        * @return 值
        * 161
        */
   
       public Object hget(String key, String item) {
   
           return redisTemplate.opsForHash().get(key, item);
   
       }
   
       /**
        * 167
        * 获取hashKey对应的所有键值
        * 168
        *
        * @param key 键
        *            169
        * @return 对应的多个键值
        * 170
        */
   
       public Map<Object, Object> hmget(String key) {
   
           return redisTemplate.opsForHash().entries(key);
   
       }
   
       /**
        * 176
        * HashSet
        * 177
        *
        * @param key 键
        *            178
        * @param map 对应多个键值
        *            179
        * @return true 成功 false 失败
        * 180
        */
   
       public boolean hmset(String key, Map<String, Object> map) {
   
           try {
   
               redisTemplate.opsForHash().putAll(key, map);
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 192
        * HashSet 并设置时间
        * 193
        *
        * @param key  键
        *             194
        * @param map  对应多个键值
        *             195
        * @param time 时间(秒)
        *             196
        * @return true成功 false失败
        * 197
        */
   
       public boolean hmset(String key, Map<String, Object> map, long time) {
   
           try {
   
               redisTemplate.opsForHash().putAll(key, map);
   
               if (time > 0) {
   
                   expire(key, time);
   
               }
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 212
        * 向一张hash表中放入数据,如果不存在将创建
        * 213
        *
        * @param key   键
        *              214
        * @param item  项
        *              215
        * @param value 值
        *              216
        * @return true 成功 false失败
        * 217
        */
   
       public boolean hset(String key, String item, Object value) {
   
           try {
   
               redisTemplate.opsForHash().put(key, item, value);
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 229
        * 向一张hash表中放入数据,如果不存在将创建
        * 230
        *
        * @param key   键
        *              231
        * @param item  项
        *              232
        * @param value 值
        *              233
        * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
        *              234
        * @return true 成功 false失败
        * 235
        */
   
       public boolean hset(String key, String item, Object value, long time) {
   
           try {
   
               redisTemplate.opsForHash().put(key, item, value);
   
               if (time > 0) {
   
                   expire(key, time);
   
               }
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 250
        * 删除hash表中的值
        * 251
        *
        * @param key  键 不能为null
        *             252
        * @param item 项 可以使多个 不能为null
        *             253
        */
   
       public void hdel(String key, Object... item) {
   
           redisTemplate.opsForHash().delete(key, item);
   
       }
   
       /**
        * 259
        * 判断hash表中是否有该项的值
        * 260
        *
        * @param key  键 不能为null
        *             261
        * @param item 项 不能为null
        *             262
        * @return true 存在 false不存在
        * 263
        */
   
       public boolean hHasKey(String key, String item) {
   
           return redisTemplate.opsForHash().hasKey(key, item);
   
       }
   
       /**
        * 269
        * hash递增 如果不存在,就会创建一个 并把新增后的值返回
        * 270
        *
        * @param key  键
        *             271
        * @param item 项
        *             272
        * @param by   要增加几(大于0)
        *             273
        * @return 274
        */
   
       public double hincr(String key, String item, double by) {
   
           return redisTemplate.opsForHash().increment(key, item, by);
   
       }
   
       /**
        * 280
        * hash递减
        * 281
        *
        * @param key  键
        *             282
        * @param item 项
        *             283
        * @param by   要减少记(小于0)
        *             284
        * @return 285
        */
   
       public double hdecr(String key, String item, double by) {
   
           return redisTemplate.opsForHash().increment(key, item, -by);
   
       }
   
       // ============================set=============================
   
       /**
        * 292
        * 根据key获取Set中的所有值
        * 293
        *
        * @param key 键
        *            294
        * @return 295
        */
   
       public Set<Object> sGet(String key) {
   
           try {
   
               return redisTemplate.opsForSet().members(key);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return null;
   
           }
   
       }
   
       /**
        * 306
        * 根据value从一个set中查询,是否存在
        * 307
        *
        * @param key   键
        *              308
        * @param value 值
        *              309
        * @return true 存在 false不存在
        * 310
        */
   
       public boolean sHasKey(String key, Object value) {
   
           try {
   
               return redisTemplate.opsForSet().isMember(key, value);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 321
        * 将数据放入set缓存
        * 322
        *
        * @param key    键
        *               323
        * @param values 值 可以是多个
        *               324
        * @return 成功个数
        * 325
        */
   
       public long sSet(String key, Object... values) {
   
           try {
   
               return redisTemplate.opsForSet().add(key, values);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return 0;
   
           }
   
       }
   
       /**
        * 336
        * 将set数据放入缓存
        * 337
        *
        * @param key    键
        *               338
        * @param time   时间(秒)
        *               339
        * @param values 值 可以是多个
        *               340
        * @return 成功个数
        * 341
        */
   
       public long sSetAndTime(String key, long time, Object... values) {
   
           try {
   
               Long count = redisTemplate.opsForSet().add(key, values);
   
               if (time > 0) {
                   expire(key, time);
               }
   
               return count;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return 0;
   
           }
   
       }
   
       /**
        * 355
        * 获取set缓存的长度
        * 356
        *
        * @param key 键
        *            357
        * @return 358
        */
   
       public long sGetSetSize(String key) {
   
           try {
   
               return redisTemplate.opsForSet().size(key);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return 0;
   
           }
   
       }
   
       /**
        * 369
        * 移除值为value的
        * 370
        *
        * @param key    键
        *               371
        * @param values 值 可以是多个
        *               372
        * @return 移除的个数
        * 373
        */
   
       public long setRemove(String key, Object... values) {
   
           try {
   
               Long count = redisTemplate.opsForSet().remove(key, values);
   
               return count;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return 0;
   
           }
   
       }
   
       // ===============================list=================================
   
       /**
        * 386
        * 获取list缓存的内容
        * 387
        *
        * @param key   键
        *              388
        * @param start 开始
        *              389
        * @param end   结束 0 到 -1代表所有值
        *              390
        * @return 391
        */
   
       public List<Object> lGet(String key, long start, long end) {
   
           try {
   
               return redisTemplate.opsForList().range(key, start, end);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return null;
   
           }
   
       }
   
       /**
        * 402
        * 获取list缓存的长度
        * 403
        *
        * @param key 键
        *            404
        * @return 405
        */
   
       public long lGetListSize(String key) {
   
           try {
   
               return redisTemplate.opsForList().size(key);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return 0;
   
           }
   
       }
   
       /**
        * 416
        * 通过索引 获取list中的值
        * 417
        *
        * @param key   键
        *              418
        * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
        *              419
        * @return 420
        */
   
       public Object lGetIndex(String key, long index) {
   
           try {
   
               return redisTemplate.opsForList().index(key, index);
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return null;
   
           }
   
       }
   
       /**
        * 431
        * 将list放入缓存
        * 432
        *
        * @param key   键
        *              433
        * @param value 值
        *              434
        * @param time  时间(秒)
        *              435
        * @return 436
        */
   
       public boolean lSet(String key, Object value) {
   
           try {
   
               redisTemplate.opsForList().rightPush(key, value);
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 将list放入缓存
        *
        * @param key   键
        * @param value 值
        * @param time  时间(秒)
        * @return
        */
   
       public boolean lSet(String key, Object value, long time) {
   
           try {
   
               redisTemplate.opsForList().rightPush(key, value);
   
               if (time > 0) {
                   expire(key, time);
               }
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 467
        * 将list放入缓存
        * 468
        *
        * @param key   键
        *              469
        * @param value 值
        *              470
        * @param time  时间(秒)
        *              471
        * @return 472
        */
   
       public boolean lSet(String key, List<Object> value) {
   
           try {
   
               redisTemplate.opsForList().rightPushAll(key, value);
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 484
        * 将list放入缓存
        * 485
        * <p>
        * 486
        *
        * @param key   键
        *              487
        * @param value 值
        *              488
        * @param time  时间(秒)
        *              489
        * @return 490
        */
   
       public boolean lSet(String key, List<Object> value, long time) {
   
           try {
   
               redisTemplate.opsForList().rightPushAll(key, value);
   
               if (time > 0) {
                   expire(key, time);
               }
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 504
        * 根据索引修改list中的某条数据
        * 505
        *
        * @param key   键
        *              506
        * @param index 索引
        *              507
        * @param value 值
        *              508
        * @return 509
        */
   
       public boolean lUpdateIndex(String key, long index, Object value) {
   
           try {
   
               redisTemplate.opsForList().set(key, index, value);
   
               return true;
   
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return false;
   
           }
   
       }
   
       /**
        * 521
        * 移除N个值为value
        * 522
        *
        * @param key   键
        *              523
        * @param count 移除多少个
        *              524
        * @param value 值
        *              525
        * @return 移除的个数
        * 526
        */
   
       public long lRemove(String key, long count, Object value) {
   
           try {
               Long remove = redisTemplate.opsForList().remove(key, count, value);
               return remove;
           } catch (Exception e) {
   
               e.printStackTrace();
   
               return 0;
           }
       }
   }
   ```



# Redis.conf详解

> 开头说明

![image-20200625144310314](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625144310314.png)



> INCLUDES

![image-20200625145830756](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625145830756.png)

​		由于Redis只有一个配置文件，但是如果个人进行开发维护的话，那么就需要多个这样的配置文件，这时候多个配置文件就可以在此通过 include /path/to/local.conf 配置进来，而原本的 redis.conf 配置文件就作为一个总闸。

​		另外需要注意的时，如果将此配置写在redis.conf 文件的开头，那么后面的配置会覆盖引入文件的配置，如果想以引入文件的配置为主，那么需要将 include 配置写在 redis.conf 文件的末尾。



> MODULES

![image-20200625150859946](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625150859946.png)

​		redis3.0的爆炸功能是新增了集群，而redis4.0就是在3.0的基础上新增了许多功能，其中这里的 自定义模块配置就是其中之一。通过这里的 loadmodule 配置将引入自定义模块来新增一些功能。



> NETWORK

```shell
# bind:绑定redis服务器网卡IP，默认为127.0.0.1,即本地回环地址。这样的话，访问redis服务只能通过本机的客户端连接，而无法通过远程连接。如果bind选项为空的话，那会接受所有来自于可用网络接口的连接。
bind 127.0.0.1

# 是否开启保护模式
protected-mode yes

# port：指定redis运行的端口，默认是6379。由于Redis是单线程模型，因此单机开多个Redis进程的时候会修改端口。
port 6379

# 单位是秒，表示将周期性的使用SO_KEEPALIVE检测客户端是否还处于健康状态，避免服务器一直阻塞，官方给出的建议值是300s，如果设置为0，则不会周期性的检测。
tcp-backlog 511

# 设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接。默认值为0，表示不关闭。
timeout 0
```



> GENERAL

```shell
# 设置为yes表示指定Redis以守护进程的方式启动（后台启动）。默认值为 no
daemonize yes

# 配置PID文件路径，当redis作为守护进程运行的时候，它会把 pid 默认写到 /var/redis/run/redis_6379.pid 文件里面
pidfile /var/run/redis_6379.pid

# 设置日志级别
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice

# 配置log文件地址,默认打印在命令行终端的窗口上
logfile ""

# 设置数据库的数目。默认的数据库是DB 0 ，可以在每个连接上使用select <dbid> 命令选择一个不同的数据库，dbid是一个介于0到databases - 1 之间的数值。默认值是 16，也就是说默认Redis有16个数据库。
databases 16

# 是否总是显示LOGO
always-show-logo yes
```



> SNAPSHOTTING

```shell
# 这里是用来配置触发 Redis的持久化条件，也就是什么条件下会将内存中的数据保存到硬盘。默认如下配置：
save 900 1		# 表示900 秒内如果至少有 1 个 key 的值变化，就进行持久化操作
save 300 10		# 表示300 秒内如果至少有 10 个 key 的值变化，就进行持久化操作
save 60 10000	# 表示60 秒内如果至少有 10000 个 key 的值变化，就进行持久化操作

# 默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了
stop-writes-on-bgsave-error yes

# 默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大。
rdbcompression yes

# 默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。
rdbchecksum yes

# 设置快照的文件名，默认是 dump.rdb
dbfilename dump.rdb

# 设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。使用上面的 dbfilename 作为保存的文件名。
dir ./
```



> REPLICATION

````shell
replicaof <masterip> <masterport>	# masterip主机ip地址；masterport主机端口号

masterauth <master-password>	# 若主机有密码，则需要配置

replca-slave-serve-stale-data yes
# 默认值为yes。当一个 slave 与 master 失去联系，或者复制正在进行的时候，slave 可能会有两种表现：
# 1.如果为 yes，slave 仍然会应答客户端请求，但返回的数据可能是过时，或者数据可能是空的在第一次同步的时候 
# 2.如果为 no ，在你执行除了 info he salveof 之外的其他命令时，slave 都将返回一个 "SYNC with master in progress" 的错误

# 配置Redis的Slave实例是否接受写操作，即Slave是否为只读Redis。默认值为yes。
replica-read-only yes		

# 主从数据复制是否使用无硬盘复制功能。默认值为no。
repl-diskless-sync no

repl-diskless-sync-delay 5
# 当启用无硬盘备份，服务器等待一段时间后才会通过套接字向从站传送RDB文件，这个等待时间是可配置的。这一点很重要，因为一旦传送开始，就不可能再为一个新到达的从站服务。从站则要排队等待下一次RDB传送。因此服务器等待一段时间以期更多的从站到达。延迟时间以秒为单位，默认为5秒。要关掉这一功能，只需将它设置为0秒，传送会立即启动。默认值为5。

repl-disable-tcp-nodelay no
# 同步之后是否禁用从站上的TCP_NODELAY 如果你选择yes，redis会使用较少量的TCP包和带宽向从站发送数据。但这会导致在从站增加一点数据的延时。  Linux内核默认配置情况下最多40毫秒的延时。如果选择no，从站的数据延时不会那么多，但备份需要的带宽相对较多。默认情况下我们将潜在因素优化，但在高负载情况下或者在主从站都跳的情况下，把它切换为yes是个好主意。默认值为no。
````



> SECURITY

```shell
rename-command		# 命令重命名，对于一些危险命令例如：
	flushdb			# 清空数据库
	flushall		# 清空所有记录
	config			# 客户端连接后可配置服务器
	keys			# 客户端连接后可查看所有存在的键
    
# 作为服务端redis-server，常常需要禁用以上命令来使得服务器更加安全，禁用的具体做法是：
rename-command FLUSHALL ""

# 也可以保留命令但是不能轻易使用，重命名这个命令即可：
rename-command FLUSHALL abcdefg		#这样，重启服务器后则需要使用新命令来执行操作，否则服务器会报错unknown command。

requirepass	123456	# 设置redis连接密码为123456

# 但更多情况下我们会在命令行中操作
127.0.0.1:6379> config get requirepass
1) "requirepass"
2) ""
127.0.0.1:6379> config set requirepass "123456"
OK
127.0.0.1:6379> config get requirepass
1) "requirepass"
2) "123456"
```



> CLIENTS

```shell
# 设置客户端最大并发连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件。  描述符数-32（redis server自身会使用一些），如果设置 maxclients为0 。表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
maxclients 10000		# 设置能连接上redis的最大客户端数量
```



> MEMORY MANAGEMENT

```shell
# 设置Redis的最大内存，如果设置为0 。表示不作限制。通常是配合下面介绍的maxmemory-policy参数一起使用。
maxmemory <bytes>


maxmemory-policy 内存清除策略
# 当内存使用达到maxmemory设置的最大值时，redis使用的内存清除策略。有以下几种可以选择：

volatile-lru   		# 利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used ) 
allkeys-lru   		# 利用LRU算法移除任何key 
volatile-random 	# 移除设置过过期时间的随机key 
allkeys-random  	# 移除随机ke
volatile-ttl   		# 移除即将过期的key(minor TTL) 
noeviction     		# 不移除任何key，只是返回一个写错误 ，默认选项

# LRU 和 minimal TTL 算法都不是精准的算法，但是相对精确的算法(为了节省内存)。随意你可以选择样本大小进行检，redis默认选择5个样本进行检测，你可以通过maxmemory-samples进行设置样本数。10个样本进行检测接近真正的LRU，但需要更多的CPU。3个样本进行检测更快但不太准确
maxmemory-samples
```



> APPEND ONLY MODE

```shell
# redis默认使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。但是redis如果中途宕机，会导致可能有几分钟的数据丢失，根据save来策略进行持久化，Append Only File是另一种持久化方式，可以提供更好的持久化特性。Redis会把每次写入的数据在接收后都写入appendonly.aof文件，每次启动时Redis都会先把这个文件的数据读入内存里，先忽略RDB文件。默认值为no。
appendonly no

# aof文件名，默认是"appendonly.aof"
appendfilename "appendonly.aof"

# aof持久化策略的配置
appendfsync always		# always表示每次写入都执行fsync，以保证数据同步到磁盘
appendfsync everysec	# everysec表示每秒执行一次fsync，可能会导致丢失这1s数据
appendfsync no			# no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快

# 在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间。
# no-appendfsync-on-rewrite字段设置为默认设置为no。如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。Linux的默认fsync策略是30秒。可能丢失30秒数据。默认值为no。
no-appendfsync-on-rewrite no

# 默认值为100。aof自动重写配置，当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候，Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。
auto-aof-rewrite-percentage 100

# 设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写。
auto-aof-rewrite-min-size 64mb

# aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项，出现这种现象  redis宕机或者异常终止不会造成尾部不完整现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以。默认值为 yes。
aof-load-truncated yes
```



> LUA SCRIPTING

![image-20200625214409763](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200625214409763.png)

```shell
lua-time-limi 5000	# 一个lua脚本执行的最大时间，单位为ms。默认值为5000.
```



> REDIS CLUSTER

```shell
cluster-enabled yes		# 集群开关，默认是不开启集群模式。

# 集群配置文件的名称，每个节点都有一个集群相关的配置文件，持久化保存集群的信息。这个文件并不需要手动配置，这个配置文件由Redis生成并更新，每个Redis集群节点需要一个单独的配置文件。请确保与实例运行的系统中配置文件名称不冲突。默认配置为nodes-6379.conf。
cluster-config-file nodes-6379.conf

cluster-node-timeout 15000	# 可以配置值为15000。节点互连超时的阀值，集群节点超时毫秒数

# 可以配置值为10。在进行故障转移的时候，全部slave都会请求申请为master，但是有些slave可能与master断开连接一段时间了，导致数据过于陈旧，这样的slave不应该被提升为master。该参数就是用来判断slave节点与master断线的时间是否过长。判断方法是：比较slave断开连接的时间和(node-timeout * slave-validity-factor) + repl-ping-slave-period 如果节点超时时间为三十秒, 并且slave-validity-factor为10,假设默认的repl-ping-slave-period是10秒，即如果超过310秒slave将不会尝试进行故障转移
cluster-replica-validity-factor 10	

# 可以配置值为1。master的slave数量大于该值，slave才能迁移到其他孤立master上，如这个参数若被设为2，那么只有当一个主节点拥有2个可工作的从节点时，它的一个从节点会尝试迁移。
cluster-migration-barrier 1

# 默认情况下，集群全部的slot有节点负责，集群状态才为ok，才能提供服务。设置为no，可以在slot没有全部分配的时候提供服务。不建议打开该配置，这样会造成分区的时候，小分区的master一直在接受写请求，而造成很长时间数据不一致。
cluster-require-full-coverage yes
```



# Redis持久化

##### ==注：Redis是内存数据库==，如果不将内存中的数据持久化到磁盘中，那么一旦服务器进程退出，服务器中数据就会消失

## RDB

> 简介





> 三种RDB触发机制

###### `save` 同步命令

* 该命令会阻塞当前Redis服务器，执行save命令期间，Redis不能处理其他命令，直到RDB过程完成为止。具体流程如下：

  ![img](https://pics1.baidu.com/feed/e7cd7b899e510fb3aa8c05042b22c093d0430ca7.jpeg?token=7ed4cf784a82d04e60b8dc72cf7e3c24&s=EDBAA5565D1859C85444707E02005071)

执行完成时候如果存在老的RDB文件，就把新的替代掉旧的。我们的客户端可能都是几万或者是几十万，这种方式显然不可取。

##### **`bgsave`异步命令**

- 执行该命令时，Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。具体流程如下：

![img](https://pics5.baidu.com/feed/023b5bb5c9ea15cefb035bc8431132f53b87b21e.jpeg?token=a72f072d65d2de548d71bb459cd0bf4f&s=05AAFE168FF04C8A10FD2DEE0300E032)

* 执行bgsave命令，Redis父进程判断当前是否存在正在执行的子进程，如RDB/AOF子进程，如果存在bgsave命令直接返回。

* 父进程执行fork操作创建子进程，fork操作过程中父进程会阻塞，通过info stats命令查看latest_fork_usec选项，可以获取最近一个fork以操作的耗时，单位为微秒。

* 父进程fork完成后，bgsave命令返回“Background saving started”信息并不再阻塞父进程，可以继续响应其他命令。

* 子进程创建RDB文件，根据父进程内存生成临时快照文件，完成后对原有文件进行原子替换。执行lastsave命令可以获取最后一次生成RDB的时间，对应info统计的rdb_last_save_time选项。

* 进程发送信号给父进程以示完成，父进程更新统计信息，具体见info Persistence下的rdb_*相关选项。

##### 自动触发

- 在某些条件满足时，以 ```bgsave```方式自动触发 RDB 文件的生成，比如：
  - `save 60 10000` 在 60s 内更新 10000 条数据，触发 RDB；
  - `save 300 10` 在 300s 内更新 10 条数据，触发 RDB；
  - `save 900 1` 在 900s 内更新 1 条数据，触发 RDB；

* 相关命令
  * **stop-writes-on-bgsave-error ：**默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了
  * **rdbcompression：** 默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。
  * **rdbchecksum ：**默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。
  * **dbfilename ：**设置快照的文件名，默认是 dump.rdb
  * **dir：**设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。

**其他RDB触发机制**

* 如果从节点执行全量复制操作，主节点自动执行bgsave生成RDB文件并发送给从节点。
* 执行`flushall`命令，会触发RDB 文件的生成；
* 执行`shutdown save` 命令时，如果没有开启AOF持久化功能则会触发 RDB 文件的生成；
* 执行debug reload命令重新加载Redis时，也会自动触发save操作。



> ```save```与```bgsave```的对比

![img](https://pics5.baidu.com/feed/1c950a7b02087bf43b4490d50ac25f2a11dfcf7e.jpeg?token=22f387ba78130c6115420059481b2393&s=EF48A15796784D8816E1D9EB03007024)



> 如何恢复rdb文件

1. 只需要将rdb文件放在redis启动目录就可以，redis启动时会自动检查dump.rdb恢复其中的数据

2. 查看需要存在的位置

   ```shell
   127.0.0.1:6379> config get dir
   1) "dir"
   2) "/usr/local/bin"		# 如果在这个目录下存在dump.rdb文件，启动就会自动恢复其中的数据
   ```

   

> RDB 的优势和劣势

**优势：**

* RDB是一个紧凑压缩的二进制文件，代表Redis在某一个时间点上的数据快照。非常适合用于备份，全量复制等场景。比如每6小时执行bgsave备份，并把RDB文件拷贝到远程机器或者文件系统中（如hdfs），用于灾难恢复。
* 生成RDB文件的时候，redis主进程会fork()一个子进程来处理所有保存工作，主进程不需要进行任何磁盘IO操作。
* RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

**劣势：**

* 一般来说，RDB数据快照文件，都是每隔5分钟，或者更长时间生成一次，这个时候就得接受一旦Redis进程宕机，那么会丢失最近5分钟的数据；
* RDB每次在fork子进程来执行RDB快照数据文件生成的时候，如果数据文件特别大就会占用很大内存，可能会导致对客户端提供的服务暂停数毫秒，或者甚至数秒；
* RDB无法实现实时或者秒级持久化



## AOF

> 简介

全量备份总是耗时的，有时候我们提供一种更加高效的方式AOF，工作机制很简单，redis会将每一个收到的写命令都通过write函数追加到文件中。通俗的理解就是日志记录。



### **AOF的执行流程包括：**

开启AOF功能需要设置配置：appendonly yes,默认不开启。AOF文件通过appendfilename 配置设置，默认文件名是appendonly.aof。保存路径同RDB持久化方式一致。通过dir配置指定。AOF的工作流程操作：命令写入（append）、文件同步（sync）、文件重写（rewrite）、重启加载（load）,工作流程如下：

![img](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1593167081701&di=3a984183020acd27071f4a488d89a08f&imgtype=0&src=http%3A%2F%2Ft8.baidu.com%2Fit%2Fu%3D2689257803%2C1425492142%26fm%3D193)

* 所有的写入命令会追加到aof_buf（缓冲区）中。
* AOF缓冲区根据对应的策略向硬盘做同步操作。
* 随着AOF文件越来越大，需要定期对AOF文件进行重写，达到压缩的目的。
* 当Redis服务重启时，可以加载AOF文件进行数据恢复。了解AOF工作流程之后，下面针对每个步骤做详细介绍。





### 持久化原理

![img](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2960974798,4291149739&fm=26&gp=0.jpg)

### **2.1.2 命令写入**

  AOF命令写入的内容直接是文本协议格式。例如set hello world 这条命令，在AOF缓冲区会追加如下文本：

```
\r\n$3\r\nset\r\n$5\r\nhello\r\n$5\r\nworld\r\n
```

  介绍关于AOF的连个疑惑：

  1） AOF为什么直接采用文本协议格式？可能的理由如下：

- 文本协议具有很好的兼容性。
- 开启AOF后，所有写入命令都包含追加操作，直接采用协议格式，避免二次处理开销。
- 文本协议具有可读性，方便直接修改和处理。

  2） AOF为什么把命令追加到aof_buf中？Redis使用单线程响应命令，如果每次写AOF文件命令都直接追加到硬盘，那么性能完全取决于当前硬盘负载。县写入缓冲区aof_buf中，还有另一个好处，Redis可以提供多种缓冲区同步硬盘的策略，在性能和安全性方面做出平衡。

### **2.1.3 文件同步**

  Redis提供了多种AOF缓冲区同步文件策略，由参数appendfsync控制，不同值的含义如表所示

```shell
# 配置为always时，每次写入都要同步AOF文件，在一般的STAT硬盘上，Redis只能支持大约几百TPS写入，显然跟Redis高性能特性背道而驰，不建议配置。
appendfsync always	

# 配置为no,由于操作系统每次同步AOF文件的周期不可控，而且会极大每次同步硬盘的数据量，虽然提升了性能，但数据安全性无法保证。
appendfsync everysec

# 配置为everysec,是建议的同步策略，也是默认配置，做到兼顾性能和数据安全性，理论上只有在系统突然宕机的情况下丢失1s的数据。（严格来说最多丢失1s数据是不准确）
appendfsync no
```

  系统调用writ和fsync说明：

- write操作会处罚延迟写（delayed write）机制，Linux在内核提供页缓冲区用来提高硬盘IO性能。write操作在写入系统缓冲区后直接返回。同步硬盘操作依赖于系统调度机制，列如：缓冲区页空间写满或达到特定时间周期。同步文件之前，如果此时系统故障宕机，缓冲区内数据将丢失。
- fsync针对单个文件操作（比如AOF文件），做强制硬盘同步，fsync将阻塞知道写入硬盘完成后返回，保证了数据持久化。

  除了write、fsync、Linx还提供了sync、fdatasync操作，具体API说明参见：http://linux.die.net/man/2/write



### **2.1.4 重写机制**

​		随着命令不断写入AOF，文件会越来越大，为了解决这个问题，Redis引入了AOF重写机制压缩文件体积。AOF文件重写是把Redis进程内的数据转化为写命令同步到新AOF文件的过程。

  重写后的AOF文件为什么可以变小？有如下原因：

  1） 进程内已经超时的数据不再写文件。

  2）旧的AOF文件含有无效命令，如del key1、 hdel key2、srem keys、set a 111、set a 222等。重写使用进程内数据直接生成，这样新的AOF文件只保留最终数据的写入命令。

  3) 多条写命令可以合并为一个，如lpush list a、lpush list b、 lpush list c 可以转化为：lpush list a b c。为了防止但挑明了过大造成客户端缓冲区溢出，对于list、set、hash、zset等类型曹组，以64个元素为界拆分为多条。 

  AOF重写降低了文件占用空间，除此之外，另一个目的是：更小的AOF文件可以更快地被Redis加载。

  AOF重写过程可以手动触发和自动触发：

- 手动触发：直接调用bgrewriteaof命令
- 自动触发：更具auto-aof-rewrite-min-size和auto-aof-rewrite-percentage参数确定自动触发时机
  - auto-aof-rewrite-min-size:表示运行AOF重写时文件最小体积，默认为64MB
  - auto-aof-rewrite-percentage:代表当前AOF文件空间（aof_current_size）和上一次重写后AOF文件空间（aof_base_size）的值

  自动触发时机=aof_current_size>auto-aof-rewrite-min-size && (aof_current_size-aof_base_size) / aof_base_size >= auto-aof-rewrite-percentage

  其中aof_current_size和aof_base_size可以再info Persistence统计信息中查看。

  当触发AOF重写时，内部做了那些事？下面结合图介绍它的运行流程：

![img](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1793823422,850624599&fm=26&gp=0.jpg)

流程说明：

* 执行AOF重写请求。

  如果当前进程正在执行AOF重写，请求不执行并返回如下响应:

```
ERR Background append only file rewriting already in progress
```

   如果当前进程正在执行bgsave操作，重写命令延迟到bgsave完成后再执行，返回如下响应：

```
Background append only file rewriting scheduled
```

* 父进程执行fork创建子进程，开销等同于bgsave过程。

* 主进程fork操作完成后，继续响应其他命令。所有修改命令依然写入AOF缓冲区并更具appendfsync策略同步到硬盘，保证原有AOF机制正确性。

* 由于fork操作运用写时复制技术，子进程只能共享fork操作时的内存数据。由于父进程依然响应命令，Redis使用"AOF重写缓冲区"保存这部分新数据，防止新AOF文件生成期间丢失这部分数据。

* 子进程根据内存快照，按照命令合并规则写入到新的AOF文件。每次批量写入硬盘数据量由配置aof-rewrite-incremental-fsync控制，默认为32MB，防止单次刷盘数据过多造成硬盘阻塞。

* 新AOF文件写入完成后，子进程发送信号给父进程，父进程更新统计信息，具体见info persistence下的aof_*相关统计。

* 父进程把AOF重写缓冲区的数据写入到新的AOF文件。

* 使用新AOF文件替换老文件，完成AOF重写。

### 2.1.5  重启加载

  AOF和RDB文件都可以用于服务器重启时的数据恢复。如图所示，表示Redis持久化文件加载流程：

  流程说明：

  1） AOF持久化开启且存在AOF文件时，优先加载AOF文件，打印如下日志：

```
DB loaded from append only file: 5.841 seconds
```

  2） AOF关闭或者AOF文件不存在时，加载RDB文件，打印如下日志：

```
DB loaded from disk:5.586 seconds
```

  3） 加载AOF/RDB文件城后，Redis启动成功。

  4） AOF/RDB文件存在错误时，Redis启动失败并打印错误信息；AOF/RDB文件存在错误时并启动失败时，可以通过`redis-check-aof --fix appendonly.aof`来修复aof文件



### AOF优点与缺点

**优点：**

* 使用 AOF Redis 会更具有可持久性(durable)：你可以有很多不同的 fsync 策略：没有 fsync，每秒 fsync，每次请求时 fsync。使用默认的每秒 fsync 策略，写性能也仍然很不错(fsync 是由后台线程完成的，主线程继续努力地执行写请求)，即便你也就仅仅只损失一秒钟的写数据。
* AOF 日志是一个追加文件，所以不需要定位，在断电时也没有损坏问题。即使由于某种原因文件末尾是一个写到一半的命令(磁盘满或者其他原因),redis-check-aof 工具也可以很轻易的修复。
* AOF 文件里面包含一个接一个的操作，以易于理解和解析的格式存储。你也可以轻易地导出一个 AOF 文件。例如，即使你不小心错误地使用 FLUSHALL 命令清空一切，如果此时并没有执行重写，你仍然可以保存你的数据集，你只要停止服务器，删除最后一条命令，然后重启 Redis 就可以。

**缺点：**

* aof的文件会比rdb文件大，恢复起来相对比较慢。
* AOF 可能比 RDB 慢，这取决于准确的 fsync 策略。通常 fsync 设置为每秒一次的话性能仍然很高，如果关闭 fsync，即使在很高的负载下也和 RDB 一样的快。不过，即使在很大的写负载情况下，RDB 还是能提供能好的最大延迟保证。



![img](https://pics5.baidu.com/feed/8326cffc1e178a82c532308ef2117b8ba977e8ae.jpeg?token=fea28817e45f0e091b5be3854d856fbb&s=BD48B55F1C784C095E61DCEB0300D036)





# 发布订阅

Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。

![img](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=3797676250,1980408338&fm=11&gp=0.jpg)

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub1.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub2.png)



```shell
PSUBSCRIBE pattern [pattern ...]	# 订阅一个或多个符合给定模式的频道。

PUBSUB subcommand [argument [argument ...]]		# 查看订阅与发布系统状态。

PUBLISH channel message		# 将信息发送到指定的频道。

PUNSUBSCRIBE [pattern [pattern ...]]	# 退订所有给定模式的频道。

SUBSCRIBE channel [channel ...]		# 订阅给定的一个或多个频道的信息。

UNSUBSCRIBE [channel [channel ...]]		# 指退订给定的频道。
```



# 主从复制

### 概述

在现有企业中80%公司大部分使用的是redis单机服务，在实际的场景当中单一节点的redis容易面临风险。

### 面临问题

1. 从结构上，单个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大。
2. 从容量上，单个Redis服务器内存容量有限，就算一台Redis服务器内存容量为256G，也不能将所有内存用作Redis存储内存，一般来说，单台Redis最大使用内存不应该超过20G



### 解决办法

要实现分布式数据库的更大的存储容量和承受高并发访问量，我们会将原来集中式数据库的数据分别存储到其他多个网络节点上。

Redis 为了解决这个单一节点的问题，也会把数据复制多个副本部署到其他节点上进行复制，实现 Redis的高可用，实现对数据的冗余备份，从而保证数据和服务的高可用。




### 什么是主从复制

**主从复制**，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点(master)，后者称为从节点(slave)，==数据的复制是单向的，只能由主节点到从节点==。

==默认情况下，每台Redis服务器都是主节点；==且一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。



### 主从复制的作用

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。
4. 读写分离：可以用于实现读写分离，主库写、从库读，读写分离不仅可以提高服务器的负载能力，同时可根据需求的变化，改变从库的数量；
5. 高可用基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是Redis高可用的基础。



![img](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3014414378,1014834491&fm=26&gp=0.jpg)



## 环境配置

### 查看主从复制配置信息

```shell
127.0.0.1:6379> INFO replication
# Replication
role:master
connected_slaves:0
master_replid:cdfa70ea897cd166499e4ef46ae8597342ea7273
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

复制3个配置文件，然后修改对应的信息

1. 端口号
2. pid文件名
3. log文件名
4. dump.rdb文件名

然后分别用3个配置文件启动3个redis-server

![image-20200630140910238](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200630140910238.png)



## 一主二从

==默认情况下，每台Redis服务器都是主机。==只需配从机

```shell
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379		# slaveof host port
OK
127.0.0.1:6380> INFO replication	# 在从机中查看
# Replication
role:slave						# 当前角色
master_host:127.0.0.1			# 主机的相关信息
master_port:6379
master_link_status:up
master_last_io_seconds_ago:4
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:7cc4cfb1ec7dddb3206d9b9d2a34b82d4e3d31c5
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14

# 在主机中查看
127.0.0.1:6379> INFO replication
# Replication
role:master
connected_slaves:1			# 多了从机的信息
slave0:ip=127.0.0.1,port=6380,state=online,offset=336,lag=1
master_replid:7cc4cfb1ec7dddb3206d9b9d2a34b82d4e3d31c5
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:336
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:336
```

**真实的主从配置是在配置文件中配置的（永久生效）；使用命令配置，是暂时的**



### 操作

主机可读可写，从机只能读；主机中的所有信息与数据都会自动复制到从机中

```shell
# 主机写
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> set k1 v1
OK

# 从机只能读，不能写
127.0.0.1:6380> get k1
"v1"
127.0.0.1:6380> set k2 v2
(error) READONLY You can't write against a read only replica.
```



### 测试：

1. 主机断开，从机依旧连接主机，此时主机重新连接并且写入k2，从机依旧可以获取到主机写的数据。

```shell
# 主机
127.0.0.1:6379> shutdown
not connected> exit
[root@iz2zeaw7yt2qgbmer62xfdz bin]# redis-server myconf/redis79.conf 
[root@iz2zeaw7yt2qgbmer62xfdz bin]# redis-cli -p 6379
127.0.0.1:6379> set k2 v2
OK

# 从机
127.0.0.1:6380> get k2		# 一开始并不存在k2
(nil)
127.0.0.1:6380> get k2		# 主机恢复后并写入k2后
"v2"
```

2. 如果使用命令配置主从，那么从机重启后会自动成为主机（没有属于它的从机）；只有变为从机，立马就会从主机中获取值。

* **复制原理**

  slave 启动成功并连接到 master 后会发送一个sync同步命令

  master 接到命令就会启动后台的存盘进程，同时收集所有接收到的用于修改数据的命令，在后台进程执行完毕之后，master 将传送整个数据文件到 slave，并完成一次完全同步。

  * ==全量复制：==slave 服务在接收到数据库文件数据后，将其存盘并加载到内存中；但是只要重新连接master，就会自动执行一次全量复制
  * ==增量复制：==master 继续将新的所有收集到的修改命令一此传给slave，完成同步



### 层层链路

![image-20200630180029858](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200630180029858.png)



**如果主机断开了，此时能不能找一个从机变为主机呢？手动**

如果主机断开连接，可以使用`slaveof on one`让从机变成主机，并且让其他节点手动配置为主机的从机，如果原主机修复了（重连），那就只能手动配置为新主机的从机。



## 哨兵模式

**主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费事费力，还会造成一段时间内服务不可用。**这不是一种推荐的方式，更多时候，我们优先考虑**哨兵模式**。Redis从2.8开始正式提供了Sentinel(哨兵)架构来解决这个问题。



## 一、哨兵模式概述

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。**

![img](https://upload-images.jianshu.io/upload_images/11320039-57a77ca2757d0924.png?imageMogr2/auto-orient/strip|imageView2/2/w/507/format/webp)

这里的哨兵有两个作用

- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。
- 当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式。



![img](https:////upload-images.jianshu.io/upload_images/11320039-3f40b17c0412116c.png?imageMogr2/auto-orient/strip|imageView2/2/w/747/format/webp)

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failove[故障转移]操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。这样对于客户端而言，一切都是透明的。



### Sentinel（哨兵）进程的作用

​	**监控(Monitoring):**  哨兵(sentinel) 会不断地检查你的Master和Slave是否运作正常。
​	**提醒(Notification)：**当被监控的某个Redis节点出现问题时, 哨兵(sentinel) 可以通过 API 向管理员或者其他应用程序发送通知。
​	**自动故障迁移(Automatic failover)：**当一个Master不能正常工作时，哨兵(sentinel) 会开始一次自动故障迁移操作，它会将失效Master的其中一个Slave升级为新的Master, 并让失效Master的其他Slave改为复制新的Master；当客户端试图连接失效的Master时，集群也会向客户端返回新Master的地址，使得集群可以使用现在的Master替换失效Master。Master和Slave服务器切换后，Master的redis.conf、Slave的redis.conf和sentinel.conf的配置文件的内容都会发生相应的改变，即，Master主服务器的redis.conf配置文件中会多一行slaveof的配置，sentinel.conf的监控目标会随之调换。



### Sentinel（哨兵）进程的工作方式

1. 每个Sentinel（哨兵）进程以每秒钟一次的频率向整个集群中的Master主服务器，Slave从服务器以及其他Sentinel（哨兵）进程发送一个 PING 命令。
2. 如果一个实例（instance）距离最后一次有效回复 PING 命令的时间超过 down-after-milliseconds 选项所指定的值， 则这个实例会被 Sentinel（哨兵）进程标记为主观下线（SDOWN）
3. 如果一个Master主服务器被标记为主观下线（SDOWN），则正在监视这个Master主服务器的所有 Sentinel（哨兵）进程要以每秒一次的频率确认Master主服务器的确进入了主观下线状态
4. 当有足够数量的 Sentinel（哨兵）进程（大于等于配置文件指定的值）在指定的时间范围内确认Master主服务器进入了主观下线状态（SDOWN）， 则Master主服务器会被标记为客观下线（ODOWN）
5. 在一般情况下， 每个 Sentinel（哨兵）进程会以每 10 秒一次的频率向集群中的所有Master主服务器、Slave从服务器发送 INFO 命令。
6. 当Master主服务器被 Sentinel（哨兵）进程标记为客观下线（ODOWN）时，Sentinel（哨兵）进程向下线的 Master主服务器的所有 Slave从服务器发送 INFO 命令的频率会从 10 秒一次改为每秒一次。
7. 若没有足够数量的 Sentinel（哨兵）进程同意 Master主服务器下线， Master主服务器的客观下线状态就会被移除。若 Master主服务器重新向 Sentinel（哨兵）进程发送 PING 命令返回有效回复，Master主服务器的主观下线状态就会被移除。
   



### 测试：

1. 配置哨兵配置文件sentinel.conf

   ```shell
   # sentinel monitor 被监控的redis名称 host port 
   sentinel monitor myredis 127.0.0.1 6379 1
   ```

2. 启动哨兵

   ```shell
   [root@iz2zeaw7yt2qgbmer62xfdz bin]# redis-sentinel myconf/sentinel.conf 
   1661:X 30 Jun 2020 18:48:40.630 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
   1661:X 30 Jun 2020 18:48:40.630 # Redis version=6.0.5, bits=64, commit=00000000, modified=0, pid=1661, just started
   1661:X 30 Jun 2020 18:48:40.630 # Configuration loaded
                   _._                                                  
              _.-``__ ''-._                                             
         _.-``    `.  `_.  ''-._           Redis 6.0.5 (00000000/0) 64 bit
     .-`` .-```.  ```\/    _.,_ ''-._                                   
    (    '      ,       .-`  | `,    )     Running in sentinel mode
    |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
    |    `-._   `._    /     _.-'    |     PID: 1661
     `-._    `-._  `-./  _.-'    _.-'                                   
    |`-._`-._    `-.__.-'    _.-'_.-'|                                  
    |    `-._`-._        _.-'_.-'    |           http://redis.io        
     `-._    `-._`-.__.-'_.-'    _.-'                                   
    |`-._`-._    `-.__.-'    _.-'_.-'|                                  
    |    `-._`-._        _.-'_.-'    |                                  
     `-._    `-._`-.__.-'_.-'    _.-'                                   
         `-._    `-.__.-'    _.-'                                       
             `-._        _.-'                                           
                 `-.__.-'                                               
   
   1661:X 30 Jun 2020 18:48:40.631 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
   1661:X 30 Jun 2020 18:48:40.633 # Sentinel ID is e453a16b726e7e9ede580947a8fb2641bc976ba3
   1661:X 30 Jun 2020 18:48:40.633 # +monitor master myredis 127.0.0.1 6379 quorum 1
   1661:X 30 Jun 2020 18:48:40.634 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
   1661:X 30 Jun 2020 18:48:40.636 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
   ```

3. 当主机断开连接后，哨兵检测主机的运行状态，当得知主机断连后，在一定时间后就会投票选出下一个主机

   ```shell
   127.0.0.1:6379> SHUTDOWN
   not connected> exit
   ```

   ![image-20200630185445376](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200630185445376.png)

   

4. 当原主机重连之后，哨兵会将原主机自动配置为新主机的从机

   ![image-20200630190330388](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200630190330388.png)

   ![image-20200630190418248](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200630190418248.png)



## 哨兵模式的优缺点

**优点：**

1. 哨兵集群模式是基于主从模式的，所有主从的优点，哨兵模式同样具有。
2. 主从可以切换，故障可以转移，系统可用性更好。
3. 哨兵模式是主从模式的升级，系统更健壮，可用性更高。

**缺点：**

1. Redis较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。
2. 配置复杂



## 哨兵模式的配置

```shell
 
 
bind 0.0.0.0
 
# 后台运行
daemonize yes
 
# 默认yes
protected-mode no
 
# 哨兵的端口，客户端通过这个端口来发现redis
port 26379
 
# 哨兵自己的IP，手动设定也可自动发现，用于与其他哨兵通信
# sentinel announce-ip
 
# 临时文件夹
dir /tmp
 
# 日志
logfile "/usr/local/redis/logs/sentinel-26379.log"
 
# sentinel监控的master的名字叫做mymaster,初始地址为 172.16.5.1 6379,2代表两个及以上哨兵认定为死亡，才认为是真的死亡
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 172.16.5.1 6379 2
 
 # 当Redis实例中开启了requirepass foobared 授权密码，那么所有连接Redis实例的客户端都需要提供密码
 # 设置哨兵连接主从的密码，注意必须为主从设置一样的验证密码
 # sentinel auth-pass <master-name> <password>
 sentinel auth-pass mymaster 0123passw0rd
 
# 发送心跳PING来确认master是否存活 默认30s
# 如果master在“一定时间范围”内不回应PONG 或者是回复了一个错误消息，那么这个sentinel会主观地(单方面地)认为这个master已经不可用了
sentinel down-after-milliseconds mymaster 1000
 
# failover-timeout 可以用在以下这些方面： 
# 1.同一个sentinel对同一个master两次failover之间的间隔时间。
# 2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
# 3.当想要取消一个正在进行的failover所需要的时间。  
# 4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了。
sentinel failover-timeout mymaster 3000
 
# 指定了在执行故障转移时，最多可以有多少个slave同时对新的master进行同步，在slave较多的情况下这个数字越小，同步的时间越长，完成故障转移所需的时间就越长;但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
sentinel parallel-syncs mymaster 1

# sentinel的notification-script和reconfig-script是用来配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。对于脚本的运行结果有以下规则：
# 若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
# 若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
# 如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
# 一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。

sentinel notification-script <master-name> <script-path> 
# 通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一个是事件的类型，一个是事件的描述。如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
sentinel notification-script mymaster /var/redis/notify.sh

sentinel client-reconfig-script <master-name> <script-path>
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”, <role>是“leader”或者“observer”中的一个。 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的。这个脚本应该是通用的，能被多次调用，不是针对性的。
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
```





# 缓存穿透和雪崩

## 缓存穿透

> 概念

缓存穿透是指缓存和数据库中都没有的数据，而用户不断发起请求，我们数据库的 id 都是1开始自增上去的，如发起为id值为 -1 的数据或 id 为特别大不存在的数据。这时的用户很可能是攻击者，攻击会导致数据库压力过大，严重会击垮数据库。



> 解决方案

1. **布隆过滤器**

![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701140534023.png)



2. **缓存空对象**

当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护了后端数据源；

![img](https://wx1.sinaimg.cn/large/007FyU7Tgy1g1uzoeoa6dj308i0a174b.jpg)

但是这种方法会存在两个问题：

1. 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键；
2. 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。



3. **接口层增加校验，如用户鉴权校验，id做基础校验，id<=0的直接拦截；**



## 缓存击穿

> 概念

缓存穿透是指一个Key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个Key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个完好无损的桶上凿开了一个洞。



> 解决方案

1. **设置热点数据永远不过期。**

2. **加互斥锁：**使用分布式锁，宝恒对于每个key同时只有一个线程去查询后端服务，其他线程没有获取分布式锁的权限，因此只需要等待即可。这种方式将高并发的压力转移到了分布式锁上，因此对分布式锁的考验很大。

   **互斥锁参考代码如下：**

![img](https://img-blog.csdn.net/20180919143214879?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tvbmd0aWFvNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

说明：

1）缓存中有数据，直接走上述代码13行后就返回结果了

2）缓存中没有数据，第1个进入的线程，获取锁并从数据库去取数据，没释放锁之前，其他并行进入的线程会等待100ms，再重新去缓存取数据。这样就防止都去数据库重复取数据，重复往缓存中更新数据情况出现。

  3）当然这是简化处理，理论上如果能根据key值加锁就更好了，就是线程A从数据库取key1的数据并不妨碍线程B取key2的数据，上面代码明显做不到这点。



## 缓存雪崩

> 概念

缓存雪崩是指缓存中数据大面积失效，而查询数据量巨大，引起数据库压力过大甚至宕机。和缓存击穿不同的是，缓存击穿指并发查同一条数据，缓存雪崩是不同数据都过期了，很多数据都查不到从而查数据库。

**例如：**如果所有首页的Key失效时间都是12小时，中午12点刷新的，我零点有个秒杀活动大量用户涌入，假设当时每秒 6000 个请求，本来缓存在可以扛住每秒 5000 个请求，但是缓存当时所有的Key都失效了。此时 1 秒 6000 个请求全部落数据库，数据库必然扛不住，它会报一下警，真实情况可能DBA都没反应过来就直接挂了。此时，如果没用什么特别的方案来处理这个故障，DBA 很着急，重启数据库，但是数据库立马又被新的流量给打死了。这就是我理解的缓存雪崩。

![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701145848629.png)



> 解决方案

1. 缓存数据的过期时间设置随机，防止同一时间大量数据过期现象发生。
2. 如果缓存数据库是分布式部署，将热点数据均匀分布在不同的缓存数据库中。
3. 设置热点数据永远不过期。