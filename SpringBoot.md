# 第一章 SpringBoot基础

## 一，HelloWorld

1. 配置maven的settings

   ```xml
   <mirror>
       <id>nexus-aliyun</id>
       <mirrorOf>central</mirrorOf>
       <name>Nexus aliyun</name>
       <url>http://maven.aliyun.com/nexus/content/groups/public</url>
   </mirror>
   <!-- 使用阿里云镜像 -->
   ```

   ```xml
   <profile>
       <id>jdk-1.8</id>
       <activation>
           <activeByDefault>true</activeByDefault>
           <jdk>1.8</jdk>
       </activation>
       <properties>
           <maven.compiler.source>1.8</maven.compiler.source>
           <maven.compiler.target>1.8</maven.compiler.target>
     		<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
       </properties>
   </profile>
   <!-- 让maven使用jdk1.8编译 -->
   ```

2. 编写pom.xml文件

   ```xml
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>2.6.3</version>
   </parent>
   
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
           <version>2.6.3</version>
       </dependency>
   </dependencies>
   ```

3. 编写springboot主程序

   ```java
   //@SpringBootApplication:这是一个springboot应用
   @SpringBootApplication
   public class MainApplication {
       public static void main(String[] args) {
           SpringApplication.run(MainApplication.class, args);
       }
   }
   ```

4. 编写controller

   ```java
   @RestController
   //@RestController表示@ResponseBody和@Controller
   //@ResponseBody
   //@Controller
   public class HelloController {
       @RequestMapping("/hello")
       public String hello() {
           return "hello springboot!";
       }
   }
   ```

5. 运行main方法

### 部署

---

1. 导入插件

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
   </build>
   ```

2. 把项目打成jar包，直接在目标服务器执行即可

## 二，依赖管理

### 依赖管理机制

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.3</version>
</parent>
<!-- 父项目 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.6.3</version>
</parent>
<!-- 的父项目中包含几乎开发中常用的依赖所有的版本 -->
<!-- 自动版本仲裁机制，自行修改版本号 -->
<properties>
    <mysql.version>5.1.6</mysql.version>
</properties>
```

### starter场景启动器

1. spring-boot-starter-\*，就是\*场景启动器

2. 只要引入starter，这个场景的所有常规需要的依赖我们都自动引入

3. 、*-spring-boot-starter，为第三方提供的简化开发的场景启动器

4. 启动器的底层都有

   ```xml
   <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter</artifactId>
     <version>2.6.3</version>
     <scope>compile</scope>
   </dependency>
   ```

### 自动配置特性

1. 自动配置SpringMVC
   - 引入SpringMVC全套组件（功能）
   - 自动配置SpringMVC常见组件
2. 自动配置Web常见功能，如：字符编码问题
3. 默认的包结构
   - 主程序所在的包即下面的所有子包里面的组件都会被默认扫描
   - 无需包扫描配置
   - 改变扫描路径使用
     1. @SpringBootApplication(scanBasePackages = "com.wgx")
     2. @ComponentScan
4. 各种配置拥有默认值
   - 默认配置最终都是映射到一个类
   - 配置文件的值最终会绑定到每个类上，这个类会在容器中创建对象
5. 按需加载所有自动配置项

## 三，底层注解

### @Configuration

```java
/**
 * 1. 配置类里面使用@Bean注解标注在方法上给容器注册组件，默认是单实例的
 * 2. 配置类本身也是组件
 * 3. proxyBeanMethods: 代理bean的方法
 *      Full(proxyBeanMethods = true)
 *      Lite(proxyBeanMethods = false)
 *      组件依赖
 */
@Configuration(proxyBeanMethods = true)//告诉springboot这是一个配置类
public class MyConfig {
    @Bean
    public User wgx() {
        //User组件依赖了Pet组件
        return new User("wgx", 22, tomcat());
    }
    @Bean
    public Pet tomcat() {
        return new Pet("tomcat");
    }
}
```

### @Import

```java
@Import({User.class, Pet.class})
//@Import({User.class, Pet.class})
//      给容器中自动创建出这个两个类型的组件，默认组件的名字就是全类名
```

### @Conditional

条件装配，满足条件才进行装配

```java
@ConditionalOnBean(name = "tomcat")
//如果容器中有tomcat才进行装配
@Bean
public User wgx() {
    //User组件依赖了Pet组件
    return new User("wgx", 22, tomcat());
}
```

### @ImportResource

导入xml配置文件

```java
@ImportResource("classpath:bean.xml")
```

### 配置绑定

```properties
mycar.brand=BMW
mycar.price=1000000
```

1. 直接使用@Component，加入到容器中

   ```java
   @Component
   //只有在容器中的组件才会有springboot提供的功能
   @ConfigurationProperties(prefix = "mycar")
   ```

2. 使用@EnableConfigurationProperties(Car.class)

   ```java
   @EnableConfigurationProperties(Car.class)//开启Car配置绑定功能，并把这个组件自动注册到容器中
   ```

