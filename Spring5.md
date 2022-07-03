#  第一章 Spring框架概述

## 一，框架概述

1. Spring是轻量级的开源的JavaEE框架
2. Spring可以解决企业应用开发的复杂性
3. Spring有两个核心部分，IOC和AOP
   - IOC：控制反转，把创建对象的过程交给Spring框架进行管理
   - AOP：面向切面，不修改源代码进行功能增强
4. Spring的特点
   - 方便解耦，简化开发
   - AOP编程支持
   - 方便程序测试
   - 方便和其他框架进行整合
   - 方便进行事务操作
   - 降低API开发难度

## 二，入门案例

1. 配置Spring配置文件

   ```xml
   <!--配置User对象创建-->
       <bean id="user" class="com.wgx.spring5.User"></bean>
   ```

2. 创建对象

   ```java
   @Test
   public void testAdd(){
       //1. 加载Spring配置文件
       ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
       //2. 获取配置创建的对象
       User user = context.getBean("user", User.class);
       System.out.println(user);
       user.add();
   }
   ```

# 第二章 IOC

## 一，IOC概念和原理

1. 什么是IOC

   - 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理
   - 使用IOC目的：为了耦合度降低

2. IOC底层原理

   - XML解析、工程模式、反射
   - ![](F:\截图\捕获141.PNG)
   - IOC思想基于IOC完成，IOC容器底层就是对象工厂

3. Spring提供IOC容器实现两种方式（两个接口）

   - BeanFactory：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用，加载配置文件是不会创建对象，在获取对象（使用）才会去创建对象
   - ApplicationContext：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用，加载配置文件的时候就会把在配置文件对象进行创建

4. ApplicationContext接口的实现类

   ![](F:\截图\捕获142.PNG)

## 二，IOC操作Bean管理

1. 什么是Bean管理

   - Bean管理指的是两个操作

     Spring创建对象

     Spring注入属性

