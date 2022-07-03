# 第一章 Oracle简介

## 一，基本概念

1. Oracle数据库

   一个相关的操作系统文件（即存储在计算机硬盘上的文件)集合，这些文件组织在一起，成为一个逻辑整体，即为Oracle数据库。

2. Oracle实例

   位于物理内存里的数据结构，它由操作系统的多个后台进程和一个共享的内存池所组成，共享的内存池可以被所有进程访问。

3. 数据文件[dbf]

   数据文件是数据库的物理存储单位。数据库的数据是存储在表空间中的，真正是在某一个或者多个数据文件中，而一个表空间可以由一个或多个数据文件组成，一个数据文件只能属于一个表空间。一旦数据文件被加入到某个表空间后，就不能删除这个文件，如果要制除某个数据文件，只能删除其所属于的表空间才行。

4. 表空间

   表空间是Oracle对物理数据库上相关数据文件(ORA或者DBF文件)的逻辑映射。一个数据库在逻辑上被划分成一到若干个表空间，每个表空间包含了在逻辑上相关联的一组结构。每个数据库至少有一个表空间（称之为system表空间)。每个表空间由同一磁盘上的一个或多个文件组成，这些文件叫数据文件(datafile)。一个数据文件只能属于一个表空间。

5. 用户

   用户是在表空间下建立的。用户登陆后只能看到和操作自己的表，ORACLE的用户与MYSQL的数据库类似，每建立一个应用需要创建一个用户。

# 第二章 Oracle数据库管理

1. 创建表空间

   ```sql
   create tablespace waterboss
   datafile 'F:\Oracle\app\wgx\product\11.2.0\dbhome_1\cdata\localhost\waterboss.dbf'
   size 100m
   autoextend on
   next 10m
   ```

2. 创建用户

   ```sql
   create user wateruser
   identified by wgx
   default tablespace waterboss
   ```

3. 为用户赋予权限

   ```sql
   grant dba to wateruser
   ```

## 一，创建表

1. 数据类型

   - char：固定长度的字符类型，最多存储2000个字节

   - varchar2：可变长度的字符类型，最多存储4000个字节

   - long：大文本类型，最大可以存储2个G

   - number：数值类型

     number(5)：最大可以存的数为99999

     number(5, 2)：最大可以存的数为999.99

   - date：日期时间型，精确到秒

   - timestamp：精确到秒的小数点后9位

   - clob：存储字符最大可以存4个G

   - blob：存储图像、声音、视频等二进制数据，最多可以存4个G

2. 修改表

   - 增加字段

     alter table 表名 add(列名1 类型 [default 默认值], 列名2 类型 [default 默认值]...)

   - 修改字段

     alter table 表名 modify(列名1 类型 [default 默认值], 列名2 类型 [default 默认值]...)

   - 修改字段名

     alter table 表名 rename column 原列名 to 新列名

   - 删除字段

     alter table 表名 drop column 列名1, 列名2

   - 删除表

     drop table 表名

## 二，增删改

1. 增加

   insert into 表名(列名1, 列名2...) values(值1, 值2)

2. 修改

   update 表名 set 列名 = 值 where ...

3. 删除

   - delete from 表名 where ...
   - truncate table 表名
   - 两种删除对比
     - delete删除的数据可以rollback
     - delete删除可能产生碎片，并且不释放空间
     - truncate是先摧毁表结构，在重构表结构

# 第三章 JDBC连接Oracle

## 一，JDBC连接以及增删改查

1. 引入依赖

   ```xml
   <dependency>
       <groupId>com.oracle</groupId>
       <artifactId>ojdbc6</artifactId>
       <version>11.2.0.1.0</version>
   </dependency>
   ```

2. 编写BaseDao

   ```java
   public class BaseDao {
       static {
           try {
               //加载驱动
               Class.forName("oracle.jdbc.driver.OracleDriver");
           } catch (ClassNotFoundException e) {
               e.printStackTrace();
           }
       }
       //获取数据库连接
       public static Connection getConnection() throws SQLException {
           return DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:orcl", "wateruser", "wgx");
       }
       //关闭资源
       public static void close(ResultSet resultSet, Statement statement, Connection connection) {
           try {
               if (resultSet != null) {
                   resultSet.close();
               }
               if (statement != null) {
                   statement.close();
               }
               if (connection != null) {
                   connection.close();
               }
           } catch (SQLException e) {
               e.printStackTrace();
           }
       }
   }
   ```