## 四，自动配置

1. springboot先加载所有的自动配置类（xxxAutoConfiguration）
2. 每个自动配置类按照条件进行生效，默认都会绑定文件指定的值（xxxProperties里面拿），xxxProperties和配置文件进行了绑定
3. 生效的配置类就会给容器中装配很多的组件，只要容器中有这些组件，相当于这些功能就有了
4. 定制化配置
   - 用户直接自己使用@Bean替换底层组件
   - 用户去看这个组件是获取的配置文件什么值，在配置文件中修改

## 五，最佳实践

1. 引入场景依赖
2. 查看自动配置了哪些组件
   - 配置文件中debug=true开启自动配置报告。Negative不生效\Positive生效
3. 是否需要修改
   - 参照文件修改配置项
   - 自定义加入或者替换组件

### Lombok

简化java开发

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
   </dependency>
   ```

2. 安装Lombok插件

3. 在bean中使用注解

   ```java
   @Data//get和set以及toString方法
   @AllArgsConstructor//全参构造器
   @NoArgsConstructor//无参构造器
   public class Car {
       private String brand;
       private Integer price;
   }
   ```

   ```java
   @Slf4j
   ...
   log.info("hello!...");
   ```

### Spring Initializr

![](F:\截图\屏幕截图 2022-02-05 220557.png)

# 第二章 SpringBoot核心功能

## 一，配置文件

### yaml

1. 基本语法

   - key: value，key和value之间有空格
   - 大小写敏感
   - 使用缩进表示层级关系
   - 缩进不允许使用tab，只允许空格
   - 缩进的空格数不重要，只要相同层级元素左对齐即可
   - ’#‘表示注释
   - “与”“表示字符串内容会被转义/不转义

2. 数据类型

   - 字面量：单个的不可再分的值，date、boolean、string、number、null

     ```yaml
     k: v
     ```

3. 对象

   - 键值对的集合，map、hash、set、object

     ```yaml
     #行内写法
     k: {k1: v1, k2: v2, k3: v3}
     #或者
     k: 
     	k1: v1
     	k2: v2
     	k3: v3
     ```

4. 数组

   - 一组按次序排列的值，array、list、queue

     ```yaml
     #行内写法
     k: [v1, v2, v3]
     #或者
     k:
       - v1
       - v2
       - v3
     ```

实例

```java
@Data
@ConfigurationProperties("person")
public class Person {
    private String name;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animals;
    private Map<String, Object> score;
    private Set<Double> salary;
    private Map<String, List<Pet>> allPets;
}
```

```yaml
person:
  name: wgx
  boss: true
  birth: 1999/11/17
  age: 22
  pet:
    name: 旺财
    weight: 20
  interests: [java, C, C++]
  animals:
    - 旺财
    - 小花
  score: {math:90, english:80}
  salary: [10000, 20000]
  allPets:
    - sick:
        - name: 旺财
          weight: 20
        - name: 小花
          weight: 15
    - health:
        - name: 大黄
          weight: 30