2. Bean管理操作有两种方式

   - 基于xml配置文件方式实现

     1. 基于xml方式创建对象

        ```xml
        <!--配置User对象创建-->
            <bean id="user" class="com.wgx.spring5.User"></bean>
        <!--在Spring配置文件中使用bean标签，标签里面添加对应属性，就可以实现对象创建-->
        <!--id属性:唯一标识
        class属性:类全路径-->
        <!--创建对象时候，默认也是执行无参构造方法完成对象创建-->
        ```

     2. 基于xml方式注入属性 

        ```xml
        <!--使用set方法注入-->
        <!--配置User对象创建-->
        <bean id="user" class="com.wgx.spring5.User">
            <!--使用property完成属性注入
                name:类里面属性名称
                value:向属性注入的值-->
            <property name="name" value="wgx"></property>
            <property name="age" value="22"></property>
        </bean>
        ```

        ```xml
        <!--使用有参构造器注入属性-->
        <bean id="user" class="com.wgx.spring5.User">
            <constructor-arg name="name" value="wgx"></constructor-arg>
            <constructor-arg name="age" value="22"></constructor-arg>
        </bean>
        ```

        ```xml
        <!--设置null值-->
        <bean id="user" class="com.wgx.spring5.User">
            <property name="name">
                <null/>
            </property>
        </bean>
        ```

        ```xml
        <!--设置特殊符号-->
        <!--1. 使用转义，&lt; &gt;
            2. 把特殊符号内容写到CDATA里面-->
        <bean id="user" class="com.wgx.spring5.User">
            <property name="name">
                <value><![CDATA[<>]]></value>
            </property>
        </bean>
        ```

        ```xml
        <!--注入外部bean-->
        <!--service和dao对象创建-->
        <bean id="userService" class="com.wgx.spring5.service.impl.UserServiceImpl">
            <!--注入userDao属性
                    name属性:类里面属性名称
                    ref属性:创建userDao对象bean标签的id值-->
            <property name="userDao" ref="userDao"></property>
        </bean>
        <bean id="userDao" class="com.wgx.spring5.dao.impl.UserDaoImpl"></bean>
        ```

        ```xml
        <!--注入内部bean-->
        <bean id="userService" class="com.wgx.spring5.service.impl.UserServiceImpl">
            <property name="userDao">
                <bean id="userDao" class="com.wgx.spring5.dao.impl.UserDaoImpl"></bean>
            </property>
        </bean>
        ```
        
        ```xml
        <!--级联赋值(第一种方式)-->
        <bean id="employee" class="com.wgx.spring5.bean.Employee">
            <property name="ename" value="wgx"></property>
            <property name="age" value="22"></property>
            <property name="dept" ref="dept"></property>
        </bean>
        <bean id="dept" class="com.wgx.spring5.bean.Dept">
            <property name="dname" value="技术部"></property>
        </bean>
        <!--级联赋值(第二种方式)-->
        <bean id="employee" class="com.wgx.spring5.bean.Employee">
            <property name="ename" value="wgx"></property>
            <property name="age" value="22"></property>
            <property name="dept">
                <bean id="dept" class="com.wgx.spring5.bean.Dept">
                    <property name="dname" value="技术部"></property>
                </bean>
            </property>
        </bean>
        ```
        
        ```xml
        <!--集合类型属性注入-->
        <bean id="student" class="com.wgx.spring5.bean.Student">
            <!--数组类型注入-->
            <property name="array">
                <array>
                    <value>数学</value>
                    <value>英语</value>
                </array>
            </property>
            <!--List类型注入-->
            <property name="list">
                <list>
                    <value>list1</value>
                    <value>list2</value>
                </list>
            </property>
            <!--Set类型注入-->
            <property name="set">
                <set>
                    <value>set1</value>
                    <value>set2</value>
                </set>
            </property>
            <!--Map类型注入-->
            <property name="map">
                <map>
                    <entry key="key1" value="value1"></entry>
                    <entry key="key2" value="value2"></entry>
                </map>
            </property>
        </bean>
        ```
        
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:util="http://www.springframework.org/schema/util"
               xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                   http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
        
            <!--提取list集合类型属性注入-->
            <util:list id="bookList">
                <value>数学</value>
                <value>英语</value>
                <value>语文</value>
            </util:list>
            <!--提取list集合类型属性注入使用-->
            <bean id="book" class="com.wgx.spring5.bean.Book">
                <property name="name" ref="bookList"></property>
            </bean>
        ```
        
        ```xml
        <!--自动装配-->
        <!--bean标签属性autowire，配置自动装配
            autowire属性常用两个值:
                byName根据属性名注入，注入值bean的id和类属性名一样
                byType根据类型注入-->
        <bean id="employee" class="com.wgx.spring5.bean.Employee" autowire="byName"></bean>
        <bean id="dept" class="com.wgx.spring5.bean.Dept"></bean>
        ```
        
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:util="http://www.springframework.org/schema/util"
               xmlns:context="http://www.springframework.org/schema/context"
               xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                   http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
                                   http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
            <!--引入外部属性文件-->
            <context:property-placeholder location="classpath:druid.properties"/>
            <!--配置连接池-->
            <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
                <property name="driverClassName" value="${prop.driverClassName}"></property>
                <property name="url" value="${prop.url}"></property>
                <property name="username" value="${prop.username}"></property>
                <property name="password" value="${prop.password}"></property>
            </bean>
        ```
   
   - 基于注解方式实现
   
     1. 什么是注解
   
        注解是代码特殊标记，格式：@注解名称(属性名称=属性值,属性名称=属性值...)
   
        注解作用在类，方法和属性上面
   
        使用注解简化xml配置
   
     2. Spring针对Bean管理中创建对象提供注解
   
        - @Component
        - @Service
        - @Controller
        - @Repository
   
     3. 基于注解方式实现对象创建
   
        - 导入jar包
   
        - 开启组件扫描
   
          ```xml
          <!--开启组件扫描
              如果扫描多个包，多个包使用逗号隔开
              或扫描上层目录-->
          <context:component-scan base-package="com.wgx.spring5"></context:component-scan>
          ```
   
        - 创建类，在类上面添加创建对象注解
   
          ```java
          @Component(value = "user")//<bean id="user" class="..."/>
          public class User {
          }
          ```
   
     4. 基于注解实现属性注入
   
        - @Autowired：根据属性类型进行注入
   
          第一步，在service和dao类添加创建对象注解
   
          第二步，在service类添加dao类型属性，在属性上面使用注解
   
          ```java
          @Service(value = "userService")
          public class UserServiceImpl implements UserService {
              @Autowired
              private UserDao userDao;
          }
          ```
   
        - @Qualifier：根据属性名称进行注入
   
          ```java
          @Service(value = "userService")
          public class UserServiceImpl implements UserService {
              @Autowired
              @Qualifier(value = "userDao")
              private UserDao userDao;
          }
          ```
   
        - @Resource：可以根据类型注入，可以根据名称注入
   
          ```java
          @Service(value = "userService")
          public class UserServiceImpl implements UserService {
              //@Resource
              @Resource(name = "userDao")
              private UserDao userDao;
          }
          ```
   
        - @Value：根据普通类型属性
   
          ```java
          @Service(value = "userService")
          public class UserServiceImpl implements UserService {
              @Value(value = "wgx")
              private String username;
          }
          ```
   
     5. 完全注解开发
   
        - 创建配置类，替代xml配置文件
   
          ```java
          @Configuration//作为配置类，代替xml配置文件
          @ComponentScan(basePackages = {"com.wgx.spring5"})
          public class SpringConfig {
          }
          ```
   
        - 编写测试类
   
          ```java
          @Test
          public void test(){
              //1. 加载配置类
              ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
              //2. 获取配置创建对象
              UserService userService = context.getBean("userService", UserService.class);
              userService.add();
          }
          ```
   
