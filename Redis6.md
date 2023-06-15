# 第一章 Redis简介

## 一，NoSQL数据库

1. 什么是NoSQL数据库

   NoSQL（Not Only SQL），”即不仅仅是SQL“，泛指非关系型的数据库。

   NoSQL不依赖业务逻辑方式储存，而以简单的key-value模式存储，因此大大的增加了数据库的扩展能力

   - 不遵循SQL标准
   - 不支持ACID
   - 远超于SQL的性能

2. NoSQL适用场景

   - 对数据高并发的读写
   - 海量数据的读写
   - 对数据高可扩展性的

3. NoSQL不适用场景

   - 需要事务支持
   - 基于SQL的结构化查询存储，处理复杂的关系，需要即席查询

## 二，Redis概述和安装

1. 概述
   - Redis是一个==开源==的key-value存储系统
   - 它支持存储的value类型相对更多，包括string、list、set、zset和hash类型
   - 这些数据类型都支持push/pop、add/remove及取交集和差集及丰富的操作，而且这些操作都是==原子性==的
   - 在此基础上，Redis支持各种不同方式的排序
   - 数据都是缓存在内存中
   - Redis会==周期性==的把更新的==数据写入硬盘==或者把修改操作写入追加的记录文件
   - 并且在此基础上实现了master-slave（主从）同步
   
2. 应用场景
   - 高频次，热门访问的数据，降低数据库IO
   - 分布式架构，做session共享
   
3. 安装

   - 上传下载好的文件到服务器

   - 解压：tar -zxvf redis-6.2.6.tar.gz

   - 安装C语言的编译环境

     yum install centos-release-scl scl-utils-build

     yum install -y devtoolset-8-toolchain

     scl enable devtoolset-8 bash

   - 进入解压好的文件夹：cd redis-6.2.6/

   - 执行make命令

   - 执行make install

   - 默认安装目录：/usr/local/bin

4. 查看默认安装目录

   - redis-benchmark：性能测试工具
   - redis-check-aof：修复有问题的AOF文件
   - redis-check-rdb：修复有问题的RDB文件
   - redis-sentinel：Redis集群使用
   - redis-server：Redis服务器启动命令
   - redis-cli：客户端，操作入口

5. 后台启动

   - 拷贝配置文件：cp redis.conf /etc/redis.conf
   - 修改redis.conf文件里面的daemonize no改成yes
   - cd /usr/local/bin
   - 启动：redis-server /etc/redis.conf

6. 关闭Redis

   - redis-cli shutdown
   - 杀死进程

## 三，Redis相关知识介绍

1. 默认16个数据库，类似数组下标从0开始，初始默认使用0号库
2. 使用命令select dbid 来切换数据库
3. 统一密码管理，所有库的密码相同
4. dbsize查看当前数据库的key的数量
5. flushdb清空当前库
6. flushall通杀全部数据库

==Redis是单线程+多路IO复用==

# 第二章 常用的五大数据类型

## 一，Redis键（key）

1. keys *：查看当前库所有的key（匹配：key * 1）

2. exists key：判断某个key是否存在

3. type key：查看你的key是什么类型

4. del key：删除指定key

5. unlink key：根据value选择非阻塞删除

   仅将keys从keyspace元数据中删除，真正删除会在后续异步操作

6. expire key 10：为给定的key设置过期时间，单位秒

7. ttl key：查看还有多少秒过期，-1表示永不过期，-2表示已过期

## 二，字符串（String）

1. 简介

   String是Redis最基本的类型，一个key对应一个value

   String类型是二进制安全的，意味着Redis的string可以包含任何数据，比如图片或者序列化对象

   一个Redis中字符串value最多可以是512M

2. 常用命令

   - set key value添加键值对
   - get key：查看key对应的值
   - append key value：将value追加到key的原本值的末尾
   - strlen key：获取key对应值的长度
   - setnx key value：只有在key不存在的时候，设置key的值
   - incr key：将key中存储的数字值加1，只能对数字值操作，如果为空新增值为1
   - decr key：将key中存储的数字值减1，只能对数字值操作，如果为空新增值为-1
   - incrby/decrby key 步长：将key中存储的数字值增减，自定义步长
   - mset key1 value1 key2 value2 ...：同时设置一个或多个key-value
   - mget key1 key2 key3 ...：同时获取一个或多个key对应的value
   - msetnx key1 value1 key2 value2 ...：同时设置一个或多个key-value，且当所有给定key都不存在
   - getrange key 起始位置 结束位置：获取值的范围，类似于java中的substring，前包后包
   - setrange key 起始位置 value：用value覆盖key中所存储的字符串值，从指定的起始位置开始
   - setex key 过期时间 value：设置键值对的同时设置过期时间，单位秒
   - getset key value：以新换旧，设置新值，同时获取旧值

3. 原子性

   原子操作是指不会被线程调度机制打断的操作

   这种操作一旦开始，就会一直运行到结束，中间不会有任何context switch（切换到另外一个线程）

   - 在单线程中，能够在单条指令中完成的操作都可以认为是“原子操作”，因为中断只能发生于指令之间
   - 在多线程中，不能被其他进程（线程）打断的操作就叫原子操作

4. Redis中string的数据结构

   **String的数据结构为简单动态字符串（Simple Dynamic String，SDS）**。是可以修改的字符串，内部结构实现上类似于java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配

## 三，列表（List）

1. 简介

   单键多值

   Redis列表是简单的字符串列表，按照插入顺序排序，可以添加一个元素到列表的头部（左边）或者尾部（右边）

   它的底层是一个双向列表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差

2. 常用命令

   - lpush/rpush key value1 value2 value3 ...：从左边/右边插入一个或者多个值
   - lpop/rpop key：从左边/右边吐出一个值。值在键在，值光键亡
   - rpoplpush key1 key2：从key1列表右边吐出一个值，插到key2左边
   - lrange key start stop：按照索引下标获得元素
   - lrange key 0 -1：0左边第一个，-1右边第一个，获取全部
   - lindex key index：按照索引获得元素（从左到右）
   - llen key：获取长度
   - linsert key before value newValue：在value前面插入newValue
   - lrem key n value：从左边删除n个value（从左到右）
   - lset key index value：将列表key下表为index的值替换成value

3. Redis中的list数据结构

   - **List的数据结构为快速链表quickList**

   在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表。它将所有的元素紧挨着一起存储，分配的是一块连续的内存

   当数据量比较多的时候才会改成quicklist，因为普通的链表需要附加指针空间太大，会比较浪费空间，比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next。

   Redis将链表和ziplist结合起来组成了quicklist，也就是将多个ziplist使用双向指针串起来使用，这样就既满足了快速的插入删除性能，又不会出现太大的空间冗余

## 四，集合（Set）

1. 简介

   Redis的set对外提供功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，可以使用set，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的

   Redis的set是string类型的无序集合。它的底层其实是一个value为null的hash表，所以添加，删除，查找的复杂度是O(1)。

