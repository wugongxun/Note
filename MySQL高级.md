# 第一章 MySQL的架构介绍

## 一，总体概览

1. 连接层

   最上层是一些客户端和连接服务，包含本地sock通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于
   SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

2. 服务层

   第二层架构主要完成大多少的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺序，是否利用素引等，最后生成相应的执行操作。如果是sect语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

3. 引擎层

   存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取。后面介绍MyISAM和InnoDB

4. 存储层

   数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

## 二，存储引擎介绍

MyISAM和InnoDB

![](F:\截图\屏幕截图 2022-04-22 172126.png)

# 第二章 索引优化

## 一，常见通用的join查询

1. SQL的执行顺序

   ![](F:\截图\屏幕截图 2022-04-22 173712.png)

   ![](F:\截图\屏幕截图 2022-04-22 173847.png)

2. 七种join理论

   - ![](F:\截图\屏幕截图 2022-04-22 174947.png)
   - ![](F:\截图\屏幕截图 2022-04-22 175007.png)
   - ![](F:\截图\屏幕截图 2022-04-22 175027.png)
   - ![](F:\截图\屏幕截图 2022-04-22 175052.png)
   - ![](F:\截图\屏幕截图 2022-04-22 175105.png)
   - ![](F:\截图\屏幕截图 2022-04-22 175143.png)
   - ![](F:\截图\屏幕截图 2022-04-22 175202.png)

   

3. 七种join的SQL编写

   ```sql
   SELECT * FROM tbl_emp AS e INNER JOIN tbl_dept AS d ON e.deptId = d.id;
   SELECT * FROM tbl_emp AS e LEFT JOIN tbl_dept AS d ON e.deptId = d.id;
   SELECT * FROM tbl_emp AS e RIGHT JOIN tbl_dept AS d ON e.deptId = d.id;
   SELECT * FROM tbl_emp AS e LEFT JOIN tbl_dept AS d ON e.deptId = d.id WHERE d.id IS NULL;
   SELECT * FROM tbl_emp AS e RIGHT JOIN tbl_dept AS d ON e.deptId = d.id WHERE e.deptId IS NULL;
   SELECT * FROM tbl_emp AS e LEFT JOIN tbl_dept AS d ON e.deptId = d.id
   UNION
   SELECT * FROM tbl_emp AS e RIGHT JOIN tbl_dept AS d ON e.deptId = d.id;
   SELECT * FROM tbl_emp AS e LEFT JOIN tbl_dept AS d ON e.deptId = d.id WHERE d.id IS NULL
   UNION
   SELECT * FROM tbl_emp AS e RIGHT JOIN tbl_dept AS d ON e.deptId = d.id WHERE e.deptId IS NULL;
   ```

## 二，索引

1. 索引是什么

   索引是帮助MySQL高效获取数据的数据结构，索引的目的在于提高查询效率。可以简单理解为==“排好序的快速查找数据结构”==。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引。

   一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上。

2. 索引的优势和劣势

   优势：

   - 提高数据检索的效率，降低数据库的IO成本。
   - 通过索引列队数据进行排序，降低数据排序的成本，降低了CPU的消耗。

   劣势：

   - 实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录，所以索引列也是要占用空间的
   - 虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息
   - 索引只是提高效率的一个因素，如果你的MySQL有大数据量的表，就需要花时间研究建立最优秀的索引。

3. 索引的分类

   - 单值索引：即一个索引只包含单个列，一个表可以有多个单列索引。

   - 唯一索引：索引列的值必须唯一，但允许有空值。

   - 复合索引：即一个索引包含多个列

   - 基本语法：

     - 创建：CREATE [UNIQUE] INDEX indexName ON mytable(columnname(length));

       ​	ALTER mytable ADD [UNIQUE] INDEX [indexName] ON (columnname(length));

     - 删除：DROP INDEX [indexName] ON mytable;

     - 查看：SHOW INDEX FROM table_name;

4. 索引的结构

   - BTree索引

     ![](F:\截图\屏幕截图 2022-04-22 191111.png)

     - 初始化介绍

       一颗b+树，浅蓝色的块我们称之为一个磁盘块，可以看到每个磁盘块包含几个数据项（深蓝色所示）和指针（黄色所示），如磁盘块1包含数据项17和35，包含指针P1、P2、P3,P1表示小于17的磁盘块，P2表示在17和35之间的磁盘块，P3表示大于35的磁盘块。真实的数据存在于叶子节点即3、5、9、10、13、15、28、29、36、60、75、79、90、99。非叶子节点只不存储真实的数据，只存储指引搜素方向的数据项，如17、35并不真实存在于数据表中。

     - 查找过程

       如果要查找数据项29，那么首先会把磁盘块1由磁盘加载到内存，此时发生一次IO,在内存中用二分查找确定29在17和35之间，锁定磁盘块1的P2指针，内存时间因为非常短（相比磁盘的IO)可以忽略不计，通过磁盘块1的P2指针的磁盘地址把磁盘块3由磁盘加载到内存，发生第二次IO,29在26和30之间，锁定磁盘块3的P2指针，通过指针加载磁盘块8到内存，发生第三次IO,同时内存中做二分查找找到29，结束查询，总计三次IO。

   - Hash索引

   - full-text索引

   - R-Tree索引

