# 第一章 HelloWorld

## 一，第一个mybatis

1. ```java
   public class MybatisTest {
       /**
        * 1. 根据xml配置文件(全局配置文件)创建一个SqlSessionFactory对象
        *      数据源以及一些运行环境
        * 2. sql映射文件配置了每一个sql，以及sql的封装规则
        * 3. 将sql映射文件注册在全局配置文件中
        * 4. 代码
        *      1）、根据配置文件得到SqlSessionFactory
        *      2）、使用sqlSession工厂获取到sqlSession对象
        *          一个sqlSession就是代表一次会话，用完就关闭
        *      3）、使用sql唯一标识来钙素MyBatis执行那个sql，sql都是保存在sql映射文件中
        */
       @Test
       public void test() throws IOException {
           String resource = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(resource);
           SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           //获取sqlSession实例，能直接执行已经映射的sql语句
           //sql的唯一标识
           //执行sql需要的参数
           SqlSession openSession = sqlSessionFactory.openSession();
           try {
               Employee employee = openSession.selectOne("com.wgx.mybatis.EmployeeMapper.selectEmp", 1);
               System.out.println(employee);
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               openSession.close();
           }
       }
   }
   ```

2. mybatis-config.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                   <property name="username" value="root"/>
                   <property name="password" value="wgx"/>
               </dataSource>
           </environment>
       </environments>
       <!--将写好的sql映射文件(EmployeeMapper.xml)注册到全局配置文件(mybatis-config.xml)中-->
       <mappers>
           <mapper resource="EmployeeMapper.xml"/>
       </mappers>
   </configuration>
   ```

3. EmployeeMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--
   namespace:名称空间
   id:唯一标识
   resultType:返回值类型
   #{id}:从传递过来的参数中取出id值
   -->
   <mapper namespace="com.wgx.mybatis.EmployeeMapper">
       <select id="selectEmp" resultType="com.wgx.mybatis.bean.Employee">
           select id, `name`, email, gender from employee where id = #{id}
       </select>
   </mapper>
   ```

## 二，使用接口方式

1. ```java
   @Test
   public void test01() throws IOException {
       String resource = "mybatis-config.xml";
       InputStream inputStream = Resources.getResourceAsStream(resource);
       SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
       SqlSession openSession = sqlSessionFactory.openSession();
       try {
           EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
           Employee employee = mapper.getEmployeeById(1);
           System.out.println(employee);
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           openSession.close();
       }
   }
   ```

2. EmployeeMapper接口

   ```java
   public interface EmployeeMapper {
       Employee getEmployeeById(Integer id);
   }
   ```

3. EmployeeMapper.xml

   ```xml
   <mapper namespace="com.wgx.mybatis.dao.EmployeeMapper">
       <select id="getEmployeeById" resultType="com.wgx.mybatis.bean.Employee">
           select id, `name`, email, gender from employee where id = #{id}
       </select>
   </mapper>
   ```

# 第二章 MyBatis全局配置文件

1. properties

   ```xml
   <!--
       mybatis可以使用properties来引入外部properties配置文件的内容
       resource:引入类路径下的资源
       url:引入网络路径或者磁盘路径下的资源
   -->
   <properties resource="dbconfig.properties"></properties>
   ```

2. settings

   ```xml
   <!--
       settings包含很多重要的设置项
       setting:用来设置每一个设置项
           name——设置项名
           value——设置项目值
   -->
   <settings>
       <setting name="mapUnderscoreToCamelCase" value="true"/>
   </settings>
   ```

3. typeAliases

   ```xml
   <!--
       typeAliases:别名处理器，可以为java类型起别名
   -->
   <typeAliases>
       <!--
           typeAlias:为某个java类型起别名
               type:指定哟啊其悲鸣的全类名，默认别名就是类名小写，employee
               alias:指定新的别名
       -->
       <typeAlias type="com.wgx.mybatis.bean.Employee" alias="employee"/>
       <!--
           package:为某个包下的所有类起别名
               name:指定包名（为当前包以及下面的所有后代包每一个类都起一个默认别名）
           批量起名的情况下，使用@Alias注解为某个类型指定新的别名
       -->
   </typeAliases>
   ```

4. environments

   ```xml
   <!--
       environments:mybatis可以配置多种环境
           environment:配置一个具体的环境信息，必须要有两个标签
           transactionManager:事务管理器
               type:事务管理器类型(JDBC|MANAGER)
               自定义事务管理器:实现TransactionFactory接口，type指定全类名
           dataSource:数据源
               type:数据源类型(UNPOOLED|POOLED|JNDI)
               自定义数据源:实现DataSourceFactory接口，type是全类名
   -->
   <environments default="development">
       <environment id="development">
           <transactionManager type="JDBC"/>
           <dataSource type="POOLED">
               <property name="driver" value="${jdbc.driver}"/>
               <property name="url" value="${jdbc.url}"/>
               <property name="username" value="${jdbc.username}"/>
               <property name="password" value="${jdbc.password}"/>
           </dataSource>
       </environment>
   </environments>
   ```