```

## 二，web开发

### 简单功能的分析

1. 静态资源的访问

   - 静态资源目录

     类路径下：/static，/public，/resources，/META-INF/resources

     访问：当前项目根路径/+静态资源名

     原理：静态映射/**

2. 静态资源访问前缀

   默认无前缀

   ```yaml
   spring:
     mvc:
       static-path-pattern: /resources/**
   #当前项目根目录/+static-path-pattern+静态资源名=静态资源文件夹下
   ```

3. webjar

   自动映射

   将前端资源以jar包的形式导入

   ```xml
   <dependency>
       <groupId>org.webjars.npm</groupId>
       <artifactId>jquery</artifactId>
       <version>3.6.0</version>
   </dependency>
   ```

   ![](F:\截图\屏幕截图 2022-02-06 215418.png)

   最终访问路径：localhost:8080/==webjars/jquery/3.6.0/dist/jquery.js==

4. 欢迎页支持

   - 静态资源路径下index.html

     ```yaml
     spring:
       web:
         resources:
           static-locations: [classpath:pages/]
     #  mvc:
     #    static-path-pattern: /resources/**
     ```

     可以配置静态资源路径

     但是不可以配置静态资源的访问前缀，否则导致index.html不能被默认访问

   - controller能处理/index

5. favicon功能

   - 静态资源路径下favicon.ico

### 请求参数处理

1. 请求映射

   - @xxxMappeing

   - Rest风格

     - 核心Filter：HiddenHttpMethodFilter
     - 用法：表单提交试用post方式，带上**_method=需要的请求方式**参数

   - Rest原理（表单提交要使用Rest风格时）

     - 表单提交带上_method=需要的请求方式

     - 请求过来会被HiddenHttpMethodFilter拦截

       判断请求是否正常，并且是post

       获取_method的值

       兼容，put,delete,patch

       原生request（post），包装模式requestWrapper重写了getMethod方法，返回传入的值

       过滤器链放行的时候用wrapper，以后的方法调用getMethod方法是调用的requestWrapper的

### 改变默认的_method

自己在容器中放入一个HiddenHttpMethodFilter

```java
@Bean
public HiddenHttpMethodFilter hiddenHttpMethodFilter() {
    HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
    hiddenHttpMethodFilter.setMethodParam("_m");
    return hiddenHttpMethodFilter;
}

//private String methodParam = "_method";默认值
//使用该方法改变methodParam
public void setMethodParam(String methodParam) {
    Assert.hasText(methodParam, "'methodParam' must not be empty");
    this.methodParam = methodParam;
}
```

## 三，请求处理

### 常用参数注解使用

@PathVariable：路径变量

@RequestHeader：获取请求头

@RequestParam：获取请求参数

@CookieValue：获取cookie值

@RequestAttribute：获取request域属性

@RequestBody：获取请求体

@MatrixVariable：矩阵变量

### 矩阵变量

语法：/cars/sell;low=34;brand=bmw,byd

矩阵变量必须要有Url路径变量才能使用

```java
@GetMapping("/cars/{path}")
public Map carsSell(@MatrixVariable("low") Integer low,
                    @MatrixVariable("brand") List<String> brand,
                    @PathVariable String path) {
    HashMap<String, Object> map = new HashMap<>();
    map.put("low", low);
    map.put("brand", brand);
    map.put("path", path);
    return map;
}
```

SpringBoot默认禁用了矩阵变量的功能

开启方法

- ```java
  @Configuration
  public class MyConfig implements WebMvcConfigurer {
      @Override
      public void configurePathMatch(PathMatchConfigurer configurer) {
          UrlPathHelper urlPathHelper = new UrlPathHelper();
          //设置为不移除分号后面的内容
          urlPathHelper.setRemoveSemicolonContent(false);
          configurer.setUrlPathHelper(urlPathHelper);
      }
  }
  ```

- ```java
  @Bean
  public WebMvcConfigurer webMvcConfigurer() {
      return new WebMvcConfigurer() {
          @Override
          public void configurePathMatch(PathMatchConfigurer configurer) {
              UrlPathHelper urlPathHelper = new UrlPathHelper();
              //设置为不移除分号后面的内容
              urlPathHelper.setRemoveSemicolonContent(false);
              configurer.setUrlPathHelper(urlPathHelper);
          }
      };
  }
  ```

最终结果：{"path":"sell","low":34,"brand":["bmw","byd"]}

---

当矩阵变量中有相同的参数时：/emp/1;age=22/2;age=30

```java
@GetMapping("/emp/{empId}/{boosId}")
public Map employeeAndBoos(@MatrixVariable(value = "age", pathVar = "empId") Integer empAge,
                           @MatrixVariable(value = "age", pathVar = "boosId")Integer boosAge,
                           @PathVariable("empId") Integer empId,
                           @PathVariable("boosId") Integer boosId) {
    HashMap<String, Object> map = new HashMap<>();
    map.put("empId", empId);
    map.put("boosId", boosId);
    map.put("empAge", empAge);
    map.put("boosAge", boosAge);
    return map;
}
```

最终结果：{"boosAge":30,"empId":1,"boosId":2,"empAge":22}

### 自定义converter

```java
//在WebMvcConfigurer中
@Override
public void addFormatters(FormatterRegistry registry) {
    //修改默认的日期converter转换规则
    registry.addFormatter(new DateFormatter("yyyy-MM-dd"));
    //自定义converter
    registry.addConverter(new Converter<String, Pet>() {
        @Override
        public Pet convert(String source) {
            //source为传进来的参数(阿花,3)
            if (!source.isEmpty()) {
                Pet pet = new Pet();
                String[] split = source.split(",");
                pet.setName(split[0]);
                pet.setAge(Integer.valueOf(split[1]));
                return pet;
            }
            return null;
        }
    });
}
```

### 开启基于请求参数的内容协商

```yaml
spring:
  #开启基于请求参数的内容协商  format=XXX
  mvc:
    contentnegotiation:
      favor-parameter: true
```

### 自定义MessageConverter

```java
//在WebMvcConfigurer中
@Override
public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
    converters.add(new MyMessageConverter());
}
```

```java
public class MyMessageConverter implements HttpMessageConverter<Person> {

    @Override
    public boolean canRead(Class<?> clazz, MediaType mediaType) {
        return false;
    }

    @Override
    public boolean canWrite(Class<?> clazz, MediaType mediaType) {
        return clazz.isAssignableFrom(Person.class);
    }

    /**
     * 服务器需要调用该方法统计所有MessageConverter可以写出多少内容类型
     * @return 返回自定义的类型
     */
    @Override
    public List<MediaType> getSupportedMediaTypes() {
        return MediaType.parseMediaTypes("application/x-person");
    }

    @Override
    public Person read(Class<? extends Person> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException {
        return null;
    }

    @Override
    public void write(Person person, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
        //自定义数据处理方式
        String data = person.getName() + ";" + person.getAge();
        //写出数据
        OutputStream body = outputMessage.getBody();
        body.write(data.getBytes(StandardCharsets.UTF_8));
    }
}
```

### 自定义参数内容协商策略

```java
//在WebMvcConfigurer中
@Override
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    //自定的参数对应媒体类型的规则
    HashMap<String, MediaType> mediaTypes = new HashMap<>();
    mediaTypes.put("json", MediaType.APPLICATION_JSON);
    mediaTypes.put("xml", MediaType.APPLICATION_ATOM_XML);
    mediaTypes.put("person", MediaType.parseMediaType("application/x-person"));
    //创建一个自定义的参数内容协商策略,指定哪些参数对应哪些媒体类型
    ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypes);
    //将请求头的内容协商策略也加入进去
    HeaderContentNegotiationStrategy headerStrategy = new HeaderContentNegotiationStrategy();
    configurer.strategies(Arrays.asList(parameterStrategy, headerStrategy));
}
```

```java
//在WebMvcConfigurer中
@Override
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.mediaType("person", MediaType.parseMediaType("application/x-person"));
}
```

## 四，Thymeleaf

### 基本语法

| 表达式名字 | 语法   | 用途                          |
| ---------- | ------ | ----------------------------- |
| 变量取值   | ${...} | 获取请求域、session域、对象   |
| 选择取值   | *{...} | 获取上下文对象值              |
| 消息       | #{...} | 获取国际化等值                |
| 链接       | @{...} | 生成链接                      |
| 片段表达式 | ~{...} | jsp:include作用，引入公共页面 |

> > >行内写法：[[${session.user.name}]]

## 五，拦截器

1. 编写一个拦截器实现HandlerInterceptor接口

   ```java
   @Slf4j
   public class LoginInterceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           log.info("拦截的请求路径是" + request.getRequestURI());
           HttpSession session = request.getSession();
           Object loginUser = session.getAttribute("loginUser");
           if (loginUser != null) {
               return true;
           }else {
               session.setAttribute("message", "请先登录");
               //request.getRequestDispatcher("/login.html").forward(request, response);
               response.sendRedirect("/login.html");
               return false;
           }
       }
   }
   ```

2. 拦截器注册到容器中（实现WebMvcConfigurer的addInterceptors）

   ```java
   @Configuration
   public class WebConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LoginInterceptor()).addPathPatterns("/**").excludePathPatterns("/login.html", "/favicon.ico", "/login");
       }
   }
   ```

3. 指定拦截规则（如果拦截所有，静态资源也会被拦截）

## 六，文件上传

1. HTML页面

   ```html
   <form th:action="@{/upload_file}" method="post" enctype="multipart/form-data">
       username:<input type="text" name="username"></br>
       email:<input type="email" name="email"></br>
       上传头像:<input type="file" name="headPortrait"></br>
       上传照片:<input type="file" name="photos" multiple></br>
       <input type="submit">
   </form>
   ```

2. controller

   ```java
   @Slf4j
   @Controller
   public class FileController {
       @PostMapping("/upload_file")
       public String uploadFile(@RequestParam("username") String username,
                                @RequestParam("email") String email,
                                //单个文件
                                @RequestPart("headPortrait") MultipartFile headPortrait,
                                //多个文件
                                @RequestPart("photos") MultipartFile[] photos) throws IOException {
           log.info(username);
           log.info(email);
   
           if (!headPortrait.isEmpty()) {
               //获取文件原名
               String originalFilename = headPortrait.getOriginalFilename();
               //转换成一个文件
               headPortrait.transferTo(new File("F:\\" + originalFilename));
           }
           if (photos.length > 0) {
               for (MultipartFile photo : photos) {
                   if (!photo.isEmpty()) {
                       String originalFilename = photo.getOriginalFilename();
                       photo.transferTo(new File("F:\\" + originalFilename));
                   }
               }
           }
           return "index";
       }
   }
   ```

3. 修改文件上传相关配置

   ```yaml
   spring:
     #修改上传文件的单个文件大小限制以及单次请求总量大小限制
     servlet:
       multipart:
         max-file-size: 50MB
         max-request-size: 500MB
   ```

## 七，异常处理

1. 默认规则

   - springboot提供/error处理所有错误的映射
   - 对于机器客户端，它将生成JSON响应，其中包含错误、HTTP状态和异常消息的详细信息，对于浏览器客户端，响应一个“whitelabel”错误视图，以HTML格式呈现相同的数据
   - 要对其进行自定义，添加View解析为error
   - 要完全替换默认行为，可以实现ErrorController并注册该类型的Bean定义，或者添加ErrorAttributes类型的组件以使用现有机制但替换其内容

2. 定制错误处理逻辑

   - 自定义错误页

     error/404.html  error/505.html

   - @ControllerAdvice+@ExceptionHandler处理异常

   - 实现HandlerExceptionResolver处理异常

3. 自定义异常处理器

   ```java
   @Slf4j
   @ControllerAdvice
   public class GlobalExceptionHandler {
       @ExceptionHandler({ArithmeticException.class})
       public ModelAndView handlerArithmeticException(Exception e) {
           log.error("出现异常:" + e);
           return new ModelAndView("login");
       }
   }
   ```

4. 自定义异常

   ```java
   @ResponseStatus(value = HttpStatus.FORBIDDEN, reason = "重复登录")
   public class RepeatLogin extends RuntimeException {
       public RepeatLogin() {
       }
   
       public RepeatLogin(String message) {
           super(message);
       }
   }
   ----------------------------------------------------------------------
   if (session.getAttribute("loginUser") != null) {
       throw new RepeatLogin();
   }
   ```

5. 自定义HandlerExceptionResolver

   ```java
   @Order(value = Ordered.HIGHEST_PRECEDENCE)//设置该异常处理器为最高优先级
   @Component
   public class CustomHandlerExceptionResolver implements HandlerExceptionResolver {
       @Override
       public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
           try {
               response.sendError(520, "自定义错误");
           } catch (IOException e) {
               e.printStackTrace();
           }
           return new ModelAndView();
       }
   }
   ```

## 八，Web原生组件注入（Servlet、Filter、Listener）

1. 使用ServletAPI

   ```java
   @WebServlet("/myServlet")
   public class MyServlet extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           resp.getWriter().write("myServlet");
       }
   }
   
   
   @Slf4j
   @WebFilter(urlPatterns = {"/myServlet"})
   public class MyFilter extends HttpFilter {
       @Override
       protected void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
           log.info("Filter正在工作");
           chain.doFilter(request, response);
       }
   }
   
   
   @Slf4j
   @WebListener
   public class MyListener implements ServletContextListener {
       @Override
       public void contextInitialized(ServletContextEvent sce) {
           log.info("项目初始化完成");
       }
   }
   ```

   ```java
   //在SpringBootApplication上
   @ServletComponentScan(basePackages = "com.wgx.springboot04webtest.servlet")
   ```

   ==没有经过SpringBoot的拦截器==

2. 使用RegisterConfig

   ```java
   @Configuration
   public class MyRegisterConfig {
       @Bean
       public ServletRegistrationBean myServlet() {
           MyServlet myServlet = new MyServlet();
           return new ServletRegistrationBean(myServlet, "/myServlet");
       }
       @Bean
       public FilterRegistrationBean myFilter() {
           MyFilter myFilter = new MyFilter();
           return new FilterRegistrationBean<>(myFilter, myServlet());
       }
       @Bean
       public ServletListenerRegistrationBean myListener() {
           MyListener myListener = new MyListener();
           return new ServletListenerRegistrationBean(myListener);
       }
   }
   ```

## 九，切换服务器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!--排除Tomcat的jar包-->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!--引入undertow-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

### 定制化Servlet容器

- 实现WebServerFactoryCustomizer\<ConfigurableWebServerFactory>

  ```java
  @Component
  public class MyWebServerFactoryCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {
      @Override
      public void customize(ConfigurableWebServerFactory factory) {
          factory.setPort(80);
      }
  }
  ```

- 修改配置文件server.xxxx

- 直接自定义ConfigurableServletWebServerFactory

  ```java
  @Component
  public class MyWebServerFactoryCustomizer {
      @Bean
      public ConfigurableServletWebServerFactory configurableServletWebServerFactory() {
          TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
          tomcat.setPort(80);
          return tomcat;
      }
  }
  ```

## 十，数据访问

### 引入依赖，以及修改配置

```xml
<!--mysql依赖-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.29</version>
    <!--利用maven就近原则,选择适合的版本-->