2. 常用命令

   - sadd key value1 value2 ...：将一个或多个元素加入到集合key中，已经存在的元素将被忽略
   - smembers key：取出key中的集合的所有值
   - sismember key value：判断集合key中是否含有该value，有1，没有0
   - scard key：返回该集合的元素个数
   - srem key value1 value2 ...：删除集合中的某个元素
   - spop key：随机从该集合中吐出一个值
   - srandmember key n：随机从该集合中取出n个值，不会从集合中删除
   - smove source destination value：把value从source中移动到destination中
   - sinter key1 key2：返回两个集合的交集元素
   - sunion key1 key2：返回两个集合的并集元素
   - sdiff key1 key2：返回两个集合的差集元素

3. Redis中的set数据结构

   - **Set数据结构是dict字典，字典是用哈希表实现的**

   java中HashSet的内部实现使用的是HashMap，只不过所有的value都指向同一个对象。Redis的set结构也一样，它的内部也是使用hash结构，所有的value都指向同一个内部值

## 五，哈希（Hash）

1. 简介

   Redis的hash是一个键值对集合，是一个string类型的field和value的映射表，hash特别适合用于存储对象，类似于java的Map<String, Object\>

2. 常用命令

   - hset key field value：给key集合中的field键赋值value
   - hget key field：从key集合中取出field的值
   - hmset key field1 value1 field2 value2：批量给key集合设置hash的值
   - hexists key field：查看key中的field字段是否存在
   - hkeys key：列出key集合中的所有field
   - hvals key：列出key集合中的左右value
   - hincrby key field increment：为key哈希表中的域field的值加上增量increment
   - hsetnx key field value：将哈希表key中的域field的值设置为value，当且仅当域field不存在

3. Redis中的hash数据结构

   Hash类型对应的数据结构是两种，ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用hashtable

## 六，有序集合（Zset）

1. 简介

   Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。不同之处是有序集合的每一个成员都关联了一个评分（score），这个评分被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复的

   因为元素是有序的，所以你也可以很快的根据评分或者次序来获取一个范围的元素

   访问有序集合中间的元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表

2. 常用命令

   - zadd key score1 value1 score2 value2 ...：将一个或者多个member元素及其score值加入到有序集合key中
   - zrange key start stop [withscores]：返回有序集合key中，下标在start和stop之间的元素，带withscores可以让分数一起返回到结果集
   - zrangebyscore key ==min max== [withscores] [limit offset count]：返回有序集合key中，所有score值介于min和max之间（包括min和max）的成员，按照score值**从小到大**排列
   - z==rev==rangebyscore key ==max min== [withscores] [limit offset count]：返回有序集合key中，所有score值介于min和max之间（包括min和max）的成员，按照score值**从大到小**排列
   - zincrby key increment value：为元素的score加上增量increment
   - zrem key value：删除key集合下，指定值value的元素
   - zcount key min max：统计该集合，分数区间内的元素个数
   - zrank key value：返回value在集合key中的排名，从0开始

3. Redis中的zset数据结构

   zset（SortdSet）是Redis提供的一个非常特别的数据结构，一方面它等价于java的数据结构Map<String, Double\>，可以给每一个元素value赋予一个权重score，另外一方面它由类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表

   zset的底层使用了两个数据结构

   - hash，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值
   - 跳跃表，跳跃表的目的在于给元素value排序，根据score的范围来获取元素列表

# 第三章 配置文件详解

1. bind 127.0.0.1 -::1

   限定只能本地连接

2. protected-mode yes

   保护模式

3. port 6379

   端口号，默认是6379

4. tcp-backlog 511

   设置tcp的backlog，backlog是一个连接队列，backlog队列和=未完成三次握手队列+已经完成三次握手队列

   在高并发环境下需要一个高backlog值来避免慢客户端的连接问题

   注意：Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

5. timeout 0

   超时时间。0永不超时

6. tcp-keepalive 300

   检测心跳

7. daemonize yes

   后台启动

8. pidfile /var/run/redis_6379.pid

   存放pid文件的位置，每个实例会产生一个不同的pid文件

9. loglevel notice

   日志级别

10. logfile ""

    日志的输出路径

11. databases 16

    默认16个数据库

12. \#requirepass foobared

    设置密码

    config get requirepass

    config set requirepass "wgx"

    auth wgx

13. maxclients

    设置Redis同时可以与多少个客户端进行连接

    默认情况下为10000个客户端

14. maxmemory-policy

    ![](F:\截图\屏幕截图 2022-03-05 205801.png)

15. maxmemory-samples

    设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算，所以你可以设置样本大小，Redis会默认检查这么多个key并选择其中LRU的那个

    一般设置3到7的数字，数值越小样本越不精确，但性能消耗越小

# 第四章 发布和订阅

## 一，什么是发布和订阅

Redis发布订阅（pub/sub）是中消息通信模式，发布者（pub）发送消息，订阅者（sub）接受消息

Redis客户端可以订阅任意数量的频道

## 二，发布和订阅的命令行实现

1. 打开一个客户端订阅channel1

   subscribe channel1

   ![](F:\截图\屏幕截图 2022-03-08 130019.png)

2. 打开另外一个客户端，给channel1发布消息hello

   publish channel1 hello

   ![](F:\截图\屏幕截图 2022-03-08 130237.png)

   返回1是订阅者的数量

3. 打开第一个客户端可以看到发送的消息

   ![](F:\截图\屏幕截图 2022-03-08 130334.png)

# 第五章 Redis6的新数据类型

## 一，Bitmaps

1. 简介
   - Bitmaps本身不是一种数据类型，实际上它就是字符串（key-value），但是它可以对字符串的位进行操作
   - Bitmaps单独提供了一套命令，所以在Redis中使用Bitmaps和使用字符串的方法不太相同。可以把Bitmaps看成一个以位为单位的数组，数组的每个单元只能存储0和1，数组的下标在Bitmaps中叫做偏移量
2. 命令
   - setbit key offset value：设置Bitmaps中某个偏移量的值（0或1）
   - getbit key offset：获取key中offset位的值
   - bitcount key [start end]：统计字符串从start字节到end字节比特值为1的数量
   - bitop and(or/not/xor) destkey [key...]：bitop是一个复合操作，它可以做多个Bitmaps的and（交集）、or（并集）、not（非）、xor（异或）操作并将结果保存在destkey中

## 二，HyperLogLog（HLL）

1. 简介

   在工作中，我们经常会遇到与统计相关的功能需求，比如统计网站PV（Page View页面访问量），可以使用Redis的incr、incrby实现

   但是像UV（Unique Visitor独立访问者）、独立IP数、搜索记录等需要去重和计数的问题。这种求集合中不重复元素个数的问题称为基数问题

   > 什么是基数：比如数据集{1, 3, 5, 7, 5, 7, 8}那么这个数据集的基数集为{1, 3, 5, 7, 8}，基数（不重复元素）为5。基数估计就是在误差可接受范围内，快速计算基数

   HyperLogLog是用来做基数统计的算法，HyperLogLog的优点是在输入元素的数量或者体积非常非常大的时候，计算基数所需的空间总是固定的、并且是很小的

2. 命令

   - pfadd key element [element...]：添加指定元素到HyperLoglog中

   - pfcount key [key]：计算HLL的近似基数，可以计算多个，比如计算一周的UV，可以使用7天的UV合并计算即可

   - pfmerge destkey sourcekey [sourcekey...]：将一个或多个HLL合并后的结果存储在另一个HLL中，比如每月活跃用户可以使用每天的活跃用户来合并计算得到