3. FactoryBean

   - Spring有两种bean，一种是普通bean，一种是工厂bean（FactoryBean）

   - 普通bean：在配置文件中定义bean类型就是返回类型

   - 工厂bean：在配置文件中定义bean类型可以和返回类型不一样

     第一步，创建类，让这个类作为工厂bean，实现接口FactoryBean

     第二步，实现接口里面的方法，在实现的方法中定义返回的bean类型

4. bean的作用域

   - 在Spring里面，默认情况下bean是单实例对象

   - scope属性可以设置单实例还是多实例

     singleton：表示单实例（默认值）

     prototype：表示多实例

   - singleton和prototype的区别

     设置scope为singleton时，加载spring配置文件的时候就会创建单实例对象

     设置scope为prototype时，在调用getBean方法的时候创建多实例对象

5. ==bean的生命周期==

   - 通过构造器创建bean实例（无参数构造器）
   - 为bean的属性设置值和对比其他bean引用（调用set方法）
   - 调用bean的初始化方法（需要进行配置初始化的方法）
   - bean可以使用（对象获取到了）
   - 当容器关闭时，调用bean的销毁方法（需要进行配置销毁的方法）

6. bean的后置处理器

   - 通过构造器创建bean实例（无参数构造器）
   - 为bean的属性设置值和对比其他bean引用（调用set方法）
   - ==*把bean实例传递bean后置处理器的方法*==（postProcessBeforeInitialization）
   - 调用bean的初始化方法（需要进行配置初始化的方法）
   - ==*把bean实例传递bean后置处理器的方法*==（postProcessAfterInitialization）
   - bean可以使用（对象获取到了）
   - 当容器关闭时，调用bean的销毁方法（需要进行配置销毁的方法）

# 第三章 AOP

## 一，基本概念和原理

1. 什么是AOP

   面向切面（方面）编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑个部分之间的耦合度降低，提高程序的可重用性，同时提高了开发效率

2. AOP底层原理

   - AOP底层使用动态代理

   - 有两种情况动态代理

     - 第一种 有接口情况，使用JDK动态代理

       创建接口实现类代理对象，增强类的方法

       ```java
       public class JDKProxy {
           public static void main(String[] args) {
               //创建接口实现类代理对象
               Class[] interfaces = {UserDao.class};
               /*
               调用newProxyInstance方法
               Params: loader – 类加载器
                       interfaces – 增强方法所在的类，这个类实现的接口，支持多个接口
                       h – 实现这个接口InvocationHandler，创建代理对象，写增强的方法
                */
               UserDao userDao = (UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(new UserDaoImpl()));
               int result = userDao.add(1, 2);
               System.out.println(result);
           }
       }
       //创建代理对象代码
       class UserDaoProxy implements InvocationHandler {
           private Object obj;
           public UserDaoProxy(Object obj) {
               this.obj = obj;
           }
           @Override
           public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
               System.out.println("before method execute...");
               //执行方法
               Object result = method.invoke(obj, args);
               System.out.println("after method execute...");
               return result;
           }
       }
       ```

     - 第二种 没有接口情况，使用CGLIB动态代理

       创建子类的代理对象，增强类的方法

## 二，AOP术语

1. 连接点：类里面那些方法可以被增强，这些方法称为连接点
2. 切入点：实际被真正增强的方法，称为切入点
3. 通知（增强）：实际增强的逻辑的部分称为通知（增强）
   - 前置通知
   - 后置通知
   - 环绕通知
   - 异常通知
   - 最终通知
4. 切面：把通知应用到切入点的过程

## 三，AOP的操作

1. 准备

   - Spring框架一般都是基于AspectJ实现AOP操作
   - AspectJ不是Spring组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用

2. 基于AspectJ实现AOP操作

   - 基于xml配置文件实现
   - 基于注解方式实现