</dependency>
<!--jdbc依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: wgx
    driver-class-name: com.mysql.jdbc.Driver
```

### 整合druid数据源

1. 自定义druid

   ```java
   @Configuration
   public class DruidConfig {
       @ConfigurationProperties("spring.datasource")
       @Bean
       public DataSource druidDataSource() {
           return new DruidDataSource();
       }
   
       //开启druid的监控功能
       //开启一个后台管理的Servlet
       @Bean
       public ServletRegistrationBean statViewServlet() {
           StatViewServlet statViewServlet = new StatViewServlet();
           ServletRegistrationBean<StatViewServlet> servletRegistrationBean = new ServletRegistrationBean<>(statViewServlet, "/druid/*");
           HashMap<String, String> initParams = new HashMap<>();
           initParams.put("loginUsername", "wgx");
           initParams.put("loginPassword", "123456");
           servletRegistrationBean.setInitParameters(initParams);
           return servletRegistrationBean;
       }
       //开启一个web监控的Filter
       @Bean
       public FilterRegistrationBean webStatFilter() {
           WebStatFilter webStatFilter = new WebStatFilter();
           FilterRegistrationBean<WebStatFilter> filterRegistrationBean = new FilterRegistrationBean<>(webStatFilter);
           filterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
           HashMap<String, String> initParams = new HashMap<>();
           initParams.put("exclusions", "*.js,*.css,/druid/*");
           filterRegistrationBean.setInitParameters(initParams);
           return filterRegistrationBean;
       }
   }
   ```

2. 基于starter

   ```xml
   <!--druid-starter-->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid-spring-boot-starter</artifactId>
       <version>1.1.14</version>
   </dependency>
   ```

   ```yaml
   druid:
     #开启监控，防火墙
     filters: stat,wall,slf4j
     #指定监控那个包
     aop-patterns: com.wgx.springboot04webtest.*
     #监控页面配置
     stat-view-servlet:
       enabled: true
       login-username: wgx
       login-password: 123456
     #web-filter配置
     web-stat-filter:
       enabled: true
       url-pattern: /*
       exclusions: "*.js,*.css,/druid/*"
     #每个filter的配置
     filter:
       stat:
         enabled: true
       wall:
         enabled: true
         config:
           drop-table-allow: false
   	slf4j:
   	  enabled: true
   ```


### 整合mybatis

1. 引入mybatis依赖

   ```xml
   <!--mybatis-->
   <dependency>
       <groupId>org.mybatis.spring.boot</groupId>
       <artifactId>mybatis-spring-boot-starter</artifactId>
       <version>2.2.2</version>
   </dependency>
   ```

2. 配置mybatis的规则

   ```yaml
   #配置mybatis
   mybatis:
   #  config-location: classpath:mybatis/mybatis-config.xml
     mapper-locations: classpath:mybatis/mapper/*.xml
     configuration:
       #开启驼峰命名法
       map-underscore-to-camel-case: true
   ```

### 整合MyBatis-Plus

1. 引入依赖

   ```xml
   <!--mybatis-plus-->
   <dependency>
       <groupId>io.github.guoshiqiufeng</groupId>
       <artifactId>mybatis-plus-boot-starter</artifactId>
       <version>1.3.7</version>
   </dependency>
   ```

2. 编写bean

   ```java
   @Data
   @NoArgsConstructor
   @AllArgsConstructor
   @TableName("dept_emp")
   public class Employee extends Model<Employee> {
       @TableId(type = IdType.AUTO)//定义主键自增长
       private Integer empNo;
       private String deptNo;
       private Date fromDate;
       private Date toDate;
   }
   ```

3. 编写mapper继承BaseMapper

   ```java
   @Mapper
   public interface EmployeeMapper extends BaseMapper<Employee> {
       Employee getEmpById(Integer id);
       void insertEmp(Employee employee);
   }
   ```

### 整合Redis

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 编写配置

   ```yaml
   #redis配置
   spring:
     redis:
       host: 47.100.85.66
       port: 6379
       password: w1999g1117x
       connect-timeout: 15000
       lettuce:
         pool:
           max-active: 8
           max-wait: 15000
           max-idle: 10
           min-idle: 0
   ```

## 十一，单元测试

1. 简介

   Spring Boot 2.2.0 版本开始引入 JUnit 5 作为单元测试默认库作为最新版本的JUnit框架，JUnit5与之前版本的Junit框架有很大的不同。由三个不同子项目的几个不同模块组成。
   ==JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage==
   JUnit Platform: Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自制的测试引擎，其他测试引擎也都可以接入。
   JUnit Jupiter: JUnit Jupiter提供了JUnit5的新的编程模型，是JUnit5新特性的核心。内部 包含了一个测试引擎，用于在Junit Platform上运行。
   JUnit Vintage: 由于JUint已经发展多年，为了照顾老的项目，JUnit Vintage提供了兼容JUnit4.x,Junit3.x的测试引擎。

   注意：
   SpringBoot 2.4 以上版本移除了默认对 Vintage 的依赖。如果需要兼容junit4需要自行引入（不能使用junit4的功能，如果需要继续兼容junit4需要自行引入vintage

   ```xml
   <dependency>
       <groupId>org.junit.vintage</groupId>
       <artifactId>junit-vintage-engine</artifactId>
       <scope>test</scope>
       <exclusions>
           <exclusion>
               <groupId>org.hamcrest</groupId>
               <artifactId>hamcrest-core</artifactId>
           </exclusion>
       </exclusions>
   </dependency>
   ```

2. 常用注解

   - @Test :表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
   - @ParameterizedTest :表示方法是参数化测试，下方会有详细介绍
   - @RepeatedTest :表示方法可重复执行，下方会有详细介绍
   - @DisplayName :为测试类或者测试方法设置展示名称
   - @BeforeEach :表示在每个单元测试之前执行
   - @AfterEach :表示在每个单元测试之后执行
   - @BeforeAll :表示在所有单元测试之前执行
   - @AfterAll :表示在所有单元测试之后执行
   - @Tag :表示单元测试类别，类似于JUnit4中的@Categories
   - @Disabled :表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
   - @Timeout :表示测试方法运行如果超过了指定时间将会返回错误
   - @ExtendWith :为测试类或测试方法提供扩展类引用

3. 断言

   断言（assertions）是测试方法中的核心部分，用来对测试需要满足的条件进行验证。这些断言方法都是org.junit.jupiter.api.Assertions 的静态方法。JUnit 5 内置的断言可以分成如下几个类别：检查业务逻辑返回的数据是否合理。

   - 简单断言：用来对单个值进行简单的验证

     ![](F:\截图\屏幕截图 2022-03-16 140424.png)

   - 数组的断言

     assertArrayEquals

   - 组合断言

     assertAll

   - 异常断言

     assertThrows

   - 快速失败

     fail

4. 前置条件

   JUnit 5 中的前置条件（assumptions【假设】）类似于断言，不同之处在于不满足的断言会使得测试方法失败，而不满足的前置条件只会使得测试方法的执行终止。前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

   ```java
   @Test
   @DisplayName("测试前置条件")
   public void testAssumptions() {
       Assumptions.assumeTrue(true);
       System.out.println("测试成功");
   }
   ```

5. 嵌套测试

   JUnit5可以通过 Java 中的内部类和@Nested注解实现嵌套测试，从而可以更好的把相关的测试方法组织一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。

   ==嵌套测试情况下，外层的Test不能驱动内层的Before(After)Each/All之类的方法，内层的Test可以驱动外层==

6. 参数化测试

   参数化测试是JUnit5很重要的一个新特性，它使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。
   利用@ValueSource等注解，指定入参，我们将可以使用不同的参数进行多次单元测试，而不需要每新增一个参数就新增一个单元测试，省去了很多冗余代码。

   - @ValueSource: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型
   - @NullSource: 表示为参数化测试提供一个null的入参
   - @EnumSource: 表示为参数化测试提供一个枚举入参
   - @CsvFileSource：表示读取指定CSV文件内容作为参数化测试入参
   - @MethodSource：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)

   ```java
   @ParameterizedTest
   @DisplayName("参数化测试")
   @ValueSource(ints = {1, 2, 3, 4, 5})
   public void testParameterizedTest(int i) {
       System.out.println(i);
   }
   ```

7. Junit4到Junit5迁移指南

   在进行迁移的时候需要注意如下的变化：

   - 注解在org.junit.jupiter.api包中，断言在 org.junit.jupiter.api.Assertions 类中，前置条件在org.junit.jupiter.api.Assumptions 类中。
   - 把@Before 和@After 替换成@BeforeEach 和@AfterEach。
   - 把@BeforeClass 和@AfterClass 替换成@BeforeAll 和@AfterAII。
   - 把@Ignore 替换成@Disabled。
   - 把@Category 替换成@Tag。
   - 把@RunWith、@Rule和@ClassRule替换成@ExtendWith。

## 十二，指标监控

### SpringBoot与Actuator

1. 引入依赖

   ```xml
   <!--引入监控功能-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   ```

2. 配置

   ```yaml
   management:
     endpoints:
       enabled-by-default: true
       web:
         exposure:
           include: '*' #以web方式暴露所有端点
   ```

3. 访问

   http://localhost:8080/actuator/**

   常用的EndPoint

   - Health：监控状况
   - Metrics：运行时错误
   - Loggers：日志记录

#### Health Endpoint

健康检查端点，我们一般用于在云平台，平台会定时的检查应用的健康状况，我们就需要Health Endpoint可以为平台返回当前应用的一系列组件健康状况的集合。
重要的几点:

- health endpoint返回的结果，应该是一系列健康检查后的一个汇总报告

- 很多的健康检查默认已经自动配置好了，比如：数据库、redis等

- 可以很容易的添加自定义的健康检查机制

- 开启详细信息

  ```yaml
  #endpoint分别配置每个端点
  endpoint:
    health:
      show-details: always
  ```

#### Metrics Endpoint

提供详细的、层级的、空间指标信息，这些信息可以被pull（主动推送）或者push（被动获取）方式得到

- 通过Metrics对接多种监控系统
- 简化核心Metrics开发
- 添加自定义Metrics或者扩展已有Metrics

### 定制EndPoint

1. 定制health

   ```java
   @Component
   public class MyComHealthIndicator extends AbstractHealthIndicator {
       @Override
       protected void doHealthCheck(Health.Builder builder) throws Exception {
           builder.up();
       }
   }
   ```

2. 定制info

   ```java
   @Component
   public class AppInfoContributor implements InfoContributor {
       @Override
       public void contribute(Info.Builder builder) {
           builder.withDetail("message", "你好");
       }
   }
   ```

3. 定制metrics

   - 构造器注入

     ```java
     @Service
     public class EmployeeService {
     
         @Autowired
         EmployeeMapper employeeMapper;
     
         Counter counter;
         public EmployeeService(MeterRegistry meterRegistry) {
             counter = meterRegistry.counter("employeeService.insertEmp.count");
         }
         public void insertEmp(Employee employee) {
             counter.increment();
             employeeMapper.insertEmp(employee);
         }
     }
     ```

4. 定制EndPoint

   ```java
   @Endpoint
   @Component
   public class MyEndPoint {
       @ReadOperation
       public String get() {
           return "读方法";
       }
       @WriteOperation
       public void set() {
           System.out.println("写方法");
       }
   }
   ```

### Boot Admin Server

1. 引入依赖

   ```xml
   <dependency>
       <groupId>de.codecentric</groupId>
       <artifactId>spring-boot-admin-starter-server</artifactId>
       <version>2.5.1</version>
   </dependency>
   ```

2. 添加注解，启动服务

   ```java
   @Configuration
   @EnableAutoConfiguration
   @EnableAdminServer
   public class SpringBootAdminApplication {
       public static void main(String[] args) {
           SpringApplication.run(SpringBootAdminApplication.class, args);
       }
   }
   ```

3. 在需要监控的微服务加入依赖

   ```xml
   <dependency>
       <groupId>de.codecentric</groupId>
       <artifactId>spring-boot-admin-starter-client</artifactId>
       <version>2.5.1</version>
   </dependency>
   ```

4. 配置

   ```yaml
   spring: 
     #boot-admin配置
     boot:
       admin:
         client:
           url: http://localhost:8888
           instance:
             prefer-ip: true#使用ip注册
     application:
       name: springboot-04-webtest
   ```

# 第三章 高级特性

## 一，Profile功能

1. application-profile功能

   - 默认配置文件application.yaml任何时候都会加载

   - 指定环境配置文件application-{env}.yaml

   - 激活指定环境

     - 配置文件激活

       ```properties
       spring.profiles.active=pord
       ```

     - 命令行激活

       java -jar springboot-05-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod

       修改配置文件的任意值，命令行优先

   - 默认配置与环境配置同时生效

   - 同名配置项， profile配置优先

2. @Profile条件装配

   ```java
   @Component
   @Profile("prod")
   @Data
   @ConfigurationProperties("person")
   public class Person {
       private String name;
       private Integer age;
   }
   ```

## 二，外部化配置

1. 外部配置源

   常用：java属性文件，yaml文件，环境变量，命令行参数

2. 配置文件查找位置

   - classpath 根路径
   - classpath 根路径下config目录
   - jar包当前目录
   - jar包当前目录的config目录
   - /config子目录的直接子目录

3. 配置文件加载顺序

   - 当前jar包内部的application.properties和application.yml
   - 当前jar包内部的application-(profile).properties 和 application-{profile}.yml
   - 引用的外部jar包的application.properties和application.yml
   - 引用的外部jar包的application-{profile}.properties和application-{profile}.yml

4. 指定环境优先，外部优先，后面的可以覆盖前面的同名配置项

## 三，自定义starter

1. starter启动原理

   - starter-pom引入autoconfiguration包

   ![](F:\截图\屏幕截图 2022-03-18 220834.png)

   - autoconfiguration中配置使用META-INF/spring.factories中EnableAutoConfiguration的值，使得项目启动加载指定的自动配置类 
   - 编写自动配置类xxxAutoConfiguration->xxxProperties
     - @Configuration
     - @Conditional
     - @EnableConfigurationProperties
     - @Bean
     - ...

2. 具体步骤

   - starter

     ```xml
     <dependencies>
         <dependency>
             <groupId>com.wgx</groupId>
             <artifactId>wgx-customer-spring-boot-starter-autoconfiguration</artifactId>
             <version>0.0.1</version>
         </dependency>
     </dependencies>
     ```

   - xxxProperties

     ```java
     @ConfigurationProperties("wgx.hello")
     public class HelloProperties {
         private String prefix;
         private String suffix;
     
         public String getPrefix() {
             return prefix;
         }
     
         public void setPrefix(String prefix) {
             this.prefix = prefix;
         }
     
         public String getSuffix() {
             return suffix;
         }
     
         public void setSuffix(String suffix) {
             this.suffix = suffix;
         }
     }
     ```

   - xxxService

     ```java
     public class HelloService {
         @Autowired
         HelloProperties helloProperties;
     
         public String hello(String name) {
             return helloProperties.getPrefix() + ":" + name + helloProperties.getSuffix();
         }
     }
     ```

   - xxxServiceAutoConfiguration

     ```java
     @Configuration
     @EnableConfigurationProperties({HelloProperties.class})
     public class HelloServiceAutoConfiguration {
         @Bean
         @ConditionalOnMissingBean({HelloService.class})
         public HelloService helloService() {
             return new HelloService();
         }
     }
     ```

   - 需要使用的项目中引入依赖

     ```xml
     <dependency>
         <groupId>com.wgx</groupId>
         <artifactId>wgx-customer-spring-boot-starter</artifactId>
         <version>1.0</version>
     </dependency>
     ```