3. 编写OwnersDao

   ```java
   public void add(Owners owners) {
       Connection connection = null;
       PreparedStatement statement = null;
       try {
           connection = BaseDao.getConnection();
           String sql = "insert into t_owners values(?, ?, ?, ?, ?, ?, ?)";
           statement = connection.prepareStatement(sql);
           statement.setLong(1, owners.getId());
           statement.setString(2, owners.getName());
           statement.setLong(3, owners.getAddress());
           statement.setString(4, owners.getHousenumber());
           statement.setString(5, owners.getWatermeter());
           statement.setDate(6, new Date(owners.getAdddate().getTime()));
           statement.setLong(7, owners.getOwnertypeid());
           statement.execute();
       } catch (SQLException e) {
           e.printStackTrace();
       } finally {
           BaseDao.close(null, statement, connection);
       }
   }
   ```

## 二，数据库的导入导出

### 整库的导入导出

1. 整库的导出命令

   exp system/wgx full=y

2. 导入

   - imp system/wgx full=y

   - 指定文件名

     exp system/wgx full=y file=XXX.dmp

### 按用户和表导入导出

1. 按用户导出

   exp system/wgx owner=XXX file=XXX.dmp

2. 按用户导入

   imp system/wgx file=XXX.dmp fromuser=XXX

3. 按表导入

   exp 用户名/密码 file=XXX.dmp tables=表名1, 表名2

4. 按表导出

   imp 用户名/密码 file=XXX.dmp tables=表名1, 表名2

# 第四章 Oracle对象

## 一，视图

1. 什么是视图

   视图是一种数据库对象，是从一个或者多个数据表或视图中导出的虚表，视图所对应的数据并不真正地存储在视图中，而是存储在所引用的数据表中，视图的结构和数据是对数据表进行查询的结果。

   根据创建视图时给定的条件，视图可以是一个数据表的一部分，也可以是多个基表的联合，它存储了要执行检索的查询语句的定义，以便在引用该视图时使用。

2. 使用视图的优点

   - 简化数据操作：视图可以简化用户处理数据的方式。
   - 着重于特定数据：不必要的数据或敏感数据可以不出现在视图中。
   - 视图提供了一个简单而有效的安全机制，可以定制不同用户对数据的访问权限。
   - 提供向后兼容性：视图使用户能够在表的架构更改时为表创建向后兼容接口。

3. 创建视图

   create [or replace|force] view 视图名 as 查询语句 [with check option|with read only]

   - or replace：若创建视图已经存在，Oracle自动重建该视图
   - force：不管基表是否存在Oracle都会自动创建该视图
   - with check option：插入或修改的数据必须满足视图定义的约束
   - with read only：该视图上不能进行任何DML操作

4. 删除视图

   drop view 视图名

==键保留表：把主键保留下来的那个表==

## 二，物化视图

1. 什么是物化视图

   视图是一个虚拟表（也可以认为是一条语句），基于它创建时指定的查询语句返回的结果集。每次访问它都会导致这个查询语句被执行一次。为了避免每次访问都执行这个查询，可以将这个查询结果集存储到一个物化视图（也叫实体化视图）。

   物化视图与普通的视图相比的区别是物化视图是建立的副本，它类似于一张表，需要占用存储空间。对一个物化视图查询的执行效率与查询一个表是一样的。

2. 创建物化视图

   ```sql
   create materialized view 视图名
   [build immediate|build deferred]
   refresh [fast|complete|force]
   [
       on [complete|demand] | start with (start_time) next 	(next_time)
   ]
   as
   查询语句
   ```

   - build immediate：在创建物化视图的时候就生成数据，默认

   - build deferred：在创建视图的时候不生成数据，以后根据需要在生成数据

   - refresh：指当基表发生了DML操作后，物化视图何时采用哪种方式和基表进行同步

     - fast：快速刷新（增量更新）

       需要创建物化视图日志：记录基表发生了那些变化

       create materialized view log on t_owners with rowid

     - complete：完全刷新

     - force：自动选择

     - on commit：在基表提交之后，自动刷新视图

     - on demand：手动刷新，默认

       刷新命令：exec dbms_mview.refresh('mv_owners', 'C');

## 三，序列

1. 创建

   create sequence 序列名称

2. 提取下一个值

   select 序列名称.nextval from dual

3. 提取当前值

   select 序列名称.currval from dual

4. 创建复杂序列

   ```sql
   create sequence 序列名称
   [increment by n]//递增的序列值是n，如果n是正数就递增，如果是负数就递减，默认是1
   [start with n]//开始的值为n
   [maxvalue n|nomaxvalue]//最大值
   [minvalue n|nominvalue]//最小值
   [cycle|nocycle]//循环、不循环
   [cache n|nocache]//分配并存入到内存中，默认值cache 20
   ```