## 三，Geographic（GEO）

1. 简介

   Redis3.2中增加了对GEO类型的支持。GEO（Geographic），地理位置的缩写，该类型就是元素的二维坐标，在地图上就是经纬度、Redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作

2. 命令

   - geoadd key longitude latitude member：添加地理位置（经度，纬度，名称）
   - geopos key member：获取指定地区的坐标值
   - geodist key member1 member2 [m|km|ft|mi]：获取两个位置之间的直线距离
   - georadius key longitude latitude radius m|km|ft|mi：以给定的经纬度为中心找出某一半径内的元素

# 第六章 Jedis

1. 创建maven工程，引入依赖

   ```xml
   <dependency>
       <groupId>redis.clients</groupId>
       <artifactId>jedis</artifactId>
       <version>3.2.0</version>
   </dependency>
   ```

2. 创建测试类

   ```java
   public class JedisDemo {
       public static void main(String[] args) {
           Jedis jedis = new Jedis("47.100.85.66", 6379);
           String ping = jedis.ping();
           System.out.println(ping);//PONG
       }
   }
   ```

3. ```java
   //操作key
   @Test
   public void test() {
       //创建Jedis对象
       Jedis jedis = new Jedis("47.100.85.66", 6379);
       Set<String> keys = jedis.keys("*");//获取所有的keys
       for (String key : keys) {
           System.out.println(key);
       }
       jedis.set("k1", "v1");//设置key和value
       System.out.println(jedis.get("k1"));//获取key对应的value
       System.out.println(jedis.exists("k1"));//判断是否存在key
       jedis.expire("k1", 30);//设置key的过期时间
       System.out.println(jedis.ttl("k1"));//查询key还有多少秒过期
   }
   ```

4. ```java
   //操作string类型数据
   @Test
   public void test() {
       //创建Jedis对象
       Jedis jedis = new Jedis("47.100.85.66", 6379);
       jedis.mset("k1", "v1", "k2", "v2");//批量添加
       System.out.println(jedis.mget("k1", "k2"));//批量获取
   }
   ```

5. ```java
   //操作list类型数据
   @Test
   public void test() {
       //创建Jedis对象
       Jedis jedis = new Jedis("47.100.85.66", 6379);
       jedis.lpush("k1", "v1", "v2", "v3");//添加list类型数据
       List<String> values = jedis.lrange("k1", 0, -1);//获取key所有的value
       for (String value : values) {
           System.out.println(value);
       }
   }
   ```

6. ```java
   //操作set类型数据
   @Test
   public void test() {
       //创建Jedis对象
       Jedis jedis = new Jedis("47.100.85.66", 6379);
       jedis.sadd("k", "v1", "v2", "v3");//添加set类型数据
       Set<String> smembers = jedis.smembers("k");//获取k的所有member
       for (String smember : smembers) {
           System.out.println(smember);
       }
       jedis.srem("k", "v3");//删除v3
   }
   ```

7. ```java
   //操作hash类型数据
   @Test
   public void test() {
       //创建Jedis对象
       Jedis jedis = new Jedis("47.100.85.66", 6379);
       HashMap<String, String> map = new HashMap<>();
       map.put("id", "1");
       map.put("name", "wgx");
       map.put("age", "22");
       jedis.hmset("users", map);//批量加入hash类型数据
       List<String> fields = jedis.hmget("users", "id", "name", "age");//批量获取hash中的字段
       for (String field : fields) {
           System.out.println(field);
       }
   }
   ```

8. ```
   //操作zset类型数据
   @Test
   public void test() {
       //创建Jedis对象
       Jedis jedis = new Jedis("47.100.85.66", 6379);
       HashMap<String, Double> map = new HashMap<>();
       map.put("java", 100d);
       map.put("C", 200d);
       map.put("C++", 300d);
       //jedis.zadd("language", map);//批量插入zset类型数据
       Set<Tuple> zset = jedis.zrangeByScoreWithScores("language", 0, 400);//批量获取zset数据，并按照分数排序
       for (Tuple tuple : zset) {
           System.out.println("language:" + tuple.getElement() + ",scores:" + tuple.getScore());
       }
   }
   ```

### 整合SpringBoot

1. 引入依赖

   ```xml
   <!--redis-->
   <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   <!--spring2.X集成redis所需要的common-pool2-->
   <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
      <version>2.6.2</version>
   </dependency>
   ```

2. 编写配置文件

   ```yaml
   spring:
     redis:
       #Redis服务器地址
       host: 47.100.85.66
       #Redis服务器连接端口
       port: 6379
       #Redis数据库索引（默认为0）
       database: 0
       #连接超时时间（毫秒）
       connect-timeout: 1200
       jedis:
         pool:
           #连接池最大连接数（负数表示没有限制）
           max-active: 20
           #最大阻塞等待时间（负数表示没有限制）
           max-wait: -1
           #连接池最大空闲连接
           max-idle: 5
           #连接池最小空闲连接
           min-idle: 0
   ```

3. 编写配置类

   ```java
   @Configuration
   @EnableCaching//开启缓存
   public class RedisConfig extends CachingConfigurerSupport {
       @Bean
       public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
           RedisTemplate<String, Object> template = new RedisTemplate<>();
           StringRedisSerializer redisSerializer = new StringRedisSerializer();
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
           ObjectMapper om = new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           jackson2JsonRedisSerializer.setObjectMapper(om);
           template.setConnectionFactory(factory);
           template.setKeySerializer(redisSerializer);
           template.setValueSerializer(jackson2JsonRedisSerializer);
           template.setHashValueSerializer(jackson2JsonRedisSerializer);
           return template;
       }
       @Bean
       public CacheManager cacheManager(RedisConnectionFactory factory) {
           StringRedisSerializer redisSerializer = new StringRedisSerializer();
           Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
           ObjectMapper om = new ObjectMapper();
           om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           jackson2JsonRedisSerializer.setObjectMapper(om);
           RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                   .entryTtl(Duration.ofSeconds(600))
                   .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                   .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                   .disableCachingNullValues();
           RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                   .cacheDefaults(config)
                   .build();
           return cacheManager;
   
       }
   }
   ```

4. 测试

   ```java
   @RestController
   public class RedisController {
       @Autowired
       private RedisTemplate redisTemplate;
       //向Redis中设置值
       @GetMapping("/setName")
       public String setName(@RequestParam("name") String name) {
           redisTemplate.opsForValue().set("name", name);
           return "success";
       }
       //获取Redis中的值
       @GetMapping("getName")
       public String getName() {
           String name = (String) redisTemplate.opsForValue().get("name");
           return name;
       }
   }
   ```

# 第七章 事务和锁机制

## 一，简介

Redis事务是一个单独的隔离操作，事务中的所有命令都会序列化，按顺序的执行。事务在执行的过程中，不会被其他客户端发送来的请求所打断

Redis事务的主要作用就是串联多个命令防止别的命令插队

## 二，multi、exec、discard

