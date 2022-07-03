# 第一章 MyBatis-Plus介绍

## 一，特性

- **无侵入**：只做增强不做改变，引入MyBatis-Plus不会对现有工程产生影响
- **损耗小**：启动即会自动注入基本的CRUD，性能基本无损耗，直接面向对象操作
- **强大的CRUD操作**：内置通用Mapper、通用Service，仅仅通过少量配置即可实现单表大部分CRUD操作
- **支持Lambda形式调用**：通过Lambda表达式，方便的编写各类查询条件，无需担心字段写错
- **支持多种数据库**：支持MySQL、MarlaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer2005、SQLServer等多种数据库
- **支持主键自动生成**：支持多达4种主键策略（内含分布式唯一ID生成器-Sequence），可自由配置，完美解决主键问题
- **支持XML热加载**：Mapper对应的XML支持热加载，对于简单的CRUD操作，甚至可以无XML启动
- **支持ActiveRecord模式**：支持ActiveRecord形式调用，实体类只需继承Model类即可进行强大的CRUD操作
- **支持自定义全局通用操作**：支持全局通用方法注入（Write once，use anywhere）
- **支持关键词自动转义**：支持数据库关键词（order、key...）自动转义，还可以自定义关键词
- **内置代码生成器**：采用代码或者Maven插件可快速生成Mapper、Model、Service、Controller层代码，支持模板引擎，更多自定义配置
- **内置分页插件**：基于MyBatis物理分页，开发者无需关心具体操作，配置好插件之后，写分页等于普通的List查询
- **内置性能分析插件**：可以输出SQL语句以及其执行时间，建议开发测试时启用该功能，能快速查出慢查询
- **内置全局拦截插件**：提供全表delete、update操作智能分析阻断，也可以自定义拦截规则，预防误操作
- **内置SQL注入剥离器**：支持SQL注入剥离，有效预防SQL注入攻击

# 第二章 快速开始

## 一，创建工程

1. 引入依赖

   ```xml
   <!--mybatis-plus-->
   <dependency>
       <groupId>com.baomidou</groupId>
       <artifactId>mybatis-plus</artifactId>
       <version>3.5.1</version>
   </dependency>
   
   <!--mysql-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.29</version>
   </dependency>
   
   <!--druid-->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid</artifactId>
       <version>1.2.8</version>
   </dependency>
   ```

2. MyBatis与MyBatis-Plus的整合

   ```java
   @NoArgsConstructor
   @AllArgsConstructor
   @Data
   @TableName("user")//对应的表名
   public class User {
       private Integer id;
       private String username;
       private String password;
       private String name;
       private Integer age;
       private String email;
   }
   ```

   ```java
   //继承BaseMapper
   public interface UserMapper extends BaseMapper<User> {
   }
   ```

   ```java
   //test
   @Slf4j
   public class Test {
       @org.junit.Test
       public void test() throws IOException {
           String config = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(config);
           SqlSessionFactory sqlSessionFactory = new MybatisSqlSessionFactoryBuilder().build(inputStream);
           SqlSession openSession = sqlSessionFactory.openSession();
           UserMapper mapper = openSession.getMapper(UserMapper.class);
           List<User> users = mapper.selectList(null);
           for (User user : users) {
               log.info(user.toString());
           }
       }
   }
   ```

3. Spring+Mybatis+Mybatis-Plus

   - 引入依赖

     ```xml
     <dependencies>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-jdbc</artifactId>
             <version>5.3.14</version>
         </dependency>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-webmvc</artifactId>
             <version>5.3.14</version>
         </dependency>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-test</artifactId>
             <version>5.3.15</version>
         </dependency>
     </dependencies>
     ```

   - ```xml
     <!--使用Mybatis-Plus提供的MybatisSqlSessionFactoryBean,完成与spring的整合-->
     <bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
         <property name="dataSource" ref="dataSource"/>
     </bean>
     ```

## 二，通用的CRUD

```java
    @TableId(type = IdType.AUTO)//让id字段自用自增长策略
    private Integer id;
```

## 三，@TableField注解

在MP中通过@TableField注解可以指定字段的一些属性，常常解决两个问题

1. 对象中的属性名和字段名不一致的问题（非驼峰）
2. 对象中的属性字段在表中不存在问题