3. 切入点表达式

   - 切入点表达式作用：知道那个类里面的那个方法进行增强
   - 语法结构：execution(\[权限修饰符]\[返回类型]\[类全路径]\[方法名称]([参数列表]))

4. AOP操作（AspectJ注解）

   - 创建类，在类里面定义方法

     ```java
     public class User {
         public void add(){
             System.out.println("add...");
         }
     }
     ```

   - 创建增强类（编写增强逻辑）

     ```java
     //增强类
     public class UserProxy {
         //前置通知
         public void before() {
             System.out.println("before...");
         }
     }
     ```

   - 执行通知的配置

     1. 在spring配置文件中开启注解扫描

        ```xml
        <!--开启注解扫描-->
        <context:component-scan base-package="com.wgx.Spring5.aop_anno"></context:component-scan>
        ```

     2. 使用注解创建User和UserProxy对象

        ```java
        @Component
        public class User {
            public void add(){
                System.out.println("add...");
            }
        }
        ```

     3. 在增强类上添加注解@Aspect

        ```java
        //增强类
        @Component
        @Aspect//生成代理对象
        public class UserProxy {
            //前置通知
            public void before() {
                System.out.println("before...");
            }
        }
        ```

     4. 在spring配置文件中开启生成代理对象

        ```xml
        <!--开启Aspect生成代理对象-->
        <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
        ```

   - 配置不同类型的通知

     在增强类的里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置

     ```java
     //增强类
     @Component
     @Aspect//生成代理对象
     public class UserProxy {
         //前置通知
         @Before(value = "execution(* com.wgx.Spring5.aop_anno.User.add(..))")
         public void before() {
             System.out.println("before...");
         }
         //最终通知
         @After(value = "execution(* com.wgx.Spring5.aop_anno.User.add(..))")
         public void after() {
             System.out.println("after...");
         }
         //后置通知(返回通知)
         @AfterReturning(value = "execution(* com.wgx.Spring5.aop_anno.User.add(..))")
         public void afterReturning() {
             System.out.println("afterReturning...");
         }
         //异常通知
         @AfterThrowing(value = "execution(* com.wgx.Spring5.aop_anno.User.add(..))")
         public void afterThrowing() {
             System.out.println("afterThrowing...");
         }
         //环绕通知
         @Around(value = "execution(* com.wgx.Spring5.aop_anno.User.add(..))")
         public void around(ProceedingJoinPoint joinPoint) throws Throwable {
             System.out.println("around before...");
             joinPoint.proceed();
             System.out.println("around after...");
         }
     }
     ```

   - 相同切入点的抽取

     ```java
     @Pointcut(value = "execution(* com.wgx.Spring5.aop_anno.User.add(..))")
     public void pointcut(){}
     
     //前置通知
     @Before(value = "pointcut()")
     public void before() {
         System.out.println("before...");
     }
     ```

   - 有多个增强类多同一方法进行增强，设置增强类优先级

     在增强类上面添加注解@Order(数字类型值)，数字类型值越小优先级越高

5. AOP操作（AspectJ配置文件）

   - 创建类，在类里面定义方法

   - 创建增强类（编写增强逻辑）

   - 在spring配置文件中创建两个类对象

     ```xml
     <!--创建对象-->
     <bean id="book" class="com.wgx.Spring5.aop_xml.Book"></bean>
     <bean id="bookProxy" class="com.wgx.Spring5.aop_xml.BookProxy"></bean>
     ```

   - 在spring配置文件中配置切入点

     ```java
     <!--配置aop增强-->
     <aop:config>
         <!--切入点-->
         <aop:pointcut id="p" expression="execution(* com.wgx.Spring5.aop_xml.Book.buy(..))"/>
         <!--切面-->
         <aop:aspect ref="bookProxy">
             <!--增强作用在具体方法上-->
             <aop:before method="before" pointcut-ref="p"/>
         </aop:aspect>
     </aop:config>
     ```

6. 完全注解开发

   创建配置类

   ```java
   @Configuration
   @ComponentScan(basePackages = "com.wgx.Spring5")
   @EnableAspectJAutoProxy(proxyTargetClass = true)
   public class ConfigAop {
   }
   ```

# 第四章 JdbcTemplate

## 一，概念和准备

1. 什么是JdbcTemplate

   Spring框架对JDBC进行封装，使用JdbcTemplate方便实现对数据库的操作