5. 修改序列

   alter sequence 序列名称 需要修改的属性(start with不能修改)

6. 删除序列

   drop sequence 序列名称

## 四，同义词

1. 什么是同义词

   同义词实质上是指定方案对象的一个别名。通过屏蔽对象的名称和所有者以及对分布式数据库的远程对象提供位置透明性，同义词可以提供一定程度的安全性。同时，同义词的易用性较好，降低了数据库用户的SQL语句复杂度。同义词允许基对象重命名或者移动，这时，只需对同义词进行重定义，基于同义词的应用程序可以继续运行而无需修改。

   你可以创建公共同义词和私有同义词。其中，公共同义词属于PUBLIC特殊用户组，数据库的所有用户都能访问：而私有同义词包含在特定用户的方案中，只允许特定用户或者有基对象访问权限的用户进行访问。

2. 创建

   create [public] synonym 同义词名称 for object

   **object表示要表示表、视图、序列等对象的名称**

## 五，索引

1. 什么是索引

   索引是用于加速数据存取的数据对象。合理的使用索引可以大大降低I/O次数从而提高数据访问性能。

   索引是需要占据存储空间的，也可以理解为是一种特殊的数据。形式类似于一棵“树”，而树的节点存储的就是每条记录的物理地址，也就是我们提到的伪列(ROWID)

2. 普通索引

   create index 索引名称 on 表名(列名)

3. 唯一索引

   create unique index 索引名称 on 表名(列名)

4. 复合索引

   create index 索引名称 on 表名(列名1, 列名2...)

## 六，反向键索引

1. 应用场景

   当某个字段的值为连续增长的值，如果构建标准索引，会形成歪脖子树。这样会增加查询的层数，性能会下降。建立反向键索引，可以使索引的值变得不规则，从而使索引树能够均匀分布。

2. create index 索引名称 on 表名(列名) reverse

## 七，位图索引

1. 应用场景

   位图索引适合创建在低基数列上

   位图索引不直接存储ROWID，而是存储字节到ROWID的映射

2. create bitmap index 索引名称 on 表名(列名)

# 第五章 Oracle编程

## 一，PL/SQL

1. 什么是PL/SQL

   PL/SQL(Procedure Language/SQL)是Oracle对SQL语言的过程化扩展，指在SQL命令语言中增加了过程处理语句（如分支、循环等），使SQL语言具有过程处理能力。把SQL语言的数据操纵能力与过程语言的数据处理能力结合起来，使得PLSQL面向过程但比过程语言简单、高效、灵活和实用。

2. 基本语法结构

   ```plsql
   declare
   --声明变量
   begin
   --代码逻辑
   exception
   --异常处理
   end;
   ```

### 变量

1. 声明变量

   变量名 类型(长度);

2. 变量赋值

   变量名 := 变量值

3. select into赋值

   select 列名 into 变量名

4. 属性类型

   - 引用型：变量名 表名.列名%type

     ```plsql
     v_name t_owners.name%type;
     ```

   - 记录型：变量名 表名.列名%rowtype

     ```plsql
     v_owners t_owners%rowtype;
     ```


### 异常

1. 在运行程序时出现的错误叫做异常

   发生异常后，语句将停止执行，控制权转移到PL/SQL块的异常处理部分异常有两种类型：

   - 预定义异常：当PL/SQL程序违反Oracle规则或超越系统限制时隐式引发
   - 用户定义异常：用户可以在PL/SQL块的声明部分定义异常，自定义的异常通过RAISE语句显式引发

2. 语法结构

   ```plsql
   exception
   	when 异常类型 then
   	异常处理逻辑
   ```

### 条件判断

1. 基本语法

   - ```plsql
     if 条件 then
     	业务逻辑
     end if;
     ```

   - ```plsql
     if 条件 then
     	业务逻辑
     else
     	业务逻辑
     end if;
     ```

   - ```plsql
     if 条件 then
     	业务逻辑
     elseif 条件 then
     	业务逻辑
     else
     	业务逻辑
     end if;
     ```

### 循环

1. 无条件循环

   ```plsql
   loop
   	循环语句
   	--使用exit或者exit when退出
   end loop;
   ```

2. 条件循环

   ```plsql
   while 条件
   loop
   	循环语句
   end loop;
   ```

3. for循环

   ```plsql
   for 变量 in 起始值 .. 终止值
   loop
   	--循环语句
   end loop;
   ```