5. databaseIdProvider

   ```xml
   <!--
       databaseIdProvider:支持多数据库厂商
       type="DB_VENDOR"，VendorDatabaseIdProvider
           作用就是得到数据库厂商的标识(驱动getDatabaseProductName())，mybatis就能根据数据库厂商标识来指向不同的SQL
           MySQL,Oracle,SQL Server,...
   -->
   <databaseIdProvider type="DB_VENDOR">
       <!--为不同的数据库厂商起别名-->
       <property name="MySQL" value="mysql"/>
       <property name="Oracle" value="oracle"/>
       <property name="SQL Server" value="sqlserver"/>
   </databaseIdProvider>
   ```

6. mappers

   ```xml
   <!--mappers,将sql映射注册到全局配置中-->
   <mappers>
       <!--
           mapper:注册一个sql映射
           注册配置文件
               resource:引用类路径下的sql映射文件
               url:引用网络路径或者磁盘路径下的sql映射文件
           注册接口
               class:引用接口
               1. 有sql映射文件，映射文件名必须和接口同名，并且放在与接口同一目录下
               2. 没有sql映射文件，所有sql都是利用注解写在接口上
       -->
       <mapper resource="EmployeeMapper.xml"/>
       <!--批量注册-->
       <package name="com.wgx.mybatis.dao"/>
   </mappers>
   ```

# 第三章 MyBatis映射文件

1. 增删改查

   ```xml
   <mapper namespace="com.wgx.mybatis.dao.EmployeeMapper">
       <select id="getEmployeeById" resultType="employee">
           select id, `name`, email, gender from employee where id = #{id}
       </select>
   
       <insert id="addEmployee">
           insert into employee values(null, #{name}, #{email}, #{gender})
       </insert>
   
       <delete id="deleteEmployeeById">
           delete from employee where id = #{id}
       </delete>
   
       <update id="updateEmployee">
           update employee set `name` = #{name}, email = #{email}, gender = #{gender} where id = #{id}
       </update>
   </mapper>
   ```

2. 获取自增主键

   ```xml
   <!--
       parameterType:参数类型，可以省略
       mysql支持自增主键，自增主键的获取，mybatis也是利用statement。getGeneratedKeys()
       useGeneratedKeys="true"，使用自增主键获取主键策略
       keyProperty="id"，指定对应的主键属性，也就是mybatis获取主键以后将这个值封装到javabean的那个属性
   -->
   <insert id="addEmployee" parameterType="com.wgx.mybatis.bean.Employee" useGeneratedKeys="true" keyProperty="id">
       insert into employee values(null, #{name}, #{email}, #{gender})
   </insert>
   ```

3. 参数处理

   - 单个参数：mybatis不会做特殊处理

   - 多个参数：mybatis会做特殊处理

     ​	多个参数会被封装成一个map

     ​		key：param1...paramN（或者参数的索引也可以）

     ​		value：处理的参数值

     #{}就是从map中获取指定的key的值

   - 命名参数：明确指定封装参数时的key

     ​		key：使用@Param注解指定的值

     ​		value：参数值

     #{指定的key}取出对应的参数值

   - POJO参数：如果多个参数正好是我们业务逻辑的数据模型，我们就可以直接传入pojo

     #{属性名}：取出传入的pojo的属性值

   - Map参数：如果多个参数不是业务逻辑中的数据，没有对应的pojo，不经常使用为了方便，我们也可以传入map

     #{key}：取出map中的对应的值

   - TO参数：如果多个参数不是业务模型中的数据，但是经常使用，推荐来编写一个TO（Transfer Object）数据传输对象

     例：Page{

     ​				int index;

     ​				int size;

     ​		}

   - #{}和${}的区别

     #{}：是以预编译的形式，将参数设置到sql语句中

     ${}：取出的值直接拼接到sql语句中，会有安全问题

     原生jdbc不支持占位符的地方我们可以使用${}进行取值

4. 返回一个List<v\>集合，resultType是v

   ```xml
   <select id="getEmployeesByName" resultType="com.wgx.mybatis.bean.Employee">
       select * from employee
       <where>
           BINARY name like #{name}
       </where>
   </select>
   ```