2. 准备工作

   - 引入jar包

   - 在spring配置文件配置数据库连接池

     ```xml
     <!--数据库连接池-->
     <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
         <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
         <property name="url" value="jdbc:mysql://localhost:3306/user_db"/>
         <property name="username" value="root"/>
         <property name="password" value="wgx"/>
     </bean>
     ```

   - 配置JdbcTemplate对象，注入DataSource

     ```xml
     <!--JdbcTemplate对象-->
     <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
         <!--注入dataSource-->
         <property name="dataSource" ref="dataSource"/>
     </bean>
     ```

   - 创建service类，创建dao类，在dao注入jdbcTemplate对象

     1. 配置文件

        ```xml
        <!--开启组件扫描-->
        <context:component-scan base-package="com.wgx.Spring5"></context:component-scan>
        ```

     2. Service

        ```java
        @Service
        public class BookServiceImpl {
            @Autowired//注入BookDao
            private BookDao bookDao;
        }
        ```

     3. Dao

        ```java
        @Repository
        public class BookDaoImpl {
            @Autowired//注入JdbcTemplate
            private JdbcTemplate jdbcTemplate;
        }
        ```

## 二，JdbcTemplate操作数据库

1. 添加

   ```java
   @Override
   public void add(Book book) {
       String sql = "insert into book values(?, ?, ?)";
       jdbcTemplate.update(sql, book.getBookId(), book.getBookName(), book.getStatus());
   }
   ```

2. 修改

   ```java
   @Override
   public void update(Book book) {
       String sql = "update book set name = ?, status = ? where id = ?";
       jdbcTemplate.update(sql, book.getBookName(), book.getStatus(), book.getBookId());
   }
   ```

3. 删除

   ```java
   @Override
   public void delete(String bookId) {
       String sql = "delete from book where id = ?";
       jdbcTemplate.update(sql, bookId);
   }
   ```

4. 查询

   - 返回Object

     ```java
     @Override
     public int queryCount() {
         String sql = "select count(*) from book";
         return jdbcTemplate.queryForObject(sql, Integer.class);
     }
     ```

   - 返回对象

     ```java
     @Override
     public Book queryBookById(int bookId) {
         String sql = "select id as bookId, name as bookName, status from book where id = ?";
         return jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), bookId);
     }
     ```

   - 返回集合

     ```java
     @Override
     public List<Book> queryBooks() {
         String sql = "select id as bookId, name as bookName, status from book";
         return jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
     }
     ```

5. 批量操作

   - 批量添加

     ```java
     @Override
     public void batchAdd(List<Object[]> args) {
         String sql = "insert into book values(?, ?, ?)";
         jdbcTemplate.batchUpdate(sql, args);
     }
     ```

   - 批量修改

     ```java
     @Override
     public void batchUpdate(List<Object[]> args) {
         String sql = "update book set name = ?, status = ? where id = ?";
         jdbcTemplate.batchUpdate(sql, args);
     }
     ```

   - 批量删除

     ```java
     @Override
     public void batchDelete(List<Object[]> args) {
         String sql = "delete from book where id = ?";
         jdbcTemplate.batchUpdate(sql, args);
     }
     ```

# 第五章 事务

## 一，基本概念

1. 什么是事务

   事务就是数据库操作最基本的单元，逻辑上一组操作，要么都成功，如果有一个失败所有操作都失败

2. 事务的特性（ACID）

   - 原子性
   - 一致性
   - 隔离性
   - 持久性

## 二，事务操作

1. 搭建环境

   - 创建service，搭建dao，完成对象创建和注入关系

     ```java
     @Service(value = "userService")
     public class UserServiceImpl {
         @Autowired
         private UserDao userDao;
     }
     ```

     ```java
     @Repository(value = "userDao")
     public class UserDaoImpl {
         @Autowired
         private JdbcTemplate jdbcTemplate;
     }
     ```

2. 事务操作过程

   - 开启事务
   - 处理业务逻辑
   - 没有发生异常，提交事务
   - 出现异常，事务回滚

3. Spring事务管理介绍

   - 事务添加到JavaEE三层结构里面Service层（业务逻辑层）

   - 在Spring进行事务管理操作，有两种方式

     1. 编程式事务管理
     2. ==声明式事务管理==

   - 声明式事务管理

     1. ==基于注解方式==
     2. 基于xml配置文件方式

   - 在Spring进行声明式事务管理，底层使用AOP原理

   - Spring事务管理API

     PlatformTransactionManager接口，代表事务管理器，这个接口针对不同的框架提供不同的实现类

     ![](F:\截图\捕获143.PNG)