从输入multi命令开始，输入的命令都会依次进入命令队列中，但是不会执行，直到输入exec之后，Redis会将之前的命令队列中的命令依次执行

组队过程中可以通过discard来放弃组队

## 三，事务的错误处理

- 组队过程中某个命令出现了报告错误，执行时整个的所有队列都会被取消
- 执行过程中某个命令出现了错误，只有报错的命令不会被执行，而其他的命令都会执行

## 四，悲观锁

悲观锁：每次去拿数据的时候都认为别人会修改，所以每次拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在操作之前先上锁

## 五，乐观锁

乐观锁：每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁用于多读的应用类型，这样可以提高吞吐量，Redis就是利用这种check-and-set机制实现事务的

## 六，watch key [key...]

在执行multi之前，先执行watch key [key...]，可以监视一个或多个key，如果在事务执行之前这个或这些key被其他命令所改动，那么事务将被打断

## 七，Redis事务三特性

- 单独的隔离操作

  事务中的所有命令都会序列化，按顺序的执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断

- 没有隔离级别的概念

  队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行

- 不保证原子性

  事务中如果有一条命令执行失败，其后的命令任然会被执行，没有回滚

### 秒杀案例

```java
@Service
public class Seckill {

    @Autowired
    private RedisTemplate redisTemplate;

    public String seckill(String userId, String goodsId) {
        //userId,goodsId不能为空
        if (userId == null || goodsId == null) {
            return Message.ERROR.getMessage();
        }
        //库存的key
        String stockKey = goodsId + ":stock";
        //秒杀成功的userId的key
        String successUserIdKey = goodsId + ":success";
        //判断该userId是否已经购买成功
        Boolean isMember = redisTemplate.opsForSet().isMember(successUserIdKey, userId);
        if (isMember) {
            return Message.REPEAT_PURCHASE.getMessage();
        }
        //监视库存
        redisTemplate.watch(stockKey);
        //查看剩余库存
        int stock = (Integer) redisTemplate.opsForValue().get(stockKey);
        if (stock < 1) {
            System.out.println("库存不足");
            return Message.SELL_OUT.getMessage();
        }
        //开启事务
        redisTemplate.multi();
        redisTemplate.opsForValue().decrement(stockKey);
        redisTemplate.opsForSet().add(successUserIdKey, userId);
        List result = redisTemplate.exec();
        if (result == null || result.isEmpty()) {
            System.out.println("秒杀失败");
            return Message.ERROR.getMessage();
        }
        System.out.println("秒杀成功");
        return Message.SUCCESS.getMessage();
    }

}
```

==注意：需要开启事务支持==

```java
//开启事务支持
template.setEnableTransactionSupport(true);
```

# 第八章 持久化操作

## 一，RDB

1.  简介

   在指定时间间隔内将内存中的数据集快照写入磁盘，也就是Snapshot快照，它恢复时是将快照下的文件直接读到内存里

2. 备份是如何执行的

   Redis会单独的创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化的文件，整个过程中主进程是不进行任何IO操作的，这就确保了极高的性能，如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效，**RDB的缺点是最后一次持久化数据可能会丢失**

3. fork

   - fork的作用是复制一个与当前进程一样的进程，新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程
   - 在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，处于效率考虑，Linux中引入了==写时复制技术==
   - 一般情况父进程和子进程会共用一段物理内存，只有进程空间的各段内容要发生变化时，才将父进程的内容复制一份给子进程

4. 修改配置文件

   ![](F:\截图\屏幕截图 2022-03-10 210355.png)

5. 优势

   - 适合大规模的数据恢复
   - 对于数据完整性和一致性要求不高更适合使用
   - 节省磁盘空间
   - 恢复速度快

6. 缺点

   - fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
   - 虽然Redis在fork时使用了写时复制技术，但是如果数据庞大时还是比较消耗性能的
   - 在一定时间间隔做一次备份，所以如果Redis意外down掉的话就会丢失最后一次快照的所有修改
   
7. rdb的备份恢复

   - 先通过config get dir查询rdb文件的目录
   - 将rdb文件拷贝到别的地方
   - 关闭Redis
   - 先把备份的文件拷贝到工作目录下
   - 启动Redis，备份数据会直接加载

## 二，AOF

1. 简介

   以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，Redis启动之初会读取该文件重新构建数据。Redis启动的话就根据日志文件将写指令从前到后执行一次以完成数据的恢复工作

2. 开启AOF

   AOF默认不开启，可以在redis.conf中配置文件名称，默认为appendonly.aof

   AOF文件的保存路径，同RDB文件一致

   AOF和RDB同时开启，系统默认取AOF的数据

3. AOF的备份恢复

   AOF的备份与RDB一致

4. 异常恢复

   通过/usr/local/bin/redis-check-aof --fix appendonly.aof进行恢复

5. AOF同步频率设置

   - appendfsync always

     始终同步，每次Redis的写入都会立刻记入日志，性能较差但是数据完整性比较好

   - appendfsync everysec

     每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失

   - appendfsync no

     Redis不主动进行同步，把同步时机交给操作系统

6. AOF持久化流程

   - 客户端的请求命令会被append追加到AOF缓冲区内
   - AOF缓冲区根据AOF持久化策略[always|everysec|no]将操作sync同步到磁盘的AOF文件中
   - AOF文件大小超过重写策略或者手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量
   - Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的

7. 优势

   - 备份机制更稳定，丢失数据概率更低
   - 可读的日志文件，通过操作AOF文件，可以处理误操作

8. 劣势

   - 比RDB占用更多的磁盘空间
   - 恢复备份速度要慢
   - 每次读写都同步的话，有一定的性能压力
   - 存在个别bug，造成恢复不能

### rewrite压缩

1. 简介

   AOF采用文件追加方式，文件会越来越大，为了避免出现此种情况，新增了重写机制，当AOF文件大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据最小指令集，可以使用命令bgrewriteaof

2. 重写原理

   AOF文件持续增长而过大时，会fork出一条新进程来将文件重写（也是先写临时文件最后再rename），Redis4.0版本后的重写是指就是把rdb的快照以二进制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作

## 三，总结

官方推荐两个都启用

如果对数据不敏感，可以选单独用RDB

不建议单独使用AOF，因为可能会出现bug

如果只是做纯内存缓存，可以都不用

# 第九章 主从复制

## 一，简介

主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slaver以读为主

- 读写分离，性能扩展
- 容灾快速恢复

## 二，搭建主从复制

1. info replication

   打印主从复制的相关情况

2. 配从不配主

   slaveof ip port

   成为某个实例的从服务器

   ==如果主机有密码，需要在从机的配置文件中加上：masterauth 主机密码==

## 三，复制原理

1. slave启动成功连接到master后发送一个sync命令
2. master接到命令启动后台的存盘进程，同时收集接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，以完成一次全同步
3. 全量复制：而slave服务在接受到数据库文件数据后，将其存盘并加载到内存中
4. 增量复制：master继续将新的所有收集到的修改命令一次传给slave，完成同步
5. 但是只要是重新连接master，一次完全同步（全量复制）将被自动执行

## 四，薪火相传