5. 适合创建索引的情况

   - 主键自动建立唯一索引
   - 频繁作为查询条件的字段应该建立索引
   - 查询中与其他表关联的字段，外键关系建立索引
   - 在高并发下倾向创建组合索引
   - 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
   - 查询中统计或者分组字段

6. 不适合创建索引的情况

   - 频繁更新的字段不创建索引
   - where条件里用不到的字段不创建索引
   - 表记录太少
   - 数据重复且分布平均的表字段。

## 三，explain

1. 作用

   - 表的读取顺序
   - 数据读取操作的操作类型
   - 哪些索引可以使用
   - 哪些索引被实际使用
   - 表之间的引用
   - 每张表有多少行被优化器查询

2. 执行计划包含的信息

   ![](F:\截图\屏幕截图 2022-04-22 224053.png)

   - id：select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序

     id相同，执行顺序由上至下

     id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行

     id相同不同，同时存在

   - select_type：查询类型，主要是用于区别普通查询、联合查询、子查询等复杂查询

     ![](F:\截图\屏幕截图 2022-04-22 225317.png)

     - SIMPLE：简单的select查询，查询中不包含子查询或者UNION
     - PRIMARY：查询中若包含任何复杂的子部分，最外层查询则被标记为PRIMARY
     - SUBQUERY：在select或where列表中包含了子查询
     - DERIVED：在FROM列表中包含的子查询被标记为DERIVED(衍生)MySQL会递归执行这些子查询，把结果放在临时表里。
     - UNION：  若第二个SELECT出现在UNION之后，则被标记为UNION:若UNION包含在FROM子句的子查询中，外层SELECT将被标记为：DERIVED
     - UNION RESULT：从UNION表获取结果的SELECT

   - table：显示这一行的数据是关于那张表的

   - type：访问类型排列

     ![](F:\截图\屏幕截图 2022-04-22 230312.png)

     显示查询使用了何种类型

     ==从最好到最差依次是：==

     system>const>eq_ref>ref>range>ALL

     - system：表只有一行记录（等于系统表），这是const类型的特列，平时不会出现，这个也可以忽略不计
     - const：表示通过索引一次就找到了，const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快，如将主键置于where列表中，MySQL就能将该查询转换为一个常量
     - eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描
     - ref：非唯一性索引扫描，返回匹配某个单独值的所有行，本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体
     - range：只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引一般就是在你的wherei语句中出现了between、<、>、in等的查询这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。
     - index：Full Index Scan，index与ALL区别为index类型只遍历索引树。这通常比ALL快，因为索引文件通常比数据文件小(也就是说虽然all和Index都是读全表，但index是从索引中读取的，而all是从硬盘中读的)
     - ALL：Full Table Scan，将遍历全表以找到匹配的行

   - possible_keys：显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，**但不一定被查询实际使用**。

   - key：实际使用的索引。如果为null，则没有使用索引

     查询中若使用了覆盖索引，则该索引仅出现在key列表中

   - key_len：表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精确性的情况下，长度越短越好。key_Ien显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的。

   - ref：显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值

   - rows：根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数

   - Extra：包含不适合在其他列中显示但十分重要的额外信息

     - ==Using filesort==：说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取，MySQL中无法利用索引完成的排序操作称为“文件排序”

     - ==Using temporary==：使用了临时表保存中间结果，MySQL在对查询结果排序时使用临时表。常见于排序order by和分组查询group by

     - ==Using index==：表示相应的select操作中使用了覆盖索引(Covering Index)，避免访问了表的数据行，效率不错！如果同时出现using where,表明索引被用来执行索引键值的查找；如果没有同时出现using where,表明索引用来读取数据而非执行查找动作。

       **覆盖索引**

       **就是select的数据列只用从素引中就能够取得，不必读取数据行，MySQL可以利用素引返回select列表中的字段，而不必根据索引再次读取数据文件，换句话说查询列要被所建的索引覆盖**。

     - Using where：表示使用了where过滤

     - Using join buffer：使用了连接缓存

     -  impossible where：where子句的值总是false，不能用来获取任何元组

     - select tables optimized away：在没有GROUPBY子句的情况下，基于索引优化MIN/MAX操作或者对于MyISAM存储引擎优化COUNT(*)操作，不必等到执行阶段再进行计算，查询执行计划生成的阶段即完成优化。

     - distinct：优化distinct操作，在找到第一匹配的元组后即停止找同样值的动作