```java
@NoArgsConstructor
@AllArgsConstructor
@Data
@TableName("user")
public class User {
    @TableId(type = IdType.AUTO)//让id字段自用自增长策略
    private Integer id;
    private String username;
    @TableField(select = false)//查询时不查询
    private String password;
    private String name;
    private Integer age;
    @TableField("email")//在表中对应的字段名
    private String mail;
    @TableField(exist = false)//在表中不存在
    private String address;
}
```

## 四，通用CRUD详解

### update

- ```java
  //根据id更新
  @org.junit.Test
  public void test() {
      User user = new User();
      user.setId(1);
      user.setPassword("111111");
      userMapper.updateById(user);
  }
  ```

- ```java
  //根据QueryWrapper更新
  @org.junit.Test
  public void test() {
      User user = new User();
      user.setAge(23);//需要更新的字段
      user.setPassword("111111");//需要更新的字段
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.eq("username", "wgx")//封装查询条件，字段username为wgx
      userMapper.update(user, wrapper);
  }
  ```

- ```java
  //根据UpdateWrapper更新
  @org.junit.Test
  public void test() {
      UpdateWrapper<User> wrapper = new UpdateWrapper<>();
      wrapper.set("password", "123456").set("age", 22)//需要更新的字段
              .eq("username", "wgx");//条件，字段username为wgx
      userMapper.update(null, wrapper);
  }
  ```

### delete

- ```java
  //根据id删除
  @org.junit.Test
  public void test() {
      userMapper.deleteById(5);
  }
  ```

- ```java
  //根据map删除
  @org.junit.Test
  public void test() {
      HashMap<String, Object> map = new HashMap<>();
      map.put("username", "wgx");
      map.put("password", "123456");
      userMapper.deleteByMap(map);//map里的条件and连接
  }
  ```

- ```java
  //根据QueryWrapper删除
  //用法一(推荐)
  @org.junit.Test
      public void test() {
      User user = new User();
      user.setUsername("wgx");
      user.setPassword("123456");
      QueryWrapper<User> wrapper = new QueryWrapper<>(user);
      userMapper.delete(wrapper);
  }
  //用法二
  @org.junit.Test
      public void test() {
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.eq("username", "wgx").eq("password", "123456");
      userMapper.delete(wrapper);
  }
  ```

- ```java
  //根据id批量删除
  @org.junit.Test
      public void test() {
      userMapper.deleteBatchIds(Arrays.asList(2, 3));
  }
  ```

### select

- ```java
  //根据id批量查询
  @org.junit.Test
  public void test() {
      userMapper.selectBatchIds(Arrays.asList(8, 9));
  }
  ```

- ```java
  //selectOne
  @org.junit.Test
  public void test() {
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.eq("username", "wgx");
      userMapper.selectOne(wrapper);//只查询一条数据，查出多条数据会报错
  }
  ```

- ```java
  //selectCount查询数量
  @org.junit.Test
  public void test() {
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.ge("age", 30);//封装条件，age>30
      Integer count = userMapper.selectCount(wrapper);
      System.out.println(count);
  }
  ```

- ```java
  //selectList查询列表
  @org.junit.Test
  public void test() {
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.ge("age", 30);//封装条件，age>30
      List<User> users = userMapper.selectList(wrapper);
      for (User user : users) {
          System.out.println(user);
      }
  }
  ```

- 分页查询

  ```xml
  <bean id="paginationInterceptor" class="com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor">
  </bean>
  
  
  <!--使用Mybatis-Plus提供的MybatisSqlSessionFactoryBean,完成与spring的整合-->
  <bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource"/>
      <property name="plugins" ref="paginationInterceptor"/>
  </bean>
  ```

  ```java
  @org.junit.Test
  public void test() {
      Page<User> page = new Page<>(1, 2);//当前第一页，每页2条数据
      IPage<User> iPage = userMapper.selectPage(page, null);
      System.out.println("当前页面" + iPage.getCurrent());
      System.out.println("总页数" + iPage.getPages());
      System.out.println("总记录数" + iPage.getTotal());
      //遍历总记录数
      List<User> users = iPage.getRecords();
      for (User user : users) {
          System.out.println(user);
      }
  }
  ```


# 第三章 配置

## 一，基本配置

### configLocation

```xml
<property name="configLocation" value="classpath:mybatis-config.xml"/>
```

### mapperLocation

```xml
<property name="mapperLocations" value="classpath*:mybatis/*.xml"/>
```

### typeAliasesPackage

```xml
<!--别名包扫描路径-->
<property name="typeAliasesPackage" value="com.wgx.mybatis_plus.bean"/>
```

## 二，DB策略配置