上一个slave可以是下一个slave的master，slave同样可以接收其他的slaves的连接和同步请求，那么该slave作为了链条中下一个的master，可以有效的减轻master的写压力，去中心化降低风险

## 五，反客为主

当一个master宕机后，后面的slave可以立刻升为master，其后面slave不用做任何修改

使用命令：slaveof no one

## 六，哨兵模式

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从机转换为主机

1. 新建sentinel.conf文件

2. 配置哨兵

   sentinel monitor mymaster 127.0.0.1 6379 1

   其中mymaster为监控对象起的服务器名称，1为至少有多少个哨兵同一迁移的数量

   有密码的话配置密码：sentinel auth-pass mymaster 密码

3. 启动哨兵

   redis-sentinel sentinel.conf

### 复制延时

由于所有的写操作都是先在master上操作，然后同步更新到slave上，所以从master同步到slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，slave机器数量的增加也会使这个问题更加严重

### 新主服务器选择规则

1. 优先级靠前的

   replica-priority/slave-priority

2. 偏移量最大的

3. runid最小的

# 第十章 集群

## 一，简介

Redis集群实现了对Redis的水平扩容，即启动N个Redis节点，将整个数据库分部存储在这N个节点中，每个节点存储总数据的1/N

Redis集群通过分区（partition）来提供一定程度的可用性（availability）：即使集群中有一部分节点失效或者无法进行通讯，集群也可以继续处理命令请求

## 二，搭建集群

1. 配置文件

   ![](F:\截图\屏幕截图 2022-03-12 222647.png)

2. 启动服务

   ![](F:\截图\屏幕截图 2022-03-12 224035.png)

   生成的节点的配置文件

   ![](F:\截图\屏幕截图 2022-03-12 224146.png)

3. 将六个节点合成一个集群

   - 先进入Redis的安装目录的src目录下：cd /opt/redis/redis-6.2.6/src
   - redis-cli --cluster create --cluster-replicas 1 127.0.0.1:6379 127.0.0.1:6380 127.0.0.1:6381 127.0.0.1:6389 127.0.0.1:6390 127.0.0.1:6391 [-a 密码]

4. 使用集群策略连接

   redis-cli -c -p port

5. 查看集群信息

   cluster nodes

## 三，集群操作

1. slots

   ![](F:\截图\屏幕截图 2022-03-13 002603.png)

   一个Redis集群包含16384个插槽（hash slot），数据库中的每个键都属于这16384个插槽其中一个，集群使用公式CRCI(key)%16384来计算key属于哪个槽，其中CRCI(key)语句用于计算键key的CRCI16检验和

   集群中的每个节点负责处理一部分插槽，例如一个集群可以有主节点，其中

   - 节点A负责处理0号到5460号插槽
   - 节点B负责处理5461号到10922号插槽
   - 节点C负责处理10923号到16383号插槽

   ![](F:\截图\屏幕截图 2022-03-13 003355.png)

2. 插入数据

   - 插入单条数据

     ![](F:\截图\屏幕截图 2022-03-13 004138.png)

   - 插入多条数据

     需要通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去

     ![](F:\截图\屏幕截图 2022-03-13 004340.png)

3. 查询集群中的值

   - 计算key的插槽

     cluster keyslot key

   - 计算插槽值里面有多少key

     cluster countkeysinslot 12706

   - 返回插槽键的数量

     cluster getkeysinslot 12706 10

## 四，集群的Jedis开发

```java
public class Cluster {
    public static void main(String[] args) {
        HostAndPort hostAndPort = new HostAndPort("47.100.85.66", 6379);
        JedisCluster jedisCluster = new JedisCluster(hostAndPort);
        jedisCluster.set("k1", "v1");
        String k1 = jedisCluster.get("k1");
        System.out.println(k1);
        jedisCluster.close();
    }
}
```

# 第十一章 应用问题解决

## 一，缓存穿透

1. 问题描述

   key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源，比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库

2. 解决方案

   一个一定不存在的缓存及查询不到数据，由于缓存是不命中时被动写的，并且处于容错考虑，如果从储存层差不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到储存层去查询，失去了缓存的意义

   - 对空值缓存

     如果一个查询返回的数据为空（不管是数据是否不存在），任然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过5分钟

   - 设置可访问的名单（白名单）

     使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmaps里面的id进行比较，如果访问id不再bitmaps里面，进行拦截，不允许访问

   - 采用布隆过滤器

     布隆过滤器（Bloom Filter）是1970年有布隆提出的，它实际上是一个很长的二进制向量（位图）和一系列随机映射函数（哈希函数）。布隆过滤器可以用于检索一个元素是否在一个集合中，它的优点是空间效率和查询时间都远远超过一半算法，缺点是有一定的误识别率和删除困难

   - 进行实时监控

     当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务

## 二，缓存击穿

1. 问题描述

   key对应的数据存在，但在Redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候打并发的请求可能会瞬间把后端DB压垮

2. 解决方案

   key可能会在某些时间点被超高并发的访问，是一种非常“热点”的数据。这个时候需要考虑一个缓存被“击穿”的问题

   - 预先设置热门数据：在Redis高峰访问之前，把一些热门数据提前存入到Redis中，加大这些热门数据key的时长
   - 实时调整：现场监控那些数据热门，实时调整key的过期时长
   - 使用锁：
     1. 就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。
     2. 先使用缓存工具的某些带成功操作返回值的操作（比如Redis的setnx）去set一个mutex key
     3. 当操作返回成功时，再进行load db的操作，并回设缓存，最后删除mutex key
     4. 当操作返回失败时，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法

## 三，缓存雪崩

1. 问题描述

   ​	key对应的数据存在，但在Redis中过期，此时有大量的并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候打并发的请求可能会瞬间把后端DB压垮

   ​	缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key正常访问

2. 解决方案

   缓存失效时的雪崩效应对底层系统的冲击非常大

   - 构建多级缓存架构：nginx缓存+redis缓存+其他缓存（ehcache等）
   - 使用锁或队列：用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层储存系统上，不适合高并发情况
   - 设置过期标志更新缓存：记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存
   - 将缓存失效时间分散开：比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件

## 四，分布式锁

1. 问题描述

   随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于
   分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发
   控制锁策略失效，单纯的 Java API并不能提供分布式锁的能力。为了解决这个问题就
   需要一种跨 JVM 的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

   分布式锁主流的实现方案。

   1. 基于数据库实现分布式锁
   2. 基于缓存（Redis等）。
   3. 基于Zookeeper

   每一种分布式锁解决方案都有各自的优缺点：。
   1.  性能：redis 最高
   2. 可靠性：zookeeper最高

2. 使用Redis实现分布式锁

   1. 上锁：set key value nx ex sec
   2. 释放锁：del key

3. 分布式锁整合java

   ```java
   public class Lock {
       @Autowired
       RedisTemplate redisTemplate;
       public void testLock(){
           //获取锁
           Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "1", 3, TimeUnit.SECONDS);
           if (lock) {
               Object num = redisTemplate.opsForValue().get("num");
               //判断num是否为空
               if (StringUtils.isEmpty(num)) {
                   return;
               }
               int value = Integer.parseInt(num.toString());
               redisTemplate.opsForValue().set("num", ++value);
               //释放锁
               redisTemplate.delete("lock");
           }else {
               //获取失败，间隔0.1秒再次获取
               try {
                   Thread.sleep(100);
                   testLock();
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       }
   }
   ```