## 四，索引优化

1. join语句优化结论
   - 尽可能减少Join语句中的NestedLoop的循环总次数；“永远用小结果集驱动大的结果集”。
   - 优先优化NestedLoop的内层循环。
   - 保证Join语句中被驱动表上Join条件字段已经被索引。
   - 当无法保证被驱动表的Join条件字段被索引且内存资源充足的前提下，不要太吝惜JoinBuffer的设置
2. 索引失效
   - 全值匹配我最爱
   - 最佳左前缀法则：如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且不跳过索引中的列。
   - 不在索引列上做任何操作（计算、函数、（自动or手动）类型转换)，会导致索引失效而转向全表扫描
   - 存储引擎不能使用索引中范围条件右边的列
   - 尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）)，减少select*
   - mysql在使用不等于(!=或者<>)的时候无法使用索引会导致全表扫描。
   - is null,is not null也无法使用索引
   - Iike以通配符开头('%abc...')mysql索引失效会变成全表扫描的操作
   - 字符串不加单引号索引失效
   - 少用or，用它来连接时会索引失效
3. 创建索引一般性优化
   - 对于单键索引，尽量选择针对当前query j过滤性更好的索引
   - 在选择组合索引的时候，当前Query中过滤性最好的字段在索引字段顺序中，位置越靠前越好。
   - 在选择组合索引的时候，尽量选择可以能够包含当前query中的where字句中更多字段的索引
   - 尽可能通过分析统计信息和调整query的写法来达到选择合适索引的目的

# 第三章 查询截取分析

## 一，order by优化

1. ORDER BY子句，尽量使用Index方式排序，避免使用FileSort方式排序
2. 尽可能在索引列上完成排序操作，遵照索引建的最佳左前缀
3. 如果不在索列上，filesort有两种算法：mysql就要启动双路排序和单路排序
4. ![](F:\截图\屏幕截图 2022-04-25 161108.png)

## 二，group by优化

1. group by实质是先排序后进行分组，遵照索引建的最佳左前缀
2. 当无法使用索引列，增大max_length_for_sort_data参数的设置+增大sort_buffer_size参数的设置
3. where高于having，能写在where限定的条件就不要去having限定了。

## 三，慢查询日志

1. MySQL的慢查询目志是MySQL提供的一种目志记录，它用来记录在MySQL中响应时间超过阀值的语句，具体指运行时间超过long_query_time值的SQL,则会被记录到慢查询日志中。具体指运行时间超过long_query_time值的SQL,则会被记录到慢查询目志中。long_query_time的默认值为10，意思是运行10秒以上的语句。

2. 查看和启动慢查询日志

   - 查看：SHOW VARIABLES LIKE '%slow_query_log%';
   - 启动：使用set global slow_query_log=1开启了慢查询日志只对当前数据库生效，如果MySQL重启后则会失效。

3. long_query_time阈值

   - 查看long_query_time：SHOW VARIABLES LIKE 'long_query_time%';
   - 设置long_query_time：set global long_query_time=3;设置完成之后需要重开会话或使用SHOW GLOBAL VARIABLES LIKE 'long_query_time%';才能查看到修改后的值。

4. 查看mysqldumpslow的帮助信息

   s：是表示按照何种方式排序

   c：访问次数

   l：锁定时间

   r：返回记录

   t：查询时间

   al：平均锁定时间

   ar：平均返回记录数

   at：平均查询时间

   t：即为返回前面多少条的数据

   g：后边搭配一个正则匹配模式，大小写不敏感

## 四，Show Profile

1. Show Profile是什么

   是mysql提供可以用来分析当前会话中语句执行的资源消耗情况，可以用于SQL的调优的测量。默认情况下，参数处于关闭状态，并保存最近15次的运行结果。

2. 分析步骤

   - 查看当前MySQL版本是否支持

     SHOW VARIABLES LIKE 'profiling';

   - 开启功能

     SET profiling = ON;

   -  运行SQL

   - 查看结果

     SHOW PROFILES;

   - 诊断SQL

     SHOW PROFILE CPU, block IO FOR QUERY 上一步中的SQL编号;

     参数：

     - ALL-显示所有的开销信息
     - BLOCK IO-显示块IO相关开销
     - CONTEXT SWITCHES-上下文切换相关开销
     - CPU-显示CPU相关开销信息
     - IPC-显示发送和接收相关开销信息
     - MEMORY-显示内存相关开销信息
     - PAGE FAULTS一显示页面错误相关开销信息
     - SOURCE-显示和Source_function,Source_file,Source line相关的开销信息
     - SWAPS-显示交换次数相关开销的信息

   - 结论

     - converting HEAP to MyISAM查询结果太大，内存都不够用了往磁盘上搬了
     - Creating tmp table创建临时表
     - Copying to tmp table on disk把内存中临时表复制到磁盘
     - locked