```xml
<!--全局idType策略-->
<property name="globalConfig">
    <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig">
        <property name="dbConfig">
            <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig$DbConfig">
                <property name="idType" value="AUTO"/>
                <!--表名前缀-->
                <property name="tablePrefix" value="tb_"/>
            </bean>
        </property>
    </bean>
</property>
```

# 第三章 条件构造器

## 一，allEq

```java
@org.junit.Test
public void test02() {
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    HashMap<String, Object> map = new HashMap<>();
    map.put("name", "wgx");
    map.put("age", 22);
    map.put("password", null);
    //SELECT id,username,name,age,email FROM user WHERE password IS NULL AND name = ? AND age = ?
    wrapper.allEq(map);
    List<User> users = userMapper.selectList(wrapper);
}
```

```java
@org.junit.Test
public void test02() {
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    HashMap<String, Object> map = new HashMap<>();
    map.put("name", "wgx");
    map.put("age", 22);
    map.put("password", null);
    //SELECT id,username,name,age,email FROM user WHERE name = ? AND age = ?  null不作为查询条件
    wrapper.allEq(map, false);
    List<User> users = userMapper.selectList(wrapper);
}
```

```java
@org.junit.Test
public void test02() {
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    HashMap<String, Object> map = new HashMap<>();
    map.put("name", "wgx");
    map.put("age", 22);
    map.put("password", null);
    //SELECT id,username,name,age,email FROM user WHERE name = ? AND age = ?  根据filter选择条件
    wrapper.allEq((k, v) -> (k.equals("name") || k.equals("age")), map);
    List<User> users = userMapper.selectList(wrapper);
}
```

## 二，模糊查询

- like：LIKE %值%
- notLike：NOT LIKE %值%
- likeLeft：LIKE %值
- likeRight：LIKE 值%

## 三，排序

-  orderBy(true, true, "id", "name")：ORDER BY id ASC， name ASC
- orderByAsc：正序排序
- orderByDesc：倒序排序

## 四，select

select()：指定查询的字段

# 第四章 ActiveRecord

## 一，什么是ActiveRecord

ActiveRecord也属于ORM（对象关系映射）层

遵循标准的ORM模型：表映射到记录，记录映射到对象，字段映射到对象属性，配合遵循的命名和配置惯例，能够很大程度的快速实现模型的操作，而且简洁易懂

ActiveRecord的主要思想：

- 每一个数据库对象创建一个类，类的每一个对象实例对应于数据库中表的一行记录；通常表中的每个字段在类中都有对应的Field
- ActiveRecord同时负责把自己持久化，在ActiveRecord中封装了对数据库的访问，即CRUD
- ActiveRecord是一中领域模型（Domain Model），封装了部分业务逻辑

## 二，使用ActiveRecord

1. 让bean对象继承Model<T\>

   ```java
   public class User extends Model<User> {
   }
   ```

2. 测试selectById

   ```java
   @org.junit.Test
   public void test03() {
       User user = new User();
       user.setId(4);
       User selectById = user.selectById();
       System.out.println(selectById);
   }
   ```

# 第五章 MyBatis-Plus插件

## 一，SQL分析插件

```xml
<!--阻止进行全表更新和全表删除-->
<bean id="sqlExplainInterceptor" class="com.baomidou.mybatisplus.extension.plugins.SqlExplainInterceptor">
    <property name="sqlParserList">
        <bean class="com.baomidou.mybatisplus.extension.parsers.BlockAttackSqlParser"/>
    </property>
</bean>
```

## 二，性能分析插件

```xml
<!--性能分析插件-->
<bean id="performanceInterceptor" class="com.baomidou.mybatisplus.extension.plugins.PerformanceInterceptor">
    <!--SQL语句的最大执行时间，单位毫秒-->
    <property name="maxTime" value="100"/>
    <!--开启格式化SQL语句-->
    <property name="format" value="true"/>
</bean>
```

![](F:\截图\屏幕截图 2022-02-25 204030.png)

## 三，乐观锁插件

主要用途：当要更新一条记录的时候，希望这条记录没有被别人同时更新

实现方式：

- 取出记录时，获取当前version
- 更新时带上这个version
- 执行更新时，set version = newVersion where version = oldVersion
- 如果version不对，就更新失败

使用：

- ```xml
  <!--乐观锁插件-->
  <bean id="optimisticLockerInterceptor" class="com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor"/>
  ```