5. resultMap

   - 多条记录封装一个map，Map<Integer, Employee>

     键是这条记录的主键，值是这条记录封装后的java对象

     使用MapKey注解告诉mybatis封装这个map的时候使用使用哪个属性作为map的key

     ```java
     @MapKey("id")
     Map<Integer, Employee> getEmployees();
     ```

   - 自定义封装规则

     ```xml
     <!--
         自定义某个javaBean的封装规则
         type:自定义规则的java类型
         id:唯一标识，方便引用
     -->
     <resultMap id="MyEmployee" type="com.wgx.mybatis.bean.Employee">
         <!--
             指定主键的封装规则，id定义主键会有主键列的优化
             column:指定哪一列
             property:指定对应的javabean中的那个属性
         -->
         <id column="id" property="id"/>
         <!--定义普通列的封装规则-->
         <result column="name" property="name"/>
         <result column="email" property="email"/>
         <result column="gender" property="gender"/>
         <!--其他不指定的列会自动封装，我们只要写resultMap就把全部的映射规则写上-->
     </resultMap>
     ```
     
   - 联合查询
   
     ```xml
     <!--联合查询:级联属性封装结果集-->
     <resultMap id="MyEmployeePlus" type="com.wgx.mybatis.bean.Employee">
         <id column="emp_id" property="id"/>
         <result column="emp_name" property="name"/>
         <result column="email" property="email"/>
         <result column="gender" property="gender"/>
         <result column="dept_id" property="dept.id"/>
         <result column="dept_name" property="dept.name"/>
     </resultMap>
     <select id="getEmpAndDept" resultMap="MyEmployeePlus">
         SELECT e.id emp_id, e.name emp_name, email, gender, d.id dept_id, d.name dept_name FROM employee e, dept d WHERE e.dept_id = d.id AND e.id = 1
     </select>
     ```
   
     ```xml
     <!--使用association定义关联的单个对象的封装规则-->
     <resultMap id="MyEmployeePlus" type="com.wgx.mybatis.bean.Employee">
         <id column="emp_id" property="id"/>
         <result column="emp_name" property="name"/>
         <result column="email" property="email"/>
         <result column="gender" property="gender"/>
         <association property="dept" javaType="com.wgx.mybatis.bean.Dept">
             <id column="dept_id" property="id"/>
             <result column="dept_name" property="name"/>
         </association>
     </resultMap>
     <select id="getEmpAndDept" resultMap="MyEmployeePlus">
         SELECT e.id emp_id, e.name emp_name, email, gender, d.id dept_id, d.name dept_name FROM employee e, dept d WHERE e.dept_id = d.id AND e.id = 1
     </select>
     ```
   
     ```xml
     <!--
         association定义关联对象的封装规则
         select:表明当前属性是调用select指定的方法查出的结果
         column:指定将那一列的值传给这个方法
     -->
     <resultMap id="MyEmpStep" type="com.wgx.mybatis.bean.Employee">
         <id column="id" property="id"/>
         <result column="name" property="name"/>
         <result column="email" property="email"/>
         <result column="gender" property="gender"/>
         <association property="dept" select="com.wgx.mybatis.dao.DeptMapper.getDeptById" column="dept_id"/>
     </resultMap>
     <select id="getEmpAndDept" resultMap="MyEmpStep">
         select * from employee where id = #{id}
     </select>
     ```
     
   - 延迟加载
   
     ```xml
     <settings>
         <setting name="mapUnderscoreToCamelCase" value="true"/>
         <!--
             可以使用延迟加载(懒加载)
             我们每次查询Employee对象的时候，都会将Dept对象一个查询出来
             延迟加载可以在我们使用的时候在去查询
             分段查询的基础之上加上两各个配置
         -->
         <setting name="lazyLoadingEnabled" value="true"/>
         <setting name="aggressiveLazyLoading" value="false"/>
     </settings>
     ```
   
   - collection嵌套结果集的方式
   
     ```xml
     <resultMap id="MyDept" type="com.wgx.mybatis.bean.Dept">
         <id column="dept_id" property="id"/>
         <result column="dept_name" property="name"/>
         <!--
             collection定义关联集合类型属性的封装规则
             ofType指定集合里面元素的类型
         -->
         <collection property="employees" ofType="com.wgx.mybatis.bean.Employee">
             <!--定义这个集合中的元素的封装规则-->
             <id column="emp_id" property="id"/>
             <result column="emp_name" property="name"/>
             <result column="email" property="email"/>
             <result column="gender" property="gender"/>
         </collection>
     </resultMap>
     <select id="getDeptByIdPlus" resultMap="MyDept">
         SELECT d.id dept_id, d.name dept_name, e.id emp_id, e.name emp_name, email, gender
         FROM dept d
         LEFT JOIN employee e
         ON e.dept_id = d.id
         WHERE d.id = #{id};
     </select>
     ```
   
   - collection分步查询
   
     ```xml
     <resultMap id="MyDeptStep" type="com.wgx.mybatis.bean.Dept">
         <id column="id" property="id"/>
         <result column="name" property="name"/>
         <collection property="employees" select="com.wgx.mybatis.dao.EmployeeMapperPlus.getEmployeesByDeptId" column="id"/>
     </resultMap>
     <select id="getDeptByIdStep" resultMap="MyDeptStep">
         select * from dept where id = #{id}
     </select>
     ```
   
     ```xml
     <select id="getEmployeesByDeptId" resultType="com.wgx.mybatis.bean.Employee">
         select * from employee where dept_id = #{deptId}
     </select>
     ```
   
   - 分步查询中多列值传递过去，将多列值封装map传递
   
     column="{key1=column1, key2=column2}"
   
   - fetchType：表示是否使用延迟加载
   
     ​	lazy：延迟
   
     ​	eager：立即
   
   - 鉴别器：mybatis可以使用discriminator判断某列的值，然后根据某列的值改变封装行为
   
     ```xml
     <resultMap id="MyEmployeeDis" type="com.wgx.mybatis.bean.Employee">
         <id column="id" property="id"/>
         <result column="name" property="name"/>
         <result column="email" property="email"/>
         <result column="gender" property="gender"/>
         <!--
             column:指定判定的列名
             javaType:列值对应的java类型
         -->
         <discriminator javaType="string" column="gender">
             <!--女生查出部门信息，resultType指定封装的结果集，不能缺少-->
             <case value="女" resultType="com.wgx.mybatis.bean.Employee">
                 <association property="dept" column="dept_id" select="com.wgx.mybatis.dao.DeptMapper.getDeptById"/>
             </case>
             <!--男生把name赋给email-->
             <case value="男" resultType="com.wgx.mybatis.bean.Employee">
                 <id column="id" property="id"/>
                 <result column="name" property="name"/>
                 <result column="name" property="email"/>
                 <result column="gender" property="gender"/>
             </case>
         </discriminator>
     </resultMap>
     <select id="getEmployeeByIdDis" resultMap="MyEmployeeDis">
         select * from employee where id = #{id}
     </select>
     ```
   