4. 注解声明式事务管理

   - 在spring配置文件配置事务管理器

     ```xml
     <!--事务管理器-->
     <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
         <!--注入数据源-->
         <property name="dataSource" ref="dataSource"/>
     </bean>
     ```

   - 在spring配置文件，开启事务注解

     1. 在spring配置文件引入名称空间tx

     2. 开启事务注解

        ```xml
        <!--开启事务注解-->
        <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
        ```

   - 在service类上面（获取service类里面方法上面）添加事务注解

     ```java
     @Service(value = "userService")
     @Transactional
     public class UserServiceImpl {
         @Autowired
         private UserDao userDao;
     }
     ```

     1. @Transactional添加到类上面，也可以添加到方法上面
     2. 如果把这个注解添加到类上面，这个类里面所有的方法都添加事务
     3. 如果把这个注解添加到方法上面，这个方法添加事务

5. 声明式事务管理参数配置

   在service类上面添加注解@Transactional，在这个注解里面可以配置事务相关参数

   ![](F:\截图\捕获144.PNG)

   - propagation：事务传播行为

     多事务方法直接进行调用，这个过程中事务时如何进行管理的

     ![](F:\截图\捕获145.PNG)

   - isolation：事务隔离级别

     - 事务有特性称为隔离性，多事务操作之间不会产生影响，不考虑隔离性产生很多问题
     - 脏读：一个未提交事务读取到另一个未提交事务的数据
     - 不可重复读：一个未提交事务读取到另一个已提交事务修改数据
     - 幻读：一个未提交事务读取到另一个已提交事务添加数据
     - ![](F:\截图\捕获146.PNG)

   - timeout：超时时长

     事务需要在一定事件内进行提交，如果不提交进行回滚

     默认值是-1，设置时间以秒为单位进行计算

   - readOnly：是否只读

     默认值为false，表示可以查询，可以添加修改删除操作

     这是readOnly为true，只能查询

   - rollbackFor：回滚

     设置出现哪些异常进行事务回滚

   - noRollbackFor：不回滚

     设置出现哪些异常不事务回滚

6. XML声明式事务管理

   - 配置事务管理器

     ```xml
     <!--事务管理器-->
     <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
         <!--注入数据源-->
         <property name="dataSource" ref="dataSource"/>
     </bean>
     ```

   - 配置通知

     ```xml
     <!--配置通知-->
     <tx:advice id="txadvice">
         <!--配置事务参数-->
         <tx:attributes>
             <tx:method name="transferAccounts"/>
         </tx:attributes>
     </tx:advice>
     ```

   - 配置切入点和切面

     ```xml
     <!--配置切入点和切面-->
     <aop:config>
         <!--配置切入点-->
         <aop:pointcut id="pointCut" expression="execution(* com.wgx.Spring5.service.UserService.*(..))"/>
         <!--配置切面-->
         <aop:advisor advice-ref="txadvice" pointcut-ref="pointCut"/>
     </aop:config>
     ```

7. 完全注解开发

   - 创建配置类，使用配置类代替xml文件

     ```java
     @Configuration//配置类
     @ComponentScan(basePackages = "com.wgx.Spring5")//组件扫描
     @EnableTransactionManagement//开启事务
     public class TxConfig {
         //创建数据库连接池
         @Bean
         public DruidDataSource getDruidDataSource() {
             DruidDataSource dataSource = new DruidDataSource();
             dataSource.setDriverClassName("com.mysql.jdbc.Driver");
             dataSource.setUrl("jdbc:mysql://localhost:3306/user_db");
             dataSource.setUsername("root");
             dataSource.setPassword("wgx");
             return dataSource;
         }
         //创建JdbcTemplate对象
         @Bean
         public JdbcTemplate getJdbcTemplate(DruidDataSource dataSource) {
             JdbcTemplate jdbcTemplate = new JdbcTemplate();
             //注入druidDataSource
             jdbcTemplate.setDataSource(dataSource);
             return jdbcTemplate;
         }
         //创建事务管理器
         @Bean
         public DataSourceTransactionManager getDataSourceTransactionManager(DruidDataSource dataSource) {
             DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
             transactionManager.setDataSource(dataSource);
             return transactionManager;
         }
     }
     ```

# 第五章 Spring5框架新功能

1. 整个Spring5框架的代码基于java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中被删除

2. Spring5框架自带了通用的日志封装

   - Spring5已经移除Log4jConfigListener，官方建议使用Log4j2

   - Spring5整和Log4j2

     - 引入相关jar包

     - 创建log4j.xml配置文件

       ```xml
       <?xml version="1.0" encoding="UTF-8"?>
       <Configuration status="INFO">
           <Appenders>
               <!-- 默认打印到控制台 -->
               <Console name="Console" target="SYSTEM_OUT">
                   <!-- 默认打印格式 -->
                   <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
               </Console>
           </Appenders>
           <Loggers>
               <!-- 默认打印日志级别为 info -->
               <Root level="INFO">
                   <AppenderRef ref="Console"/>
               </Root>
           </Loggers>
       </Configuration>
       ```