4. 分布式锁至少需要确保的四个条件

   - 互斥性：在任意时刻，只有一个客户端能持有锁。
   - 不会发生死锁：即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
   - 解铃还须系铃人：加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。
   - 加锁和解锁必须具有原子性。

# 第十二章 Redis6.0新功能

## 一，ACL

1. 简介

   Redis ACL是Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接。
   在Redis 5 版本之前，Redis安全规则只有密码控制 还有通过 rename 来调整
   高危命令比如 flushdb，KEYS*，shutdown 等。Redis 6 则提供ACL的功能对用户
   进行更细粒度的权限控制 ：

   - 接入权限：用户名和密码。

   - 可以执行的命令。

   - 可以操作的 KEY

2. 命令

   - acl list：展现用户权限列表
   - acl cat：查看添加权限指令类别
   - acl setuser user1：创建新用户默认权限
   - acl setuser user1 on >password ~cached:* +get：设置有用户名、密码、ACL权限、并启用用户

## 二，IO多线程

1. 简介

   IO 多线程其实指客户端交互部分的网络 IO 交处理模块多线程，而非执行命令多线程。Redis6 执行命令依然是单线程。

2. 原理架构

   Redis 6 加入多线程,但跟 Memcached 这种从 IO 处理到数据访问多线程的实现模式有些差异。Redis 的多线程部分只是用来处理网络数据的读写和协议解析，执行命令仍然是单线程。之所以这么设计是不想因为多线程而变得复杂，需要去控制 key、lua、事务，LPUSH/LPOP 等等的并发问题。

# 第十三章 高级篇

## 一，hash算法

1. 哈希取余算法

   用户每次读写操作都是根据公式：hash(key) % N个机器台数，计算出hash值来决定映射到哪一个节点上

2. 一致性哈希算法

   - 一致性哈希环

     一致性哈希算法必然有个hash函数并按照算法产生hash值，这个算法的所有可能哈希值会构成一个全量集，这个集合可以成为一个hash空间 [0, 2^32^-1]，这个是一个线性空间，但是在算法中，我们通过适当的逻辑控制将它首尾相连(0=2^32^)，这样让它逻辑上形成了一个环形空间。

     他也是按照使用取模的方法，但是一致性哈希算法是对2^32^取模。简单来说，一致性哈希算法将整个哈希值组织成一个虚拟的圆环。

   - 服务器ip节点映射

     将集群中各个ip节点映射到环上的某一个位置。 

     将各个服务器使用Hash进行一个哈希，具体可以选择服务器的ip或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希坏上的位置。假如 4个节点NodeA、B、C、D，经过IP地址的哈希函数计算(hash(ip))，使用IP地址哈希后在环空间的位置如下：

     ![](F:\截图\屏幕截图 2023-02-19 164259.png)

   - key落到服务器的落键规则

     当我们需要存储一个kv键值对时，首先计算key的hash值hash(key)，将这个key使用相同的函数Hash计算出哈希值并确定此数据在环上的位置，从此位置沿环顺时针“行走”，第一台遇到的服务器就是其应该定位到的服务器，并将该键值对存储在该节点上。

      如我们有Object A、Object B、Object C、Object D四个数据对象，经过哈希计算后，在环空间上的位置如下：

     ![](F:\截图\屏幕截图 2023-02-19 164608.png)

     根据一致性Hash算法，数据A会被定 为到Node A上，B被定为到Node B上，C被定为到Node C上，D被定为到Node D上。

3. 哈希槽分区

   哈希槽实质就是一个数组，数组[0, 2^14^-1]形成hash slot空间

   解决均匀分配的问题，在数据和节点之间又加入了一层，把这层称之为哈希槽（slot），用于管理数据和节点之间的关系，现在就相当于节点上放的是槽，槽里放的是数据

   ![](F:\截图\屏幕截图 2023-02-19 170220.png)

   槽解决的是粒度问题，相当于把粒度变大了，这样便于数据移动

   哈希解决的是映射问题，使用key的哈希值来计算所在的槽，便于数据分配

   一个集群只能有16384个槽，编号0-16383(0~2^14^-1)。这些槽会分配给集群中的所有主节点，分配策略没有要求。可以指定哪些编号的槽分配给哪个主节点。集群会记录节点和槽的对应关系。解决了节点和槽的关系后，接下来就需要对key求哈希值，然后对16384取余，余数是几key就落入对 应的槽里。slot=CRC16(key)%16384。以槽为单位移动数据，因为槽的数目是固定的，处理起来比较容易，这样数据移动问题就解决了。

## 二，BigKey

1. 禁用危险命令

   ![](F:\截图\屏幕截图 2023-05-10 155701.png)

2. scan代替keys *

   - 语法：SCAN cursor [MATCH pattern] [COUNT count]
   - 基于游标的迭代器，需要基于上一次的游标延续之前的迭代过程以0作为游标开始一次新的迭代，直到命令返回游标0完成一次遍历不保证每次执行都返回某个给定数量的元素，支持模糊查询一次返回的数量不可控，只能是大概率符合count参数

3. 发现BigKey

   - redis-cli --bigkeys
   - memory usage

4. 删除BigKey

   - string类型：del
   - hash类型：hscan + hdel
   - list类型：ltrim
   - set类型：sscan + srem
   - zset类型：zscan + zremrangebyrank

5. 惰性释放

   开启配置

   ![](F:\截图\屏幕截图 2023-05-10 164350.png)

## 三，Bloom过滤器

1. 是什么

   布隆过滤器(Bloom Filter)是一种专门用来解决去重问题的高级数据结构。实质就是一个大型位数组和几个不同的无偏hash函数（无偏表示分布均匀）。由一个初值都为零的bit数组和多个个哈希函数构成，用来快速判断某个数据是否存在。但是跟HyperLogLog一样，也一样有那么一点点不精确，也存在一定的误判概率

2. 特点

   - 一个元素如果判断结果存在，**元素不一定存在**，但是判断结果为**不存在是一定不存在**
   - 布隆过滤器可以添加元素，但是不能删除元素，由于涉及hashcode判断依据，删掉元素会导致误判率增加。

3. Guava版布隆过滤器

   ```java
   @Test
   public void testGuavaBloomFilter() {
       //创建Guava版BloomFilter
       BloomFilter<Integer> bloomFilter = BloomFilter.create(Funnels.integerFunnel(), 100);
       //判断元素是否存在
       System.out.println(bloomFilter.mightContain(1));
       //放入元素
       bloomFilter.put(1);
       System.out.println(bloomFilter.mightContain(1));
   }
   ```

## 四，内存淘汰策略

1. 查看redis的最大内存，和占用内存情况

   - 查看redis最大内存

     config get maxmemory或者查看配置文件中的maxmemory

   - 查看内存占用情况

     info memory

2. 开启惰性淘汰

   lazyfree-lazy-eviction yes

3. LRU

   最近最少使用页面置换算法，淘汰最长时间未被使用的页面，看页面最后一次被使用到发生调度的时间长短，首先淘汰最长时间未被使用的页面。