# 第四章 动态SQL

1. if

   ```xml
   <!--查询员工，要求:携带了那个字段查询条件就带上那个字段的值-->
   <select id="getEmployeesByCondition" resultType="com.wgx.mybatis.bean.Employee">
       select * from employee where
       <!--test,判断表达式(OGNL),从参数中取值判断-->
       <if test="id != null">
           id = #{id}
       </if>
       <if test="name != null and name != ''">
           and name like #{name}
       </if>
       <if test="email != null and email != ''">
           and email = #{email}
       </if>
       <if test="gender != null and gender != ''">
           and gender = #{gender}
       </if>
   </select>
   ```

   注意：如果id为null，SQL语句会出现错误

   解决方法：

   - 在where后面加上1=1

     ```xml
     <select id="getEmployeesByCondition" resultType="com.wgx.mybatis.bean.Employee">
         select * from employee where 1=1
         <!--test,判断表达式(OGNL),从参数中取值判断-->
         <if test="id != null">
             and id = #{id}
         </if>
         <if test="name != null and name != ''">
             and name like #{name}
         </if>
         <if test="email != null and email != ''">
             and email = #{email}
         </if>
         <if test="gender != null and gender != ''">
             and gender = #{gender}
         </if>
     </select>
     ```

   - 使用where标签

     ```xml
     <select id="getEmployeesByCondition" resultType="com.wgx.mybatis.bean.Employee">
         select * from employee
          <where>
             <!--test,判断表达式(OGNL),从参数中取值判断-->
             <if test="id != null">
                 id = #{id}
             </if>
             <if test="name != null and name != ''">
                 and name like #{name}
             </if>
             <if test="email != null and email != ''">
                 and email = #{email}
             </if>
             <if test="gender != null and gender != ''">
                 and gender = #{gender}
             </if>
          </where>
     </select>
     ```

2. trim

   ```xml
   <select id="getEmployeesByConditionTrim" resultType="com.wgx.mybatis.bean.Employee">
       select * from employee
       <!--
           prefix:前缀，trim标签中是整个字符串拼串后的结果，prefix给拼串后的结果加一个前缀
           prefixOverrides:前缀覆盖，去掉整个字符串前面多余的字符
           suffix:后缀，给整个字符串加一个后缀
           suffixOverrides:后缀覆盖，去掉整个字符串后面多余的字符
           select * from employee where id = ? and email = ? and 1 = 1
       -->
       <trim prefix="where" prefixOverrides="and" suffix="and 1 = 1" suffixOverrides="and">
           <!--test,判断表达式(OGNL),从参数中取值判断-->
           <if test="id != null">
               id = #{id} and
           </if>
           <if test="name != null and name != ''">
               name like #{name} and
           </if>
           <if test="email != null and email != ''">
               email = #{email} and
           </if>
           <if test="gender != null and gender != ''">
               gender = #{gender}
           </if>
       </trim>
   </select>
   ```

3. choose

   ```xml
   <select id="getEmployeesByConditionChoose" resultType="com.wgx.mybatis.bean.Employee">
       select * from employee
       <where>
           <!--带了id就使用id，带了name就使用name-->
           <choose>
               <when test="id != null">
                   id = #{id}
               </when>
               <when test="name != null">
                   name like #{name}
               </when>
               <otherwise>
                   gender = '男'
               </otherwise>
           </choose>
       </where>
   </select>
   ```

4. set

   ```xml
   <update id="updateEmployee" parameterType="com.wgx.mybatis.bean.Employee">
       update employee
       <set>
           <if test="name != null">
               name = #{name},
           </if>
           <if test="email != null">
               email = #{email},
           </if>
           <if test="gender != null">
               gender = #{gender},
           </if>
       </set>
       <where>
           id = #{id}
       </where>
   </update>
   ```

5. foreach

   ```xml
   <select id="getEmployeesByConditionForeach" resultType="com.wgx.mybatis.bean.Employee">
       select * from employee where id in
       <!--
           collection:指定要遍历的集合
                   list类型的参数会特殊处理封装在map中，map的key就叫list
           item:将当前遍历出的元素赋值给指定的变量
           separator:每个元素之间的分隔符
           open:遍历出所有元素结果拼接一个开始的字符
           close:遍历出所有的元素结果拼接一个结束的字符
           index:索引，遍历list的时候index就是索引，item就是当前值
                   遍历map的时候index表示的就是map的key，item就是map的值
           #{变量名}就能取出变量的值也就是当前遍历出的元素
       -->
       <foreach collection="ids" item="id" separator="," open="(" close=")">
           #{id}
       </foreach>
   </select>
   ```

   使用foreach批量保存

   ```xml
   <!--批量保存-->
   <insert id="batchInsert" parameterType="com.wgx.mybatis.bean.Employee">
       insert into employee values
       <foreach collection="employees" item="employee" separator=",">
           (#{employee.id}, #{employee.name}, #{employee.email}, #{employee.gender}, #{employee.dept.id})
       </foreach>
   </insert>
   ```