- 在数据库表中和bean对象中添加version字段

  ```java
  @Version
  private Integer version;
  ```

  ```sql
  ALTER TABLE `user` ADD COLUMN `version` INT(11) AFTER email
  UPDATE `user` SET `version` = 1
  ```

  ![](F:\截图\屏幕截图 2022-02-25 210020.png)

- 乐观锁特别说明
  1. 支持的数据类型只有int、Integer、long、Long、Date、TimeStamp、LocalDateTime
  2. 整数类型下newVersion = oldVersion + 1
  3. newVersion会回写到bean中
  4. 仅支持updateById(id)与update(entity, wrapper)方法
  5. 在update(entity, wrapper)方法下，wrapper不能复用

## 四，SQL注入器

1. ```java
   //编写一个自己的BaseMapper接口，以后的其他mapper都继承这个BaseMapper
   public interface MyBaseMapper<T> extends BaseMapper<T> {
       List<T> findAll();
   }
   ```

2. 创建自己的方法类，继承AbstractMethod

   ```java
   public class FindAll extends AbstractMethod {
       @Override
       public MappedStatement injectMappedStatement(Class<?> mapperClass, Class<?> modelClass, TableInfo tableInfo) {
           //sql语句
           String sql = "select * from " + tableInfo.getTableName();
           //创建SqlSource
           SqlSource sqlSource = this.languageDriver.createSqlSource(this.configuration, sql, modelClass);
           //返回MappedStatement
           return this.addSelectMappedStatement(mapperClass, "findAll", sqlSource, modelClass, tableInfo);
       }
   }
   ```

3. 将自己的方法添加到自己的SQL注入器

   ```java
   public class MyInjector extends DefaultSqlInjector {
       @Override
       public List<AbstractMethod> getMethodList() {
           List<AbstractMethod> list = new ArrayList<>();
           //将父类中的方法加入进来
           list.addAll(super.getMethodList());
           list.add(new FindAll());
           return list;
       }
   }
   ```

4. 将自己的SQL注入器加入到spring容器中

## 五，自动填充

1. 添加注解

```java
@TableField(select = false, fill = FieldFill.INSERT)//查询时不查询,在插入数据的时候自动填充
private String password;
```

2. 编写MyMetaObjectHandler

```java
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        //在插入时password为null，自动填充默认密码，不为null，则插入数据
        Object password = getFieldValByName("password", metaObject);
        if (password == null) {
            setFieldValByName("password", "123456", metaObject);
        }
    }
}
```

3. 将MyMetaObjectHandler加入到spring容器中

## 六，逻辑删除

1. 在表中添加deleted列

   ```sql
   ALTER TABLE `user` ADD COLUMN `deleted` INT(11) AFTER `version`
   ```

2. 在bean中添加相应的字段，并添加注解

   ```java
   @TableLogic//逻辑删除字段，0-未删除，1-已删除
   private Integer deleted;
   ```

3. 配置

   ```xml
   <property name="globalConfig">
       <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig">
           <property name="dbConfig">
               <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig$DbConfig">
                   <property name="idType" value="AUTO"/>
                   <property name="logicDeleteValue" value="1"/><!--1表示已删除-->
                   <property name="logicNotDeleteValue" value="0"/><!--0表示未删除-->
                   <!--<property name="tablePrefix" value="tb_"/>-->
               </bean>
           </property>
       </bean>
   </property>
   ```

4. 执行的删除SQL语句

   ![](F:\截图\屏幕截图 2022-02-26 150738.png)

5. 删除后查询该数据，执行的SQL语句

   ![](F:\截图\屏幕截图 2022-02-26 150915.png)

## 七，通用枚举

1. 修改表结构

   ```sql
   ALTER TABLE `user` ADD COLUMN `gender` INT(11) AFTER `age`
   ```

2. 在bean中添加对应的字段

   ```java
   //1-MAN,2-WOMAN
   private Integer gender;
   ```

3. 定义枚举类

   ```java
   public enum Gender implements IEnum<Integer> {
       MAN(1, "男"), WOMAN(2, "女");
   
       private Integer value;
       private String desc;
   
       Gender(Integer value, String desc) {
           this.value = value;
           this.desc = desc;
       }
   
       @Override
       public Integer getValue() {
           return this.value;
       }
       
       @Override
       public String toString() {
           return this.desc;
       }
   }
   ```

4. 配置

   ```xml
   <!--配置枚举包扫描路径-->
   <property name="typeEnumsPackage" value="com.wgx.mybatis_plus.enums"/>
   ```