4. LFU

   最近最不常用页面置换算法，淘汰一定时期内被访问次数最少的页面，看一定时间段内页面被使用的频率，淘汰一定时期内被访问次数最少的页面

5. redis内存淘汰策略

   - noeviction：不会驱逐任何key，表示即使内存达到上限也不进行置换，所有能引起内存增加的命令都会返回error
   - allkeys-lru：对所有key使用LRU算法进行删除，优先删除掉最近最不经常使用的key，用以保存新数据
   - volatile-lru：对所有设置了过期时间的key使用LRU算法进行删除
   - allkeys-random：对所有key随机删除
   - volatile-random：对所有设置了过期时间的key随机删除
   - volatile-ttl：删除马上要过期的key
   - allkeys-lfu：对所有key使用LFU算法进行删除
   - volatile-lfu：对所有设置了过期时间的key使用LFU算法删除

## 五，Redis7新特性

1. 多AOF文件支持

   7.0版本中一个比较大的变化就是aof文件由一个变成了多个，主要分为两种类型：基本文件（base files)、增量文件（incr files)，请注意这些文件名称是复数形式说明每一类文件不仅仅只有一个。在此之外还引入了一个清单文件（manifest）用于跟踪文件以及文件的创建和应用顺序（恢复）

2. config命令增强

   对于Config Set和Get命令，支持在一次调用过程中传递多个配置参数。例如，现在我们可以在执行一次Config Set命令中更改多个参数：config set maxmemory1 0000001 maxmemory-clients 50% port 6399

3. 限制客户端的内存使用Client-eviction

   一旦Redis连接较多，再加上每个连接的内存占用都比较大的时候，Redis总连接内存占用可能会达到 maxmemory的上限，可以增加允许限制所有客户端的总内存使用量配置项，redis.config中对应的配置项

   两种配置形式：指定内存大小、基于maxmemory的百分比。 

   - maxmemory-clients 1g

   - maxmemory-clients 10%

4. listpack紧凑列表调整

   listpack是用来替代ziplist的新数据结构，在7.0版本已经没有ziplist的配置了（6.0版本仅部分数据类型作为过渡阶段在使用）listpack已经替换了ziplist类似hash-max-ziplist-entries的配置

5. 访问安全性增强ACLV2

   在redis.conf配置文件中，protected-mode默认为yes，只有当你希望你的客户端在没有授权的情况下可以连接到 Redis serverl的时候可以将protected-mode设置为no

## 六，RedisObject

为了便于操作，Redis采用redisObject结构来统一五种不同的数据类型，这样所有的数据类型就都可以以相同的形式在函数间传递而不用使用特定的类型结构。同时，为了识别不同的数据类型，redisObjec中定义了type和encoding字段对不同的数据类型加以区别。简单地说，redisObject就是string、hash、Iist、set、zset的父类，可以在函数间传递时隐藏具体的类型信息，所以作者抽象了 redisobject结构来到达同样的目的。

1. 查看key的类型

   TYPE key

2. 查看key的编码

   OBJECT ENCODING key

3. 各字段的含义

   ![](F:\截图\屏幕截图 2023-06-13 153643.png)

   - 4位的type表示具体的数据类型

     ![](F:\截图\屏幕截图 2023-06-13 153938.png)

   - 4位的encoding表示该类型的物理编码方式，同一种数据类型可能有不同的编码方式（比如：string提供了3种，int、embstr、raw）

   - lru表示当内存超限时采用LRU算法清除内存中的对象

   - refcount表示对象的应用计数

4. 查看key的各字段

   DEBUG OBJECT key

## 七，String

1. String的3大物理编码方式

   - int
   - embstr
   - raw

2. int

   - 保存long型的64位（8字节）有符号整数
   - 只有整数才会使用int，如果是浮点数，Redis内部其实先将浮点数转化为字符串值，然后再保存。

3. embstr

   - embedded string：嵌入式的字符串

   - 代表embstr格式的SDS（Simple Dynamic String 简单动态字符串），保存长度小于44字节的字符串

4. SDS对比C语言字符串

   - 字符串的长度处理

     - C语言：需要从头开始遍历，直到遇到'\0'为止，时间复杂度O(N)
     - SDS：记录当前字符串的长度，直接读取即可，时间复杂度O(1)

   - 内存重新分配

     - C语言：分配内存空间超过后，会导致数组下标越级或者内存分配溢出

     - SDS：SDS修改后，Ien长度小于1M，那么将会额外分配与Ien相同长度的未使用空间。如果修改后长度大于1M，那么将分配1M的使用空间。

       有空间分配对应的就有空间释放。SDS缩短时并不会回收多余的内存空间，而是使用free字段将多出来的空间记录下来。如果后续有变更操作，直接使用free中记录的空间，减少了内存的分配。

   - 二进制安全

     - C语言：二进制数据并不是规则的字符串格式，可能会包含些特殊的字符，比如'\0'等。前面提到过，C中字符串遇到'\0'会结束，那'\0'之后的数据就读取不上了
     - 根据Ien长度来判断字符串结束的，二进制安全的问题就解决了

5. 对于embstr,由于其实现是只读的，因此在对embstr对象进行修改时，都会先转化为raw再进行修改。因此，只要是修改embstr对象，修改后的对象一定是raw的，无论是否达到了44个字节

   修改字符串APPEND key v

## 八，Hash

1. hash的编码格式

   - redis6：ziplist+hashtable
   - redis7：listpack+hashtable

2. hash相关配置

   - hash-max-ziplist-entries：使用压缩列表保存时哈希集合中的最大元素个数。

   - hash-max-ziplist-value：使用压缩列表保存时哈希集合中单个元素的最大长度。
   - Hash类型键的字段个数小于hash-max-ziplist-entries并且每个字段名和字段值的长度小于hash-max-ziplist-value时， Redis才会使用OBJ_ENCODING_ZIPLIST来存储该键，前述条件任意一个不满足则会转换为OBJ_ENCODING_HT的编码方式
   - redis7的listpack同上

3. ziplist

   ziplist压缩列表是一种紧凑编码格式，总体思想是多花时间来换取节约空间，即以部分读写性能为代价，来换取极高的内存空间利用率，因此只会用于字段个数少，且字段值也较小的场景。压缩列表内存利用率极高的原因与其连续内存的特性是分不开的。

   ziplist是一个经过特殊编码的双向链表，它不存储指向前一个链表节点prev和指向下一个链表节点的指针next而是存储上一个节点长度和当前节点长度，通过牺牲部分读写性能，来换取高效的内存空间利用率，节约内存，是一种时间换空间的思想。只用在字段个数少，字段值小的场景里面