6. 内置参数

   ```xml
   <!--
       mybatis的两个内置参数
       _parameter:代表整个参数
           单个参数，_parameter就是这个参数
           多个参数，参数会封装成一个map，_parameter就代表这个map
       _databaseId:如果配置了databaseIdProvider标签
               _databaseId就是代表当前数据库的别名
   -->
   <select id="getEmployeesTestInnerParameter" resultType="com.wgx.mybatis.bean.Employee">
       <if test="_databaseId == 'mysql'">
           select * from employee
           <if test="_parameter != null">
               where id = #{id}
           </if>
       </if>
   </select>
   ```

7. bind

   ```xml
   <select id="getEmployeesByConditionChoose" resultType="com.wgx.mybatis.bean.Employee">
       select * from employee
       <where>
           <!--bind标签可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值-->
           <bind name="_name" value="'%' + name + '%'"></bind>
           <!--带了id就使用id，带了name就使用name-->
           <choose>
               <when test="id != null">
                   id = #{id}
               </when>
               <when test="name != null">
                   name like #{_name}
               </when>
               <otherwise>
                   gender = '男'
               </otherwise>
           </choose>
       </where>
   </select>
   ```

8. sql

   ```xml
   <!--
       sql标签用于抽取可重用的sql片段，方便后面引用
       1. sql抽取
       2. include引用已经抽取的sql
       3. include还可以自定义一些property，sql标签内部就能使用自定义的属性
               使用${property}取出自定义属性，不能使用#{}
   -->
   <sql id="insertColumn">
       e.id emp_id, e.name emp_name, email, gender, d.id dept_id, d.name dept_name from employee e, dept d
   </sql>
   <select id="getEmployeesByConditionForeach" resultMap="MyEmployee">
       select <include refid="insertColumn"></include> where dept_id = d.id and e.id in
       <foreach collection="ids" item="id" separator="," open="(" close=")">
           #{id}
       </foreach>
   </select>
   ```

# 第五章 MyBatis的缓存机制

1. 一级缓存（本地缓存），SqlSession级别的缓存，一级缓存是一只开启的

   - 与数据库同一次会话期间查询到的数据会放在本地缓存中，以后如果需要获取相同的数据，直接从缓存中取，没必要再去查询数据库
   - 一级缓存失效情况
     - SqlSession不同
     - SqlSession相同，查询条件不同（一级缓存中还没有这个数据）
     - SqlSession相同，两次查询之间执行了增删改操作
     - SqlSession相同，手动清除了一级缓存

2. 二级缓存（全局缓存），基于namespace级别的缓存，一个namespace对应一个二级缓存

   - 工作机制

     一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓存中

     如果会话关闭，一级缓存的数据会被保存到二级缓存中，新的会话查询信息，就可以参照二级缓存

     不同的namespace查出的数据会放在自己对应的缓存中（map）

     ==只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存==

   - 使用

     - 开启全局二级缓存

       ```xml
       <setting name="cacheEnabled" value="true"/>
       ```

     - 去mapper.xml中配置使用二级缓存

       ```xml
       <!--
           eviction:缓存的回收策略
               LRU-最近最少使用的，移除最长时间不被使用的对象
               FIFO-先进先出，按对象进入缓存的顺序来移除
               SOFT-软引用，移除基于垃圾回收器状态和软引用规则的对象
               WEAK-弱引用，更积极地移除基于垃圾收集器状态和弱引用规则的对象
               默认是LRU
           flushInterval:缓存刷新间隔
               缓存多长时间清空一次，默认不清空，设置一个毫秒值
           readOnly:是否只读
               true-只读，mybatis认为所有从缓存中获取数据的操作都是只读操作，不会修改数据
                   mybatis直接将数据在缓存中的引用交给用户，不安全，速度快
               false-非只读，mybatis认为获取的数据可能会被改变
                   mybatis会利用序列化和反序列化的技术克隆一份新的数据给你，安全速度慢
           size:缓存存放多少元素
           type:指定自定义缓存的全类名
               实现Cache接口
       -->
           <cache eviction="FIFO" flushInterval="60000" readOnly="false" size="1024"></cache>
       ```

     - POJO需要实现序列化接口

   - 和缓存相关的设置/属性

     - cacheEnabled="true"，false表示关闭缓存（关闭二级缓存，一级缓存可以使用）

     - 每个select标签都有useCache="true"，false不使用缓存（一级缓存依旧使用，二级缓存不使用）

     - 每个增删改标签都有flushCache="true"，一级二级缓存都会清除，增删改执行完成后就会清除缓存

     - 每个select标签都有flushCache="false"，设置为true则每次查询之后都会清空缓存，缓存是没有被使用的

     - sqlSession.clearCache()只是清除当前session的一级缓存

     - localCacheScope，本地缓存作用域（一级缓存），默认SESSION，当前会话的所有数据保存在会话缓存中。

       设置为STATEMENT，可以禁用一级缓存

3. MyBatis整合ehcache

   - 导入第三方缓存包
   - 导入第三方缓存整合的适配包
   - mapper.xml中使用自定义缓存

# 第六章 MyBatis—Spring整合