### 游标

1. 什么是游标

   游标是系统为用户开设的一个数据缓冲区，存放SQL语句的执行结果。我们可以把游标理解为PLSQL中的结果集。

2. 语法结构

   - 声明游标

     cursor 游标名称 is SQL语句;

   - 使用游标

     ```plsql
     open 游标名称;
     loop
     	fetch 游标名称 into 变量;
     	exit when 游标名称%notfound;
     end loop;
     close 游标名称
     ```

   - 带参数的游标

     ```plsql
     cursor 游标名称(参数) is SQL语句[可以使用参数];
     
     ...
     
     open 游标名称(参数);
     loop
     	fetch 游标名称 into 变量;
     	exit when 游标名称%notfound;
     end loop;
     close 游标名称
     ```

   - for循环带参数的游标

     ```plsql
     cursor 游标名称(参数) is SQL语句[可以使用参数];
     
     ...
     
     for 变量 in 游标名称(参数);
     loop
     	--使用变量
     end loop;
     ```

## 二，存储函数

1. 什么是存储函数

   存储函数又称为自定义函数。可以接收一个或多个参数，返回一个结果。在函数中我们可以使用PL/SQL进行逻辑的处理。

2. 语法结构

   ```plsql
   create [or replace] function 函数名称(参数名称 参数类型, ...)
   return 结果变量数据类型
   is
   	变量声明部分;
   begin
   	逻辑部分;
   	return 结果变量;
   exception
   	异常处理部分;
   end;
   ```

## 三，存储过程

1. 什么是存储过程

   存储过程是被命名的PL/SQL块，存储于数据库中，是数据库对象的一种。应用程序可以调用存储过程，执行相应的逻辑。

   存储过程与存储函数都可以封装一定的业务逻辑并返回结果，存在区别如下：

   - 存储函数中有返回值，且必须返回；而存储过程没有返回值，可以通过传出参数返回多个值。
   - 存储函数可以在select语句中直接使用，而存储过程不能。过程多数是被应用程序所调用。
   - 存储函数一般都是封装一个查询结果，而存储过程一般都封装一段事务代码。

2. 语法结构

   ```plsql
   create [or replace] procedure 存储过程名称(参数名 参数类型, ...)
   is|as
   	变量声明部分;
   begin
   	逻辑部分;
   exception
   	异常处理部分;
   end;
   ```

   过程参数的三种模式：

   - in 传入参数(默认)
   - out 传出参数，主要用于返回程序运行结果
   - in out 传入传出参数

3. JDBC调用存储过程

   ```java
   public long addByUseProcedure(Owners owners) {
       Connection connection = null;
       CallableStatement statement = null;
       try {
           connection = BaseDao.getConnection();
           statement = connection.prepareCall("{call pro_insertowners(?, ?, ?, ?, ?, ?, ?)}");
           statement.setString(1, owners.getName());
           statement.setLong(2, owners.getAddressid());
           statement.setString(3, owners.getHousenumber());
           statement.setString(4, owners.getWatermeter());
           statement.setDate(5, new Date(owners.getAdddate().getTime()));
           statement.setLong(6, owners.getOwnertypeid());
           //注册传出参数的类型
           statement.registerOutParameter(7, OracleTypes.NUMBER);
           statement.execute();
           return statement.getLong(7);
       } catch (SQLException e) {
           e.printStackTrace();
       } finally {
           BaseDao.close(null, statement, connection);
       }
       return 0;
   }
   ```

## 四，触发器

1. 什么是触发器

   数据库触发器是一个与表相关联的、存储的PL/SQL程序。每当一个特定的数据操作语句Insert,update,delete)在指定的表上发出时，Oracle自动地执行触发器中定义的语句序列。

2. 应用场景

   - 数据确认
   - 实施复杂的安全性检查
   - 做审计，跟踪表上所做的数据操作等
   - 数据的备份和同步

3. 触发器分类

   - 前置触发器（before）
   - 后置触发器（after）

4. 创建触发器语法

   ```plsql
   create or replace trigger 触发器名称
   before | after
   [delete] [or insert] [or update [of 列名]]
   on 表名
   [for each row] [when(条件)]
   declare
   begin
   end;
   ```

5. 在触发器中触发语句与==伪记录变量==的值

   | 触发语句 | :old             | :new           |
   | -------- | ---------------- | -------------- |
   | insert   | 所有字段为空     | 将要插入的数据 |
   | update   | 更新以前该行的值 | 更新后的值     |
   | delete   | 删除以前该行的值 | 所有字段为空   |

   