4. ziplist的各个组成单元

   | 属性    | 类型     | 长度  | 用途                                                         |
   | ------- | -------- | :---- | :----------------------------------------------------------- |
   | zlbytes | uint32_t | 4字节 | 记录整个压缩列表占用的内存字节数，在对压缩列表进行内存重分配，或者计算zlend的位置时使用 |
   | zltail  | uint32_t | 4字节 | 记录压缩列表表尾节点距离压缩列表的起始地址有多少字节，通过这个偏移量，程序无须遍历整个压缩列表就可以确定表尾节点的地址 |
   | zllen   | uint16_t | 2字节 | 记录了压缩列表包含的节点数量，当这个属性的值小于UINT16MAX(65535)时，这个属性的值就是压缩列表包含节点的数量；当这个值等于 UINT16_MAX时，节点的真实数量需要遍历整个压缩列表才能计算得出 |
   | entryX  | 列表节点 | 不定  | 压缩列表包含的各个节点，节点的长度由节点保存的内容决定       |
   | zlend   | uint8_t  | 1字节 | 特殊值0×FF（十进制255)，用于标记压缩列表的末端               |

5. zlentry的结构

   ![](F:\截图\屏幕截图 2023-06-13 171640.png)

6. ziplist的连锁更新问题

   压缩列表新增某个元素或修改某个元素时，如果空间不不够，压缩列表占用的内存空间就需要重新分配。而当新插入的元素较大时，可能会导致后续元素的prevlen占用空间都发生变化，从而引起「连锁更新」问题，导致每个元素的空间都要重新分配，造成访问压缩列表性能的下降。

7. listpack的结构

   | 属性              | 用途                                                         |
   | ----------------- | ------------------------------------------------------------ |
   | Total Bytes       | 整个listpack的空间大小，占用4个字节，每个listpack最多占用4294967295Bytes |
   | num-elements      | listpack中的元素个数，即Entry的个数占用2个字节               |
   | element           | 每个具体的元素                                               |
   | listpack-end-byte | listpack结束标志，占用1个字节，内容为0xFF                    |

8. listpack的entry结构

   | 属性     | 用途               |
   | -------- | ------------------ |
   | encoding | 当前节点的编码类型 |
   | data     | 实际存放的数据     |
   | len      | 当前节点的长度     |

## 九，List

1. list的编码格式
   - redis6：quicklist+ziplist
   - redis7：quicklist+listpack
2. list的相关配置
   - list-max-ziplist-size：当取正值到时候，表示按照数据项个数来限定每个quicklist节点上的ziplist长度。比如，当这个参数是5到时候，表示每个quicklist节点的ziplist最多包含5个数据项。当取负值的时候，表示按照占用字节数来限定每个quicklist节点上的ziplist长度。比如，当这个参数是5的时候，表示每个quicklist节点上的ziplist大小不能超过64kb（$2^{5+1}$）。这时它只能取-1到-5这五个值
   - list-compress-depth：表示一个quicklist两端不被压缩的节点个数，这里的节点是指quicklist双向链表的节点，而不是指ziplist里面的数据项个数

## 十，Set

1. set的编码格式

   intset+hashtable

2. set的相关配置

   set-max-intset-entries：集合元素都是整数，并且元素个数小于等于set-max-intset-entries时，编码就是intset，反之为hashtable

## 十一，Zset

1. zset的编码格式
   - redis6：ziplist+skiplist
   - redis7：listpack+skiplist
2. zset相关配置
   - zset-max-ziplist-entries：zset的个数小于等于zset-max-ziplist-entries时，编码就是ziplist，超过变成skiplist
   - zset-max-ziplist-value：zset的元素个数小于等于zset-max-ziplist-value时，编码就是ziplist，超过变成skiplist
   - redis7的listpack同上

## 十二，IO模型

1. 阻塞IO模型

   最传统的一种IO模型，即在读写数据过程中会发生阻塞现象。当用户线程发出IO请求之后，内核会去查看数据是否就绪，如果没有就绪就会等待数据就绪，而用户线程就会处于阻塞状态，用户线程交出CPU。当数据就绪之后，内核会将数据拷贝到用户线程，并返回结果给用户线程，用户线程才解除 block 状态。典型的阻塞 IO 模型的例子为：data = socket.read()如果数据没有就绪，就会一直阻塞在read方法

2. 非阻塞IO模型

   当用户线程发起一个 read 操作后，并不需要等待，而是马上就得到了一个结果。如果结果是一个error 时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦内核中的数据准备好了，并且又再次收到了用户线程的请求，那么它马上就将数据拷贝到了用户线程，然后返回。所以事实上，在非阻塞IO模型中，户线程需要不断地询问内核数据是否就绪，也就说非阻塞IO不会交出CPU，而会一直占用CPU。

3. 多路复用IO模型

   多路复用IO模型是目前使用得比较多的模型。Java的NIO实际上就是多路复用IO。在多路复用IO模型中，会有一个线程不断去轮询多个socket的状态，只有当socket真正有读写事件时，才真正调用实际的IO读写操作。因为在多路复用IO模型中，只需要使用一个线程就可以管理多个socket，系统不需要建立新的进程或者线程，也不必维护这些线程和进程，并且只有在真正有socket读写事件进行时，才会使用IO资源，所以它大大减少了资源占用。在JavaNIO中，是通过selector.select()去查询每个通道是否有到达事件，如果没有事件，则一直阻塞在那里，因此这种方式会导致用户线程的阻塞。多路复用IO模式，通过一个线程就可以管理多个socket，只有当socket真正有读写事件发生才会占用资源来进行实际的读写操作。因此，多路复用IO比较适合连接数比较多的情况。另外多路复用IO为何比非阻塞IO模型的效率高是因为在非阻塞IO中，不断地询问socket状态时通过用户线程去进行的，而在多路复用IO中，轮询每个socket状态是内核在进行的，这个效率要比用户线程要高的多。不过要注意的是，多路复用IO模型是通过轮询的方式来检测是否有事件到达，并且对到达的事件逐一进行响应。因此对于多路复用IO模型来说，一旦事件响应体很大，那么就会导致后续的事件迟迟得不到处理，并且会影响新的事件轮询。

4. 信号驱动IO模型

   在信号驱动IO模型中，当用户线程发起一个IO请求操作，会给对应的socket注册一个信号函数，然后用户线程会继续执行，当内核数据就绪时会发送一个信号给用户线程，用户线程接收到信号之后，便在信号函数中调用IO读写操作来进行实际的IO请求操作。

5. 异步IO模型

   异步IO模型才是最理想的IO模型，在异步IO模型中，当用户线程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从内核的角度，当它受到一个asynchronous read之后，它会立刻返回，说明read请求已经成功发起了，因此不会对用户线程产生任何block。然后，内核会等待数据准备完成，然后将数据拷贝到用户线程，当这一切都完成之后，内核会给用户线程发送一个信号，告诉它read操作完成了。也就说用户线程完全不需要实际的整个IO操作是如何进行的，只需要先发起一个请求，当接收内核返回的成功信号时表示IO操作已经完成，可以直接去使用数据了。也就说在异步IO模型中，IO操作的两个阶段都不会阻塞用户线程，这两个阶段都是由内核自动完成，然后发送一个信号告知用户线程操作已完成。用户线程中不需要再次调用IO函数进行具体的读写。这点是和信号驱动模型有所不同的，在信号驱动模型中，当用户线程接收到信号表示数据已经就绪，然后需要用户线程调用IO函数进行实际的读写操作；而在异步IO模型中，收到信号表示IO操作已经完成，不需要再在用户线程中调用IO函数进行实际的读写操作