1. web.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <context-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:applicationContext.xml</param-value>
       </context-param>
       <listener>
           <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
       </listener>
   
       <filter>
           <filter-name>CharacterEncodingFilter</filter-name>
           <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
           <init-param>
               <param-name>encoding</param-name>
               <param-value>UTF-8</param-value>
           </init-param>
       </filter>
       <filter-mapping>
           <filter-name>CharacterEncodingFilter</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
   
       <filter>
           <filter-name>HiddenHttpMethodFilter</filter-name>
           <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
       </filter>
       <filter-mapping>
           <filter-name>HiddenHttpMethodFilter</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
   
       <servlet>
           <servlet-name>springMVC</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springMVC.xml</param-value>
           </init-param>
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>springMVC</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

2. springMVC.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          http://www.springframework.org/schema/mvc/spring-mvc.xsd">
       <!--开启注解扫描-->
       <!--只扫描控制器-->
       <context:component-scan base-package="com.wgx.mybatis" use-default-filters="false">
           <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   
   
       <!-- 配置Thymeleaf视图解析器 -->
       <bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
           <property name="order" value="1"/>
           <property name="characterEncoding" value="UTF-8"/>
           <property name="templateEngine">
               <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                   <property name="templateResolver">
                       <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                           <!-- 视图前缀 -->
                           <property name="prefix" value="/WEB-INF/templates/"/>
                           <!-- 视图后缀 -->
                           <property name="suffix" value=".html"/>
                           <property name="templateMode" value="HTML5"/>
                           <property name="characterEncoding" value="UTF-8" />
                       </bean>
                   </property>
               </bean>
           </property>
       </bean>
   
       <mvc:view-controller path="/" view-name="index"></mvc:view-controller>
       <mvc:view-controller path="/editEmployee" view-name="editEmployee"></mvc:view-controller>
       <!--开启注解驱动-->
       <mvc:annotation-driven/>
       <!--开启默认的servlet-->
       <mvc:default-servlet-handler/>
   
   </beans>
   ```

3. applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
          xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/tx
          http://www.springframework.org/schema/tx/spring-tx.xsd
          http://mybatis.org/schema/mybatis-spring
          http://mybatis.org/schema/mybatis-spring.xsd">
       <!--spring管理控制层以外的组件-->
       <context:component-scan base-package="com.wgx.mybatis">
           <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   
   
       <!--引入外部属性文件-->
       <context:property-placeholder location="classpath:druid.properties"/>
       <!--配置连接池-->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
           <property name="driverClassName" value="${jdbc.driverClassName}"></property>
           <property name="url" value="${jdbc.url}"></property>
           <property name="username" value="${jdbc.username}"></property>
           <property name="password" value="${jdbc.password}"></property>
       </bean>
       <!--事务管理器-->
       <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="dataSource"></property>
       </bean>
       <!--开启基于注解的事务-->
       <tx:annotation-driven transaction-manager="dataSourceTransactionManager"></tx:annotation-driven>
       <!--创建SqlSessionFactory对象-->
       <bean id="sessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource"></property>
           <!--configLocation指定全局配置文件的位置-->
           <property name="configLocation" value="classpath:mybatis-config.xml"></property>
           <!--mapperLocations指定mapper文件的位置-->
           <property name="mapperLocations" value="classpath:mapper/*.xml"></property>
       </bean>
       <!--扫描所有mapper的接口，让这些mapper能够自动注入-->
       <mybatis-spring:scan base-package="com.wgx.mybatis.dao"/>
   
   </beans>
   ```

# 第七章 逆向工程