3. Spring5框架支持@Nullable注解

   - @Nullable注解可以使用在方法上、属性上、参数上，表示方法返回值可以为空，属性值可以为空，参数值可以为空

4. Spring5核心容器支持函数式风格GenericApplicationContext

   ```java
   @Test
   public void testGenericApplicationContext() {
       //1. 创建GenericApplicationContext对象
       GenericApplicationContext context = new GenericApplicationContext();
       //2. 调用context的方法对象注册
       context.refresh();
       context.registerBean("user", User.class, () -> new User());
       //3. 获取在Spring注册的对象
       User user = (User) context.getBean("user");
       System.out.println(user);
   }
   ```

# 第六章 WebFlux

## 一，介绍

1. 介绍
   - 是Spring5添加新的模块，用于web开发的，功能和SpringMVC类似的，Webflux使用当前一种比较流程响应式编程出现的框架。
   - 使用传统web框架，比如SpringMVC，这些基于Servlet容器，Webflux是一种异步非阻塞的框架，异步非阻塞的框架在Servlet3.1以后才支持，核心是基于Reactor的相关API实现的
   - 同步和异步：异步和同步针对调用者，调用者发送请求，如果等着对方回应之后才去做其他事情就是同步，如果发送请求之后不等薰对方回应就去做其他事情就是异步。
   - 阻塞和非阻塞：阻塞和非阻塞针对被调用者，被调用者受到请求之后，做完请求任务之后才给出反馈就是阻塞，受到请求之后马上给出反馈然后再去做事情就是非阻塞。
2. webflux特点
   - 异步非阻塞：在有限的资源下，提高系统的吞吐量和伸缩性，以Reactor为基础实现响应式编程
   - 函数式编程：Spring5框架基于java8，webflux使用java8函数式编程方式实现路由请求
3. 对比SpringMVC
   - 都可以使用注解方式，都运行在Tomcat等容器中
   - SpringMVC使用命令式编程，Webflux采用异步响应式编程

## 二，响应式编程

1. 什么是响应式编程

   响应式编程是一种面向数据流和变化传播的编程范式。这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播。

2. java8实现响应式编程

   ```java
   public class ObserverDemo extends Observable {
       public static void main(String[] args) {
           ObserverDemo observer = new ObserverDemo();
           //添加观察者
           observer.addObserver((o, arg) -> {
               System.out.println("发生了变化");
           });
           observer.addObserver((o, arg) -> {
               System.out.println("手动被观察者通知，准备改变");
           });
           //数据变化
           observer.setChanged();
           //通知
           observer.notifyObservers();
       }
   }
   ```

3. Reactor实现响应式编程

   1. 响应式编程操作中，Reactor是满足Reactive规范框架

   2. Reactor有两个核心类，Mono和Flux，这两个类实现接口Publisher,提供丰富操作符。Flux对象实现发布者，返回N个元素：Mono实现发布者，返回0或者1个元素

   3. Flux和Mono都是数据流的发布者，使用Flux和Mono都可以发出三种数据信号：元素值，错误信号，完成信号，错误信号和完成信号都代表终止信号，终止信号用于告诉订阅者数据流结束了，错误信号终止数据流同时把错误信息传递给订阅者

   4. 引入依赖

      ```xml
      <dependency>
          <groupId>io.projectreactor</groupId>
          <artifactId>reactor-core</artifactId>
          <version>3.4.16</version>
      </dependency>
      ```

   5. 代码实现

      ```java
      public class Reactor {
          public static void main(String[] args) {
              //just方法直接声明
              Flux.just(1, 2, 3, 4).subscribe(System.out :: println);
              Mono.just(1).subscribe(System.out :: println);
              //其他方法
              Integer[] arr = {1, 2, 3, 4};
              Flux.fromArray(arr);
      
              List<Integer> list = Arrays.asList(arr);
              Flux.fromIterable(list);
      
              Stream<Integer> stream = list.stream();
              Flux.fromStream(stream);
          }
      }
      ```

   6. 操作符

      对数据流进行一道道操作，称为操作符

      - map元素映射为新的元素
      - flatMap元素映射为流

## 三，SpringWebFlux执行流程和核心API