## 五，全局查询日志

1. 开启全局查询日志
   - set global general_log=1;
   - set global log_output='TABLE';

# 第四章 MySQL锁机制

## 一，锁的分类

1. 从对数据操作的类型分

   - 读锁（共享锁）

     针对同一份数据，多个读操作可以同时进行而不会互相影响

   - 写锁（排它锁）

     当前操作没有完成前，他会阻断其他写锁和读锁

2. 从数据操作的力度分

   - 表锁

     偏向MyISAM存储引擎，开销小，加锁快；无死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低。

   - 行锁

     偏向InnoDB存储引擎，开销大，加锁慢：会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

## 二，添加锁和释放锁

1. 添加锁：lock table 表名 [write|read];
2. unlock tables;

## 三，案例分析

1. 读锁案例分析

   当一个session对一张表添加==读锁==，当前session可以查询该表记录，但是不能修改或插入数据，也不能查询其他没有锁定的表。其他session可以查询该表记录，可以查询或更新其他未锁定的表，但是对该锁定表修改或者插入数据==会一直等待获取锁==。

2. 写锁案例分析

   当一个session对一张表添加==写锁==，当前session可以对锁定的表查询，修改和插入数据。其他session对锁定的表查询被阻塞。

3. 总结

   读锁会阻塞写，但是不会阻塞读。写锁则会把读和写都阻塞。

4. 行锁案例分析

   当一个session更新一条数据没有提交，这时候其他session来更新同一条数据被阻塞，提交更新之后解除阻塞更新正常。但是其他session可以更新其他的行的数据。

## 四，间隙锁

1. 什么是间隙锁

   当我们用范围条件而不是相等条件检索数据，并请求共享锁或排他锁时，InnoDB会给符合条件的已有数据记录的索引项加锁；对于键值在条件范围内但并不存在的记录，叫做“间隙(GAP)”,InnoDB也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁(Next-Key锁)。

## 五，手动锁定一行数据

select ... for update;

其他会话的操作会被阻塞，只到锁定行的会话提交。

## 六，行锁优化建议

1. 尽可能让所有数据检索都通过索引来完成，避免无索引行锁升级为表锁。
2. 合理设计索引，尽量缩小锁的范围
3. 尽可能较少检索条件，避免间隙锁
4. 尽量控制事务大小，减少锁定资源量和时间长度
5. 尽可能低级别事务隔离

# 第五章 主从复制

## 一，MySQL复制过程

1. master将改变记录到二进制日志(binary log)。这些记录过程叫做二进制日志事件，binary log events
2. slave:将master的binary log events拷贝到它的中继日志(relay log)
3. slave重做中继日志中的事件，将改变应用到自己的数据库中。MySQL复制是异步的且串行化的

## 二，复制的基本原则

1. 每个slave只有一个master
2. 每个slave只能有一个唯一的服务器ID
3. 每个master可以有多个salve

## 三，配置

主从都配置在[mysqld]节点下，主从服务器MySQL版本保持一致。

1. 主机配置文件

   - 主服务器唯一ID==[必须]==

     server-id=1

   - 启用二进制日志==[必须]==

     log-bin=自己本地的路径/mysqlbin

   - 启用错误日志[可选]

     log-err=自己本地的路径/mysqlerr

   - 根目录[可选]

     basedir=自己本地的路径

   - 临时目录[可选]

     tmpdir=自己本地的路径

   - 数据目录[可选]

     datadir=自己本地的路径/data/

   - 主机，读写都可以

     read-only=0

   - 设置不要复制的数据库[可选]

     binlog-ignore-db=mysql

   - 设置需要复制的数据库[可选]

     binlog-do-db=需要复制的主数据库名字

2. 从机配置文件

   - 从机服务器ID==[必须]==

     server-id=2

   - 启用二进制日志[可选]

     log-bin=mysql-bin

3. 在主机上建立账户授权slave

    ```sql
    GRANT REPLICATION SLAVE ON *.* TO '账号' @ '从机数据库IP' IDENTIFIED BY '密码';
    ```

   查看主机状态

   show master status;

4. 在从机上配置

   ```sql 
   CHANGE MASTER TO MASTER_HOST='主机IP',
   MASTER_USER='账号',
   MASTER_PASSWORD='密码',
   MASTER_LOG_FILE='mysqlbin.具体数字',
   MASTER_L0G_P0S=具体值;
   ```

5. 启动从服务器复制功能

   start slave;

   查看从机状态

   show slave status;

6. 停止从机复制功能

   stop slave;