1. mbg.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" standalone="no"?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   <generatorConfiguration>
       <context id="DB2Tables" targetRuntime="MyBatis3Simple">
           <!--jdbcConnection指定如何连接到目标数据库-->
           <jdbcConnection driverClass="com.mysql.jdbc.Driver"
               connectionURL="jdbc:mysql://localhost:3306/mybatis"
               userId="root"
               password="wgx">
           </jdbcConnection>
           <javaTypeResolver>
               <property name="forceBigDecimals" value="false"/>
           </javaTypeResolver>
           <!--
               javaModelGenerator:指定JavaBean生成的策略
               targetPackage:目标包名
               targetProject:目标工程
           -->
           <javaModelGenerator targetPackage="com.wgx.mybatis.bean" targetProject="./src/main/java"></javaModelGenerator>
           <!--
               sqlMapGenerator:sql映射文件生成策略
               targetPackage:目标包名
               targetProject:目标工程
           -->
           <sqlMapGenerator targetPackage="com.wgx.mybatis.dao" targetProject="./src/main/resource"></sqlMapGenerator>
           <!--
               javaClientGenerator:指定mapper接口所在的位置
               targetPackage:目标包名
               targetProject:目标工程
           -->
           <javaClientGenerator type="XMLMAPPER" targetPackage="com.wgx.mybatis.dao" targetProject="./src/main/java"></javaClientGenerator>
           <!--指定要逆向分析哪些表，根据表要创建javaBean-->
           <table tableName="employee" domainObjectName="Employee"></table>
           <table tableName="dept" domainObjectName="Dept"></table>
       </context>
   </generatorConfiguration>
   ```

2. test.java

   ```java
   public class test {
       @Test
       public void test() throws XMLParserException, IOException, InvalidConfigurationException, SQLException, InterruptedException {
           ArrayList<String> warnings = new ArrayList<>();
           boolean overwrite = true;
           File configFile = new File("src/main/resources/mbg.xml");
           ConfigurationParser cp = new ConfigurationParser(warnings);
           Configuration config = cp.parseConfiguration(configFile);
           DefaultShellCallback callback = new DefaultShellCallback(overwrite);
           MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
           myBatisGenerator.generate(null);
   
       }
   }
   ```

# 第八章 MyBatis运行原理

1. 获取sqlSessionFactory对象

   解析文件的每一个信息保存在Configuration对象中，返回包含Configuration的DefaultSqlSession

   ==注：一个MapperStatement代表一个增删改查的详细信息==

2. 获取sqlSession对象

   返回一个DefaultSqlSession，包含Executor对象

   ==这一步会创建Executor对象==

3. 获取接口的代理对象（MapperProxy）

   getMapper。使用MapperProxyFactory创建一个MapperProxy的代理对象

   代理对象里面包含了，DefaultSqlSession(Executor)

4. 执行增删改查方法

5. 总结

   1. 根据配置文件（全局，sql映射）初始化Configuration对象

   2. 创建一个DefaultSqlSession对象，里面包含Configuration对象以及Executor（根据全局配置文件中的defaultExecutorType创建出对应的Executor）

   3. DefaultSqlSession.getMapper()，拿到Mapper接口对应的MapperProxy

   4. MapperProxy里面有DefaultSqlSession

   5. 执行增删改查方法

      - 调用DefaultSqlSession的增删改查（Executor）

      - 会创建一个StatementHandler对象

        （同时也会创建出ParameterHandler对象和ResultSetHandler）

      - 调用StatementHandler预编译参数以及设置参数值

      - 调用StatementHandler的增删改查方法

      - ResultHandler封装结果

      注：四大对象每个创建的时候都会有一个interceptorChain.pluginAll(parameterHandler

# 第九章 插件

1. 插件原理

   - 每一个四大对象创建出来的时候不是直接返回的，而是调用interceptorChain.pluginAll()方法

   - 获取到所有的Interceptor(插件需要实现的接口)

     调用interceptor.plugin(target)；返回target包装后的对象

   - 插件机制，我们可以使用插件为目标对象创建一个代理对象；AOP

     我们的插件可以为四大对象创建出代理对象

     代理对象就可以拦截到四大对象的每一个执行

2. 插件的编写

   - 编写Interceptor的实现类

   - 使用@interceptors注解完成插件的签名

     ```java
     /**
      * 完成插件的签名
      */
     @Intercepts(
             {
                     @Signature(type = StatementHandler.class, method = "parameterize", args = Statement.class)
             })
     public class MyFirstPlugin implements Interceptor {
         /**
          * interceptor,拦截目标对象的目标方法执行
          * @param invocation
          * @return  返回执行后的返回值
          * @throws Throwable
          */
         @Override
         public Object intercept(Invocation invocation) throws Throwable {
             Object proceed = invocation.proceed();
             return proceed;
         }
     
         /**
          * plugin,包装目标对象，为目标对象创建一个代理对象
          * @param target
          * @return 返回包装后的对象
          */
         @Override
         public Object plugin(Object target) {
             Object wrap = Plugin.wrap(target, this);
             return wrap;
         }
     
         /**
          * setProperties,将插件注册时的property属性设置进来
          * @param properties
          */
         @Override
         public void setProperties(Properties properties) {
             System.out.println("插件的配置信息:" + properties);
         }
     }
     ```

   - 将写好的插件注册到全局配置文件中

     ```xml
     <!--plugins注册插件-->
     <plugins>
         <plugin interceptor="com.wgx.mybatis.dao.MyFirstPlugin">
             <property name="username" value="root"/>
             <property name="password" value="wgx"/>
         </plugin>
     </plugins>
     ```

   - 一个简单的插件

     ```java
     @Override
     public Object intercept(Invocation invocation) throws Throwable {
         //动态的改变sql的运行的采纳数，查询一号员工时，实际从数据库查询出三号员工
         System.out.println("MyFirstPlugin---intercept方法" + invocation.getMethod());
         //获取当前的拦截对象
         Object target = invocation.getTarget();
         //获取target的元数据
         MetaObject metaObject = SystemMetaObject.forObject(target);
         //获取parameterHandler.parameterObject的值，即sql语句的参数
         Object value = metaObject.getValue("parameterHandler.parameterObject");
         //判断value是否为1，如果为1，修改为3
         if (Integer.parseInt(value.toString()) == 1) {
             //修改参数为3
             metaObject.setValue("parameterHandler.parameterObject", 3);
         }
         return invocation.proceed();
     }
     ```

# 第十章 扩展

1. 分页

   - 导入PageHelper依赖

   - 注册PageInterceptor

     ```xml
     <plugin interceptor="com.github.pagehelper.PageInterceptor">
     </plugin>
     ```

   - 

   - ```java
     @Test
     public void testSimple() throws IOException {
         String resource = "mybatis-config.xml";
         InputStream inputStream = Resources.getResourceAsStream(resource);
         SqlSession openSession = new SqlSessionFactoryBuilder().build(inputStream).openSession();
         try {
             EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
             Page<Object> page = PageHelper.startPage(1, 3);
             List<Employee> employees = mapper.selectAll();
             //PageInfo(List<T> list, int navigatePages) pageInfo可以获取更多的分页信息
             //list传入结果集合
             //navigatePages分页导航，连续显示多少页
             PageInfo<Employee> pageInfo = new PageInfo<>(employees, 5);
             for (Employee employee : employees) {
                 System.out.println(employee);
             }
             System.out.println("总记录数" + page.getTotal());
             System.out.println("当前页码" + page.getPageNum());
             System.out.println("每页记录数" + page.getPageSize());
             System.out.println("总页数" + page.getPages());
             System.out.println("是否是第一页" + pageInfo.isIsFirstPage());
             System.out.println("是否有下一页" + pageInfo.isHasNextPage());
             int[] nums = pageInfo.getNavigatepageNums();
             for (int i = 0; i < nums.length; i++) {
                 System.out.println(nums[i]);
             }
         } finally {
             openSession.close();
         }
     }
     ```

2. 批量

   ```java
   @Test
   public void testBatch() throws IOException {
       String resource = "mybatis-config.xml";
       InputStream inputStream = Resources.getResourceAsStream(resource);
       SqlSession openSession = new SqlSessionFactoryBuilder().build(inputStream).openSession(ExecutorType.BATCH);
       try {
           EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
           for (int i = 0; i < 10000; i++) {
               mapper.insert(new Employee(UUID.randomUUID().toString().substring(0, 5), "2297665453@qq.com", "男", 1));
           }
           openSession.commit();
       } finally {
           openSession.close();
       }
   }
   ```

   - 与spring的整合

     1. 配置一个可以进行批量操作的sqlSession

        ```xml
        <!--配置一个可以批量操作的sqlSession-->
        <bean class="org.mybatis.spring.SqlSessionTemplate">
            <constructor-arg name="sqlSessionFactory" ref="sessionFactoryBean"/>
            <constructor-arg name="executorType" value="BATCH"/>
        </bean>
        ```

     2. 在service层使用@Autowired注解注入

        ```java
        @Autowired
        private SqlSession sqlSession;
        ```
   
3. 自定义类型处理器

   - 枚举类

   ```java
   public enum EmployeeStatus {
       LOGIN(100, "登录"), LOGOUT(200, "登出"), REMOVE(300, "注销");
       private Integer statusCode;
       private String statusMessage;
   
   
       EmployeeStatus(int statusCode, String statusMessage) {
           this.statusCode = statusCode;
           this.statusMessage = statusMessage;
       }
   
       public static EmployeeStatus getStatusMessageByStatusCode(Integer statusCode) {
           switch (statusCode) {
               case 100 :
                   return LOGIN;
               case 200 :
                   return LOGOUT;
               case 300 :
                   return REMOVE;
               default :
                   return LOGOUT;
           }
       }
   
       public Integer getStatusCode() {
           return statusCode;
       }
   
       public void setStatusCode(Integer statusCode) {
           this.statusCode = statusCode;
       }
   
       public String getStatusMessage() {
           return statusMessage;
       }
   
       public void setStatusMessage(String statusMessage) {
           this.statusMessage = statusMessage;
       }
   }
   ```

   - 处理器

   ```java
   public class MyEmployeeStatusTypeHandler implements TypeHandler<EmployeeStatus> {
   
       @Override
       public void setParameter(PreparedStatement ps, int i, EmployeeStatus parameter, JdbcType jdbcType) throws SQLException {
           System.out.println("保存的状态码:" + parameter.getStatusCode());
           ps.setInt(i, parameter.getStatusCode());
       }
   
       @Override
       public EmployeeStatus getResult(ResultSet rs, String columnName) throws SQLException {
           int code = rs.getInt(columnName);
           System.out.println("从数据库中获取的状态码" + code);
           EmployeeStatus status = EmployeeStatus.getStatusMessageByStatusCode(code);
           return status;
       }
   
       @Override
       public EmployeeStatus getResult(ResultSet rs, int columnIndex) throws SQLException {
           int code = rs.getInt(columnIndex);
           System.out.println("从数据库中获取的状态码" + code);
           EmployeeStatus status = EmployeeStatus.getStatusMessageByStatusCode(code);
           return status;
       }
   
       @Override
       public EmployeeStatus getResult(CallableStatement cs, int columnIndex) throws SQLException {
           int code = cs.getInt(columnIndex);
           System.out.println("从数据库中获取的状态码" + code);
           EmployeeStatus status = EmployeeStatus.getStatusMessageByStatusCode(code);
           return status;
       }
   }
   ```

   - 指定typeHandler

     1. 在mybatis全局配置文件中

        ```xml
        <typeHandlers>
            <typeHandler handler="com.wgx.mybatis.typehandler.MyEmployeeStatusTypeHandler" javaType="com.wgx.mybatis.bean.EmployeeStatus"/>
        </typeHandlers>
        ```

     2. 在查询语句中

        ```xml
        #{status, typeHandler=com.wgx.mybatis.typehandler.MyEmployeeStatusTypeHandler}
        ```

     3. 在resultMap中

        ```xml
        <result column="status" property="status" typeHandler="com.wgx.mybatis.typehandler.MyEmployeeStatusTypeHandler"/>
        ```