1. 执行流程

   与SpringMVC相同

   - HandlerMapping
   - HandlerAdapter
   - HandlerResultHandler

2. SpringWebFlux实现函数式编程，两个接口：RouteFunction（路由处理）和HandlerFunction（处理函数）

## 四，SpringWebFlux编程模型

1. 基于注解编程模型

   - bean

     ```java
     @Data
     @AllArgsConstructor
     @NoArgsConstructor
     public class User {
         private String name;
         private String gender;
         private Integer age;
     }
     ```

   - service

     ```java
     @Service
     public class UserServiceImpl implements UserService {
         public final Map<Integer, User> users = new ConcurrentHashMap<>();
     
         @PostConstruct
         public void init() {
             this.users.put(1, new User("wgx", "男", 23));
             this.users.put(2, new User("mary", "女", 23));
             this.users.put(3, new User("jack", "男", 23));
         }
     
         //根据id查询用户
         @Override
         public Mono<User> getUserById(Integer id) {
             return Mono.justOrEmpty(this.users.get(id));
         }
     
         //查询所有用户
         @Override
         public Flux<User> getAllUser() {
             return Flux.fromIterable(this.users.values());
         }
     
         //保存用户
         @Override
         public Mono<Void> saveUser(Mono<User> userMono) {
             return userMono.doOnNext(user -> {
                 this.users.put(this.users.size() + 1, user);
             }).then(Mono.empty());
         }
     }
     ```

   - controller

     ```java
     @RestController
     public class UserController {
         @Resource
         private UserService userService;
     
         @GetMapping("/user/{id}")
         public Mono<User> getUserById(@PathVariable("id") Integer id) {
             return userService.getUserById(id);
         }
     
         @GetMapping("/user")
         public Flux<User> getAllUser() {
             return userService.getAllUser();
         }
     
         @PostMapping("/user")
         public Mono<Void> saveUser(@RequestBody User user) {
             Mono<User> userMono = Mono.just(user);
             return userService.saveUser(userMono);
         }
     }
     ```

2. 基于函数式编程模型

   - handler

     ```java
     public class UserHandler {
         private final UserService userService;
     
         public UserHandler(UserService userService) {
             this.userService = userService;
         }
     
         public Mono<ServerResponse> getUserById(ServerRequest request) {
             //获取路径中的id值
             Integer id = Integer.valueOf(request.pathVariable("id"));
             //调用service获取数据
             Mono<User> userMono = this.userService.getUserById(id);
             //userMono转换
             return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(userMono, User.class).switchIfEmpty(Mono.empty());
         }
     
         public Mono<ServerResponse> getAllUser(ServerRequest request) {
             Flux<User> users = this.userService.getAllUser();
             return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(users, User.class);
         }
     
         public Mono<ServerResponse> saveUser(ServerRequest request) {
             Mono<User> userMono = request.bodyToMono(User.class);
             return ServerResponse.ok().build(this.userService.saveUser(userMono));
         }
     }
     ```

   - 路由

     ```java
     public class Server {
         //创建路由
         public RouterFunction<ServerResponse> routerFunction() {
             //创建handler对象
             UserServiceImpl userService = new UserServiceImpl();
             UserHandler userHandler = new UserHandler(userService);
             //设置路由
             return RouterFunctions.route(GET("/user/{id}").and(accept(APPLICATION_JSON)), userHandler :: getUserById)
                     .andRoute(GET("/user").and(accept(APPLICATION_JSON)), userHandler :: getAllUser)
                     .andRoute(POST("/user").and(accept(APPLICATION_JSON)), userHandler :: saveUser);
         }
     }
     ```

   - 创建服务

     ```java
     //创建服务器完成适配
     public void createReactorServer() {
         //路由和handler适配
         RouterFunction<ServerResponse> router = routerFunction();
         HttpHandler httpHandler = toHttpHandler(router);
         ReactorHttpHandlerAdapter adapter = new ReactorHttpHandlerAdapter(httpHandler);
         //创建服务器
         HttpServer httpServer = HttpServer.create();
         httpServer.handle(adapter).bindNow();
     }
     ```

   - 调用

     ```java
     public static void main(String[] args) {
         Server server = new Server();
         server.createReactorServer();
     }
     ```

   ==使用函数式编程时@PostConstruct注解不生效，原因未知==

   将service层初始化修改为

   ```java
   public UserServiceImpl() {
       this.users.put(1, new User("wgx", "男", 23));
       this.users.put(2, new User("mary", "女", 23));
       this.users.put(3, new User("jack", "男", 23));
   }
   ```

