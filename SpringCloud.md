# 一，父工程

## 父工程Project

1. 使用maven创建父工程

2. 设置字符编码

   ![](F:\截图\屏幕截图 2022-05-04 213218.png)

3. 注解生效激活

   ![](F:\截图\屏幕截图 2022-05-04 213337.png)

## 父工程POM

maven中跳过单元测试

![](F:\截图\屏幕截图 2022-05-04 215714.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.wgx.springcloud</groupId>
  <artifactId>springcloud</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>

  <name>Maven</name>
  <!-- FIXME change it to the project's website -->
  <url>http://maven.apache.org/</url>
  <inceptionYear>2001</inceptionYear>

  <!--统一管理jar包版本-->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <lombok.version>1.16.18</lombok.version>
    <mysql.version>5.1.47</mysql.version>
    <druid.version>1.1.16</druid.version>
    <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
  </properties>

  <!--子模块继承之后提供作用：锁定版本+子模块不用写groupId和version-->
  <dependencyManagement>
    <dependencies>
      <!--spring boot 2.2.2-->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot</artifactId>
        <version>2.2.2.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud Hoxton.SR1-->
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>Hoxton.SR1</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud alibaba 2.1.0.RELEASE-->
      <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>2.1.0.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>

</project>
```

# 二，支付模块构建

1. 项目结构

   ![](F:\截图\屏幕截图 2022-05-04 230347.png)

2. 编写bean，controller，service，dao

   ![](F:\截图\屏幕截图 2022-05-05 153146.png)

3. Devtools

   - 引入依赖

   - 开启自动编译

     ![](F:\截图\屏幕截图 2022-05-05 153941.png)

# 三，消费者订单模块

1. RestTemplate

   RestTemplate提供了多种便捷访问远程Http服务的方法，是一种简单便捷的访问restful服务模板类，是Spring提供的用于访问Rest服务的客户端模板工具集。

2. 编写bean，config，controller

   ![](F:\截图\屏幕截图 2022-05-05 163110.png)

# 四，Eureka

1. 服务治理

   在传统的rpc远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。

2. 服务注册与发现

   Eureka采用了CS的设计架构，Eureka Server作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用Eureka的客户端连接到Eureka Server并维持心跳连接，这样系统的维护人员就可以通过Eureka Server来监控系统中各个微服务是否正常运行。

   在服务注册与发现中，有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息比如：服务地址通讯地址等以别名方式注册到注册中心上。另一方（消费者，服务提供者)，以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地RPC调用PC远程调用框架核心设计思想。在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系（服务治理概念）。在任何pc远程框架中，都会有一个注册中心（存放服务地址相关信息（接口地址））

3. Eureka Server

   各个微服务节点通过配置启动后，会在Eureka Server中进行注册，这样Eureka Server中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。

4. Eureka Client

   是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳（默认周期为30秒，。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒）

## Eureka服务端安装

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

2. 配置application.yaml

   ```yaml
   eureka:
     instance:
       hostname: localhost #eureka服务端的实例名称
     client:
       register-with-eureka: false #false表示不想注册中心注册自己
       fetch-registry: false #fasle表示自己就是注册中心，维护服务实例，并不需要去检索服务
       service-url:
         #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
         defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
   ```

3. 启动类

   ```java
   @SpringBootApplication
   @EnableEurekaServer
   public class CloudEurekaServer7001 {
       public static void main(String[] args) {
           SpringApplication.run(CloudEurekaServer7001.class, args);
       }
   }
   ```

## Eureka服务端注册

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   ```

2. 配置application.yaml

   ```yaml
   eureka:
     client:
       #表示是否将自己注册进Eureka Server，默认为true
       register-with-eureka: true
       #是否从Eureka Server抓取已有的注册信息，默认为true，单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
       fetch-registry: true
       service-url:
         defaultZone: http://localhost:7001/eureka
   ```

3. 启动类添加注解

   ```java
   @SpringBootApplication
   @EnableEurekaClient 
   public class CloudProviderPayment8001 {
       public static void main(String[] args) {
           SpringApplication.run(CloudProviderPayment8001.class, args);
       }
   }
   ```

## Eureka集群搭建

1. 修改hosts文件

   127.0.0.1		eureka7001.com
   127.0.0.1		eureka7002.com
   127.0.0.1		eureka7003.com

2. 修改application.yaml

   ```yaml
   server:
     port: 7001
   
   eureka:
     instance:
       hostname: eureka7001.com #eureka服务端的实例名称
     client:
       register-with-eureka: false #false表示不想注册中心注册自己
       fetch-registry: false #fasle表示自己就是注册中心，维护服务实例，并不需要去检索服务
       service-url:
         #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
         defaultZone: http://eureka7002.com:7002/eureka/
   ```

   ```yaml
   server:
     port: 7002
   
   eureka:
     instance:
       hostname: eureka7002.com #eureka服务端的实例名称
     client:
       register-with-eureka: false #false表示不想注册中心注册自己
       fetch-registry: false #fasle表示自己就是注册中心，维护服务实例，并不需要去检索服务
       service-url:
         #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
         defaultZone: http://eureka7001.com:7001/eureka/
   ```

## 订单和支付服务注册进Eureka集群

1. 修改application.yaml

   ```yaml
   server:
     port: 80
   
   spring:
     application:
       name: cloud-order-service
   
   eureka:
     client:
       #表示是否将自己注册进Eureka Server，默认为true
       register-with-eureka: true
       #是否从Eureka Server抓取已有的注册信息，默认为true，单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
       fetch-registry: true
       service-url:
         #defaultZone: http://localhost:7001/eureka  单机版
         defaultZone: http://eureka7001.com:7001/eureka, http://eureka7002.com:7002/eureka  #集群版
   ```

   订单服务同上

## 支付服务集群

1. 创建多个支付服务

   ![](F:\截图\屏幕截图 2022-05-06 221222.png)

2. 将订单服务中的固定地址修改为微服务名称

   ```java
   //public static final String PAYMENT_URL = "http://localhost:8001";
   public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";
   ```

3. 使用@LoadBalanced赋予RestTemplate负载均衡

   ```java
   @Bean
   @LoadBalanced
   public RestTemplate getRestTemplate() {
       return new RestTemplate();
   }
   ```

## Actuator微服务信息完善

修改主机名称，以及显示ip地址

```yaml
eureka:
  instance:
    instance-id: payment8001
    prefer-ip-address: true
```

![](F:\截图\屏幕截图 2022-05-06 223033.png)

## 服务发现Discovery

对于注册进Eureka里面的微服务，可以通过服务发现来获取该服务的信息

1. 在需要的Controller中注入

   ```java
   @Autowired
   private DiscoveryClient discoveryClient;
   ```

2. 在启动类上添加@EnableDiscoveryClient注解

   ```java
   @SpringBootApplication
   @EnableEurekaClient
   @EnableDiscoveryClient
   public class CloudProviderPayment8001 {
       public static void main(String[] args) {
           SpringApplication.run(CloudProviderPayment8001.class, args);
       }
   }
   ```

3. controller

   ```java
   @GetMapping("/discovery")
   public void discovery() {
       //获取总共有多少个微服务
       List<String> services = discoveryClient.getServices();
       for (String service : services) {
           log.info("service:{}", service);
       }
       //获取某个微服务名称下有多少个实例
       List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
       for (ServiceInstance instance : instances) {
           log.info("{}\t{}\t{}\t{}", instance.getServiceId(), instance.getHost(), instance.getPort(), instance.getUri());
       }
   }
   ```

## Eureka自我保护

1. 如果出现下面这段字，说明Eureka进入了保护模式

   ![](F:\截图\屏幕截图 2022-05-06 225417.png)

2. 导致原因

   某时刻某一个微服务不可用了，Eureka不会立刻清理，依旧会对该微服务的信息进行保存

3. 怎么禁止自我保护

   - 服务端修改

     ```yaml
     eureka:
       server:
         #关闭自我保护机制，保证不可用服务被及时剔除
         enable-self-preservation: false
         eviction-interval-timer-in-ms: 2000
     ```

     ![](F:\截图\屏幕截图 2022-05-06 230636.png)

   - 客户端修改

     ```yaml
     eureka:
       instance:
         instance-id: payment8001
         prefer-ip-address: true
         #Eureka客户端向服务端发送心跳的时间间隔，单位秒
         lease-renewal-interval-in-seconds: 1
         #Eureka服务端在收到最后一次心跳后等待时间上限，单位秒
         lease-expiration-duration-in-seconds: 2
     ```

# 五，Zookeeper

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
   </dependency>
   ```

2. 启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient//该注解用于向使用consul或者Zookeeper作为注册中心式注册服务
   public class CloudProviderPayment8004 {
       public static void main(String[] args) {
           SpringApplication.run(CloudProviderPayment8004.class, args);
       }
   }
   ```

3. application.yaml

   ```yaml
   #zookeeper
   spring:
     application:
       name: cloud-provider-payment
     cloud:
       zookeeper:
         connect-string: 47.100.85.66:2181
   ```

# 六，Consul

1. 介绍

   Consul是一套开源的分布式服务发现和配置管理系统，由HashiCorp公司用Go语言开发。提供了微服务系统中的服务治理、配置中心、控制总线等功能。这些功能中的每一个都可以根据需要单独使用，也可以一起使用以构建全方位的服务网格，总之Consul提供了一种完整的服务网格解决方案。

2. 安装

   下载完成之后直接运行consul.exe

   在运行的目录下查看版本：consul --version

   在开发模式下运行：consul agent -dev

   访问localhost:8500

## 服务注册进Consul

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-consul-discovery</artifactId>
   </dependency>
   ```

2. application.yaml

   ```yaml
   server:
     port: 8006
   
   spring:
     application:
       name: cloud-provider-payment-consul
     cloud:
       consul:
         host: localhost
         port: 8500
         discovery:
           service-name: ${spring.application.name}
   ```

3. 启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class CloudProviderConsulPayment8006 {
       public static void main(String[] args) {
           SpringApplication.run(CloudProviderConsulPayment8006.class, args);
       }
   }
   ```

# 七，三个服务注册中心区别

![](F:\截图\屏幕截图 2022-05-11 163728.png)

# 八，Ribbon负载均衡调用

1. 简介

   Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端负载均衡的工具。

   简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供客户端的软件负载均衡算法和服务调用。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer(简称LB)后面所有的机器，Ribbons会自动的帮助你基于某种规侧（如简单轮询，随机连接等）去连接这些机器。我们很容易使用Ribbo实现自定义的负载均衡算法。

2. Ribbon本地负载均衡客户端和Nginx服务端负载均衡区别

   Nginx是服务器负载均衡，客户端所有请求都会交给nginx,然后由nginx实现转发请求。即负载均衡是由服务端实现的。

   Ribbor本地负载均衡，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现RPC远程服务调用技术。

==Eureka依赖自带Ribbon==

3. RestTemplate的getForEntity

   ```java
   @GetMapping("/payment/getPaymentByIdEntity/{id}")
   public CommonResult<Payment> getPaymentByIdEntity(@PathVariable("id") Long id) {
       ResponseEntity<CommonResult> entity = restTemplate.getForEntity(PAYMENT_URL + "/payment/getPaymentById/" + id, CommonResult.class);
       if (entity.getStatusCode().is2xxSuccessful()) {
           return entity.getBody();
       } else {
           return new CommonResult<>(200, "操作失败");
       }
   }
   ```

4. 修改负载均衡机制

   **注意：修改负载均衡的配置类不能放在@ComponentScan注解扫描包下**

   ![](F:\截图\屏幕截图 2022-05-11 194500.png)

   - 配置类

     ```java
     @Configuration
     public class MySelfRule {
         @Bean
         public IRule myRule() {
             return new RandomRule();//修改为随机
         }
     }
     ```

   - 在启动类上添加@RibbonClient注解

     ```java
     @SpringBootApplication
     @EnableEurekaClient
     @RibbonClient(name = "CLOUD-PAYMENT-SERVICE", configuration = MySelfRule.class)
     public class CloudConsumerOrder80 {
         public static void main(String[] args) {
             SpringApplication.run(CloudConsumerOrder80.class, args);
         }
     }
     ```

5. 轮询算法原理

   rest接口第几次清求数 % 服务器集群总数量 = 实际调用服务器位置下标，每次服务重启动后rest接口计数从1开始。

# 九，OpenFeign

1. 简介

   Feign是一个声明式WebService客户端。使用Feign能让编写Web Service客户端更加简单。它的使用方法是定义一个服务接口然后在上面添加注解。Feign也支持可拔插式的编码器和解码器。Spring Cloud对Feign进行了封装,使其支持了Spring MVC标准注解和HttpMessageConverters。Feign可以与Eureka和Ribbons组合使用以支持负载均衡

2. 使用步骤

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-openfeign</artifactId>
     </dependency>
     ```

   - 启动类

     ```java
     @SpringBootApplication
     @EnableFeignClients
     @EnableEurekaClient
     public class CloudConsumerFeignOrder80 {
         public static void main(String[] args) {
             SpringApplication.run(CloudConsumerFeignOrder80.class, args);
         }
     }
     ```

   - feign接口

     ```java
     //value为调用的服务名，path为所有路径的前缀
     @FeignClient(value = "CLOUD-PAYMENT-SERVICE", path = "/payment")
     public interface PaymentFeignService {
         //"/create"为调用的路径
         @GetMapping("/create")
         CommonResult create(Payment payment);
     
         @GetMapping("/getPaymentById/{id}")
         CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
     }
     ```

   - controller

     ```java
     @Slf4j
     @RestController
     @RequestMapping("/consumer")
     public class OrderController {
         @Resource
         private PaymentFeignService paymentFeignService;
     
         @GetMapping("/payment/create")
         CommonResult create(Payment payment) {
             return paymentFeignService.create(payment);
         }
     
         @GetMapping("/payment/getPaymentById/{id}")
         CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
             return paymentFeignService.getPaymentById(id);
         }
     }
     ```
   
3. 超时控制

   - 修改超时时间

     ```yaml
     feign:
       client:
         config:
           default:
             #建立连接所用的时间，适用于网络状况正常的情况下，两端连接所需要的时间
             ConnectTimeOut: 5000
             #指建立连接后从服务端读取到可用资源所用的时间
             ReadTimeOut: 5000
     ```

4. 日志增强

   - 配置类

     ```java
     @Configuration
     public class FeignConfig {
         @Bean
         Logger.Level feignLoggerLevel() {
             return Logger.Level.FULL;
         }
     }
     ```

   - 修改application.yaml

     ```yaml
     logging:
       level:
         #feign日志要以什么级别监控哪个接口
         com.wgx.springcloud.service.PaymentFeignService: debug
     ```

# 十，Hystrix

1. 简介

   Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

   “断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类以熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应(FallBack)，而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

2. 案例

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
     </dependency>
     ```

   - 启动类

     ```java
     @SpringBootApplication
     @EnableEurekaClient
     @EnableCircuitBreaker//启动注解
     public class CloudProviderHystrixPayment8001 {
         public static void main(String[] args) {
             SpringApplication.run(CloudProviderHystrixPayment8001.class, args);
         }
     }
     ```

   - 在超时或者报错的方法上添加@HystrixCommand注解

     ```java
     //指定超时后调用的方法
     @HystrixCommand(fallbackMethod = "paymentInfo_timeoutHandler", commandProperties = {
             //设置超时时间为3秒
             @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
     })
     public String paymentInfo_timeout(Integer id) {
         try {
             TimeUnit.SECONDS.sleep(5);
         } catch (InterruptedException e) {
             e.printStackTrace();
         }
         return "线程池:" + Thread.currentThread().getName() + ",id=" + id + "_timeout\tO(∩_∩)O哈哈~";
     }
     ```

   - 编写超时或报错后调用的方法

     ```java
     public String paymentInfo_timeoutHandler(@PathVariable("id") Integer id) {
         return "线程池:" + Thread.currentThread().getName() + ",id=" + id + "_timeout\to(╥﹏╥)o";
     }
     ```

3. 全局服务降级

   - 使用@DefaultProperties注解指定全局的fallback方法

     ```java
     @DefaultProperties(defaultFallback = "paymentInfo_Global_timeoutHandler", commandProperties = {
             @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1000")
     })
     ```

   - 在需要熔断降级的方法上添加@HystrixCommand注解

4. 通配服务降级FeignFallback

   - 修改application.yaml

     ```yaml
     feign:
       hystrix:
         enabled: true
     ```

   - 编写一个类继承需要通配服务降级的接口，如果PaymentFeignService出现错误或者超时就会调用相对应的实现方法（即fallback方法）

     ```java
     @Component
     public class PaymentFeignFallbackService implements PaymentFeignService{
         @Override
         public String paymentInfo_success(Integer id) {
             return "PaymentFeignFallbackService_paymentInfo_success_o(╥﹏╥)o";
         }
     
         @Override
         public String paymentInfo_timeout(Integer id) {
             return "PaymentFeignFallbackService_paymentInfo_timeout_o(╥﹏╥)o";
         }
     }
     ```

   - 在@FeignClient注解中设置fallback属性指定实现类

     ```java
     @FeignClient(value = "CLOUD-PAYMENT-HYSTRIX-SERVICE", path = "/payment", fallback = PaymentFeignFallbackService.class)
     public interface PaymentFeignService {
         @GetMapping("/hystrix/success/{id}")
         String paymentInfo_success(@PathVariable("id") Integer id);
     
         @GetMapping("/hystrix/timeout/{id}")
         String paymentInfo_timeout(@PathVariable("id") Integer id);
     }
     ```

## 服务熔断

1. 概念

   熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。==当检测到该节点微服务调用响应正常后，恢复调用链路。==

2. 案例

   - ```java
     //服务熔断
     @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
             @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),//是否开启断路器
             @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),//请求次数
             @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),//时间窗口期
             @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60")//失败率达到多少后跳闸
     })
     public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
         if (id < 0) {
             throw new RuntimeException("----id不能为负数");
         }
         String uuid = IdUtil.simpleUUID();
         return Thread.currentThread().getName() + "\t调用成功:" + uuid;
     }
     
     public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
         return "id不能为负数，请稍后再试o(╥﹏╥)o\tid:" + id;
     }
     ```

3. 总结

   - 熔断打开：请求不再进行调用当前服务，内部设置时钟一般为MTTR(平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态
   - 熔断关闭：熔断关闭不会对服务进行熔断
   - 熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断

4. 重要参数

   - 快照时间窗：断路器确定是否打开需要统计一些请求和错误数据，而统计的时间范围就是快照时间窗，默认为最近的10秒。
   - 请求总数阀值：在快照时间窗内，必须满足请求总数阀值才有资格熔断。默认为20，意味着在10秒内，如果该Hystrix命令的调用次数不足20次即使所有的请求都超时或其他原因失败，断路器都不会打开。
   - 错误百分比阀值：当请求总数在快照时间窗内超过了阀值，比如发生了30次调用，如果在这30次调用中，有15次发生了超时异常，也就是超过50%的错误百分比，在默认设定50%阀值情况下，这时候就会将断路器打开。
   
5. 搭建图形化DashBoard

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
     </dependency>
     ```

   - 启动类

     ```java
     @SpringBootApplication
     @EnableHystrixDashboard
     public class CloudConsumerHystrixDashboard9001 {
         public static void main(String[] args) {
             SpringApplication.run(CloudConsumerHystrixDashboard9001.class, args);
         }
     }
     ```

   - 访问http://localhost:9001/hystrix

   - 在配置熔断的项目中添加配置

     ```java
     	/**
          * 此配置是为了服务监控而配置，与服务容错本身无关，springcloud升级后的坑
          * ServletRegistrationBean因为springboot的默认路径不是"/hystrix.stream"，
          * 只要在自己的项目里配置下面的servlet就可以了
          */
     @Bean
     public ServletRegistrationBean getServlet() {
         HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
         ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
         registrationBean.setLoadOnStartup(1);
         registrationBean.addUrlMappings("/hystrix.stream");
         registrationBean.setName("HystrixMetricsStreamServlet");
         return registrationBean;
     }
     ```

   - 监控http://localhost:8001/hystrix.stream

     ![](F:\截图\屏幕截图 2022-05-13 152606.png)

# 十一，Gateway

1. 三大核心概念
   - Route（路由）：路由是构建网关的基本模块，它由ID,目标URl,一系列的断言和过滤器组成，如果断言为true则匹配该路由
   - Predicate（断言）：开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由
   - Filter（过滤）：指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

## 搭建Gateway网关

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-gateway</artifactId>
   </dependency>
   ```

   ==注意：Gateway和web依赖冲突，需要排除spring-boot-starter-web以及相关依赖==

2. 配置application.yaml

   ```yaml
   spring:
     application:
       name: cloud-gateway-service
     cloud:
       gateway:
         routes:
           - id: payment_routh #路由的ID，没有固定的规则但是要求唯一，建议配合服务名
             uri: http://localhost:8001 #匹配后面提供的路由地址
             predicates:
               - Path=/payment/** #断言，路径相匹配的进行路由
   
   #        - id: payment_routh2 #路由的ID，没有固定的规则但是要求唯一，建议配合服务名
   #            uri: http://localhost:8001 #匹配后面提供的路由地址
   #            predicates:
   #              - Path=/payment/get/** #断言，路径相匹配的进行路由
   ```

3. 访问http://localhost:9527/payment/getPaymentById/1

4. 编写配置类代替配置文件

   ```java
   @Configuration
   public class GatewayConfig {
       @Bean
       public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder) {
           //尝试使用/game定位到百度的游戏新闻http://news.baidu.com/game
           return routeLocatorBuilder.routes().route("payment_routh3", fn -> fn.path("/game").uri("http://news.baidu.com/game")).build();
       }
   }
   ```

## 动态路由

1. 修改application.yaml

   ```yaml
   spring:
     application:
       name: cloud-gateway-service
     cloud:
       gateway:
         discovery:
           locator:
             enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
         routes:
           - id: payment_routh #路由的ID，没有固定的规则但是要求唯一，建议配合服务名
             #uri: http://localhost:8001 #匹配后面提供的路由地址
             uri: lb://cloud-payment-service #uri修改为服务名
             predicates:
               - Path=/payment/** #断言，路径相匹配的进行路由
   ```

## 常用的Route Predicate

1. After=2022-05-13T23:28:47.214+08:00[Asia/Shanghai]

   在该时间之后才能生效，Before和Between类似

2. Cookie=username,wgx

   Cookie Route Predicate需要两个参数，一个是Cookie name,一个是正则表达式。路由规则会通过获取对应的Cookie name值和正则表达式去匹配，如果匹配上就会执行路由，如果没有匹配上则不执行

3. Header=X-Request-Id, \d+

   一个是属性名称和一个正则表达式，这个属性值和正则表达式匹配侧执行。Host类似

4. Method=GET

   请求方法为GET

5. Query=username, \d+

   要有参数名username并且值必须为正整数

## Filter

自定义全局Filter

```java
@Slf4j
@Component
public class LogGatewayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("come in " + new Date());
        //需要携带username参数才能放行
        List<String> username = exchange.getRequest().getQueryParams().get("username");
        if (username == null) {
            log.info("用户名为null，非法用户，o(╥﹏╥)o");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```

# 十二，Config

1. Config是什么

   SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置。

   SpringCloud Config分为服务端和客户端两部分。服务端也称为分布式配置中心，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口客户端侧是通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息配置服务器默认采用git来存储配置信息，这样就有助于对环境配置进行版本管理，并且可以通过git客户端工具来方便的管理和访问配置内容
   
2. Config配置总控中心搭建

   - 在GitHub上创建仓库

     ![](F:\截图\屏幕截图 2022-06-05 220028.png)

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-config-server</artifactId>
     </dependency>
     ```

   - application.yaml

     ```yaml
     server:
       port: 3344
     
     spring:
       application:
         name: cloud-config-center
       cloud:
         config:
           server:
             git:
               #github上的仓库地址
               uri: git@github.com:wugongxun/springcloud-config.git
               username: 2297665453@qq.com
               password: w1999g1117x
               search-paths:
                 - springcloud-config
             default-label: master
     
     eureka:
       client:
         service-url:
           defaultZone: http://eureka7001.com:7001/eureka
       instance:
         instance-id: config3344
         prefer-ip-address: true
     ```

   - 启动类

     ```java
     @SpringBootApplication
     @EnableEurekaClient
     @EnableConfigServer
     public class CloudConfigCenter3344 {
         public static void main(String[] args) {
             SpringApplication.run(CloudConfigCenter3344.class, args);
         }
     }
     ```

   - 访问路径http://config-3344.com:3344/master/config-dev.yml

     - /{label}/{application}-{profile}.yml
     - /{application}-{profile}.yml
     - /{application}/{profile}[/{label}]
   
3. Config客户端配置

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-config</artifactId>
     </dependency>
     ```

   - 创建bootstrap.yaml

     application.yaml是用户级资源配置项

     bootstrap.yaml是系统级，优先级更高

     Spring Cloud会创建一个"Bootstrap Context'”,作为Spring应用的Application Context的父上下文。初始化的时候，BootstrapContext负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的Environment。

     Bootstrap属性有高优先级，默认情况下，它们不会被本地配置覆盖。Bootstrap context和Application Context有着不同的约定,所以新增了一个bootstrap,yml文件，保证Bootstrap Context和Application Context配置的分离。

     ```yaml
     server:
       port: 3355
     
     spring:
       application:
         name: cloud-config-client
       cloud:
         #Config客户端配置
         config:
           label: master #分支名称
           name: config #配置文件名称
           profile: dev #读取后缀名称
           #最终访问路径为/master/config-dev.yml
           uri: http://localhost:3344 #配置中心地址
           
     eureka:
       client:
         service-url: 
           defaultZone: http://eureka7001.com:7001/eureka
       instance:
         instance-id: config3355
         prefer-ip-address: true
     ```

   - 编写测试controller

     ```java
     @RestController
     public class ConfigController {
         @Value("${config.info}")
         private String configInfo;
     
         @GetMapping("/configInfo")
         public String getConfigInfo() {
             return configInfo;
         }
     }
     ```

4. 客户端动态刷新

   - 必须添加actuator，并暴露端点

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-actuator</artifactId>
     </dependency>
     ```

     ```yaml
     management:
       endpoints:
         web:
           exposure:
             include: "*"
     ```

   - 在业务类上添加@RefreshScope注解

     ```java
     @RestController
     @RefreshScope
     public class ConfigController {
         @Value("${config.info}")
         private String configInfo;
     
         @GetMapping("/configInfo")
         public String getConfigInfo() {
             return configInfo;
         }
     }
     ```

   - 修改配置之后访问curl -X POST "http://localhost:3355/actuator/refresh"刷新

# 十三，Bus

1. Bus是什么

   Spring Cloud Bus,是用来将分布式系统的节点与轻量级消息系统链接起来的框架，它整合了Java的事件处理机制和消息中间件的功能。Spring Clud Bus目前支持RabbitMQ和Kafka。

2. 什么是总线

   在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线。在总线上的各个实例，都可以方便地广播一些需要让其他连接在该主题上的实例都知道的消息。

3. 全局广播配置实现

   - Config配置总控中心添加依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-bus-amqp</artifactId>
     </dependency>
     ```

   - Config配置总控中心添加配置

     ```yaml
     spring:
       #rabbitmq相关配置
       rabbitmq:
         host: 47.100.85.66
         port: 5672
         username: admin
         password: wgx
     #暴露bus刷新配置端点
     management:
       endpoints:
         web:
           exposure:
             include: 'bus-refresh'
     ```

   - Config客户端添加依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-bus-amqp</artifactId>
     </dependency>
     ```

   - Config客户端添加配置

     ```yaml
     spring:
       #rabbitmq相关配置
       rabbitmq:
         host: 47.100.85.66
         port: 5672
         username: admin
         password: wgx
     ```

   - 发送一个post请求

     curl -X POST "http://localhost:3344/actuator/bus-refresh"

4. 定点通知

   curl -X POST "http://localhost:3344/actuator/bus-refresh/cloud-config-client:3355"

# 十四，Stream

1. 是什么

   Spring Cloud Stream是一个构建消息驱动微服务的框架。

   应用程序通过inputs或者outputs来与Spring Cloud Stream中binder对象交互。通过我们配置来binding(绑定)，而Spring Cloud Stream的binder对象负责与消息中间件交互。所以我们只需要搞清楚如何与Spring Cloud Stream交互就可以方便使用消息驱动的方式。

   通过使用Spring Integration来连接消息代理中间件以实现消息事件驱动缓冲Spring Cloud Stream为一些供应商的消息中间件产品提供了个性化的自动化配置实现，引用了发布-订阅、消费组、分区的三个核心概念。

2. Binder

   在没有绑定器这个概念的情况下，我们的Spring Boot应用要直接与消息中间件进行信息交互的时候，由于各消息中间件构建的初衷不同，它们的实现细节上会有较大的差异性，通过定义绑定器作为中间层，完美地实现了应用程序与消息中间件细节之间的隔离。Stream对消息中间件的进一步封装可以做到代码层面对中间件的无感知，甚至于动态的切换中间件rabbitmq切换为kafka),使得微服务开发的高度解耦，服务可以关注更多自己的业务流程

3. 消息生产者

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
     </dependency>
     ```

   - application.yaml

     ```yaml
     server:
       port: 8801
     
     spring:
       application:
         name: cloud-stream-provider8801
       rabbitmq:
         host: 47.100.85.66
         port: 5672
         username: admin
         password: wgx
       cloud:
         stream:
           #配置要绑定的rabbitmq的服务信息
           binders:
             defaultRabbit: #表示定义的名称，用于binding整合
               type: rabbit #消息组件类型
           bindings: #服务整合处理
             output: #这个名字是一个通道的名称
               destination: studyExchange #表示要使用的Exchange名称定义
               content-type: application/json #设置消息类型，本次为json，文本则设置“text/plain”
               binder: defaultRabbit #设置要绑定的消息服务的具体设置
     
     eureka:
       client:
         service-url:
           defaultZone: http://eureka7001.com:7001/eureka
       instance:
         prefer-ip-address: true
         instance-id: stream-provider8801
     ```
     
   - service

     ```java
     @Slf4j
     @EnableBinding(Source.class)//定义消息的推送管道
     public class MessageProviderImpl implements IMessageProvider {
     
         @Resource
         private MessageChannel output;
     
         @Override
         public String send() {
             String serial = UUID.randomUUID().toString();
             output.send(MessageBuilder.withPayload(serial).build());
             log.info("serial:{}", serial);
             return serial;
         }
     }
     ```
   
   - controller

     ```java
     @RestController
     public class SendMessageController {
         @Resource
         private IMessageProvider messageProvider;
     
         @GetMapping("/sendMessage")
         public String sendMessage() {
             return messageProvider.send();
         }
     }
     ```
   
4. 消息消费者

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
     </dependency>
     ```

   - application.yaml

     ```yaml
     server:
       port: 8802
     
     spring:
       application:
         name: cloud-stream-consumer8802
       rabbitmq:
         host: 47.100.85.66
         port: 5672
         username: admin
         password: wgx
       cloud:
         stream:
           #配置要绑定的rabbitmq的服务信息
           binders:
             defaultRabbit: #表示定义的名称，用于binding整合
               type: rabbit #消息组件类型
           bindings: #服务整合处理
             input: #这个名字是一个通道的名称
               destination: studyExchange #表示要使用的Exchange名称定义
               content-type: application/json #设置消息类型，本次为json，文本则设置“text/plain”
               binder: defaultRabbit #设置要绑定的消息服务的具体设置
     
     eureka:
       client:
         service-url:
           defaultZone: http://eureka7001.com:7001/eureka
       instance:
         prefer-ip-address: true
         instance-id: stream-consumer8802
     ```

   - controller

     ```java
     @Slf4j
     @Component
     @EnableBinding(Sink.class)
     public class ReceiveMessageController {
         @Value("${server.port}")
         private String serverPort;
     
         @StreamListener(Sink.INPUT)
         public void input(Message<String> message) {
             log.info("消费者受到消息:{}, server.port={}", message.getPayload(), serverPort);
         }
     }
     ```

5. 分组消费与持久化

   解决重复消费，同一组不会重复消费

   修改application.yaml

   ```yaml
   spring:
     application:
       name: cloud-stream-consumer
     rabbitmq:
       host: 47.100.85.66
       port: 5672
       username: admin
       password: wgx
     cloud:
       stream:
         #配置要绑定的rabbitmq的服务信息
         binders:
           defaultRabbit: #表示定义的名称，用于binding整合
             type: rabbit #消息组件类型
         bindings: #服务整合处理
           input: #这个名字是一个通道的名称
             destination: studyExchange #表示要使用的Exchange名称定义
             content-type: application/json #设置消息类型，本次为json，文本则设置“text/plain”
             binder: defaultRabbit #设置要绑定的消息服务的具体设置
             group: A #分组
   ```

# 十五，Sleuth

1. 是什么

   Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案，在分布式系统中提供追踪解决方案并且支持了zipkin

2. 下载安装zipkin，运行

3. 访问http://127.0.0.1:9411/

4. Trace：类似于树结构的Span集合，表示一条调用链路，存在唯一标识

5. Span：表示调用链路来源，Span就是一次请求信息

6. 在需要监控的服务中引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-zipkin</artifactId>
   </dependency>
   ```

7. 修改application.yaml

   ```yaml
   spring:
     application:
       name: cloud-payment-service
     zipkin:
       base-url: http://localhost:9411
     sleuth:
       sampler:
         #采样率值鉴于0到1之间，1则表示全部采集
         probability: 1
   ```

8. 访问http://127.0.0.1:9411/查看

   ![](F:\截图\屏幕截图 2022-06-08 231302.png)

# 十六，Nacos

1. 下载解压，运行startup.cmd -m standalone

2. 访问http://localhost:8848/nacos，默认账号密码都是nacos

   ![](F:\截图\屏幕截图 2022-06-09 201251.png)

## 服务注册

1. 服务提供者注册

   - 引入依赖

     ```xml
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
     </dependency>
     ```

   - application.yaml

     ```yaml
     server:
       port: 9001
     
     spring:
       application:
         name: payment-provider-nacos
       cloud:
         nacos:
           discovery:
             server-addr: localhost:8848
     
     management:
       endpoints:
         web:
           exposure:
             include: "*"
     ```

   - 启动类

     ```java
     @SpringBootApplication
     @EnableDiscoveryClient
     public class CloudalibabaProviderPayment9001 {
         public static void main(String[] args) {
             SpringApplication.run(CloudalibabaProviderPayment9001.class, args);
         }
     }
     ```

2. 消费者注册

   - 引入依赖

     ```xml
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
     </dependency>
     ```

   - application.yaml

     ```yaml
     server:
       port: 80
     
     spring:
       application:
         name: order-consumer-nacos
       cloud:
         nacos:
           discovery:
             server-addr: localhost:8848
     service-url:
       nacos-user-service: http://payment-provider-nacos
     
     management:
       endpoints:
         web:
           exposure:
             include: "*"
     ```

   - 启动类

     ```java
     @SpringBootApplication
     @EnableDiscoveryClient
     public class CloudalibabaConsumerOrder80 {
         public static void main(String[] args) {
             SpringApplication.run(CloudalibabaConsumerOrder80.class, args);
         }
     }
     ```

   - ApplicationContextConfig

     ```java
     @Configuration
     public class ApplicationContextConfig {
         @Bean
         @LoadBalanced
         public RestTemplate getRestTemplate() {
             return new RestTemplate();
         }
     }
     ```

## 配置中心

1. 配置中心

   - 引入依赖

     ```xml
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
     </dependency>
     ```

   - bootstrap.yaml

     ```yaml
     server:
       port: 3377
     
     spring:
       application:
         name: config-client-nacos
       cloud:
         nacos:
           discovery:
             server-addr: localhost:8848
           config:
             server-addr: localhost:8848 #Nacos作为配置中心的地址
             file-extension: yaml #指定yaml格式的配置
             #group: DEV_GROUP #指定组
     ```

   - application.yaml

     ```yaml
     spring:
       profiles:
         active: dev #表示开发环境
     ```

   - controller

     ```java
     @RestController
     @RefreshScope
     public class ConfigClientController {
         @Value("${config.info}")
         private String configInfo;
     
         @GetMapping("/configInfo")
         public String getConfigInfo() {
             return configInfo;
         }
     }
     ```

   - 在nacos配置中心添加配置

     格式为：${prefix}-${spring.profiles.active}.${file-extension}

     **prefix 默认为 spring.application.name 的值，也可以通过配置项 spring.cloud.nacos.config.prefix来配置。**

     ![](F:\截图\屏幕截图 2022-06-09 213404.png)

2. Namespace，Group，Cluster

   ![](F:\截图\屏幕截图 2022-06-10 173917.png)

   - Nacos默认的命名空间是public，Namespace主要用来实现隔离。比方说我们现在有三个环境：开发、测试、生产环境，我们就可以创建三个Namespace,不同的Namespace之间是隔离的。
   - Group默认是DEFAULT GROUP,Group可以把不同的微服务划分到同一个分组里面去
   - Service就是微服务；一个Service可以包含多个Cluster(集群)，Nacos默认Cluster是DEFAULT,Cluster是对指定微服务的一个虚拟划分。比方说为了容灾，将Service微服务分别部署在了杭州机房和广州机房，这时就可以给杭州机房的Service微服务起一个集群名称(HZ)，给广州机房的Sevⅵce微服务起一个集群名称(GZ)，还可以尽量让同一个机房的微服务互相调用，以提升性能。
   - 最后是Instance,就是微服务的实例。

## 集群和持久化配置

1. 持久化

   - 使用nacos的conf目录中的nacos-mysql.sql在MySQL数据库中创建相应的表

   - 修改application.properties

     ![](F:\截图\屏幕截图 2022-06-10 183019.png)

2. 集群

   - 复制cluster.conf.example为cluster.conf

     cp cluster.conf.example cluster.conf

   - 修改cluster.conf

     ![](F:\截图\屏幕截图 2022-06-10 213116.png)

   - 修改startup.sh脚本，使其可以接受不同的端口号启动不同的nacos实例

     ![](F:\截图\屏幕截图 2022-06-10 215527.png)

     ![](F:\截图\屏幕截图 2022-06-10 220219.png)

     ![](F:\截图\屏幕截图 2022-06-10 220507.png)

   - 修改application.properties中的server.port
   
   - 修改nginx.conf
   
     ![](F:\截图\屏幕截图 2022-06-10 235943.png)

# 十七，Sentinel

1. 下载sentinel的jar包
2. 启动sentinel，==默认端口8080==
3. 访问localhost:8080

## 初始化监控工程

1. 引入依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.csp</groupId>
       <artifactId>sentinel-datasource-nacos</artifactId>
   </dependency>
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   </dependency>
   ```

2. application.yaml

   ```yaml
   server:
     port: 8401
   
   spring:
     application:
       name: cloudalibaba-sentinel-service
     cloud:
       nacos:
         discovery:
           server-addr: 47.100.85.66:8848
       sentinel:
         transport:
           #配置Sentinel dashboard地址
           dashboard: localhost:8080
           #默认8719端口，假如被占用会依次从8719开始依次加一扫描，直到找到未被占用的端口
           port: 8719
           
   management:
     endpoints:
       web:
         exposure:
           include: "*"
   ```

## 流控规则

- 资源名：唯一名称，默认请求路径
- 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default(不区分来源)
- 阈值类型单机阈值：
  - QPS(每秒钟的请求数量)：当调用该api的QPS达到阈值的时候，进行限流
  - 线程数：当调用该api的线程数达到阔值的时候，进行限流
- 是否集群：不需要集群
- 流控模式：
  - 直接：api达到限流条件时，直接限流。
  - 关联：当关联的资源达到阔值时，就限流自己
  - 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阔值，就进行限流）【api级别的针对来源】
- 流控效果：
  - 快速失败：直接失败，抛异常
  - Warm Up：根据codeFactor(冷加载因子，默认3)的值，从阈值/codeFactor,经过预热时长，才达到设置的QPS阈值
  - 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效

1. 直接->快速失败

   ![](F:\截图\屏幕截图 2022-06-12 214447.png)

2. 关联

   ![](F:\截图\屏幕截图 2022-06-12 220352.png)

3. 链路

   - 编写一个service

     ```java
     @Service
     public class FlowLimitService {
         //定义资源doSomething
         @SentinelResource(value = "doSomething", blockHandler = "handlerException")
         public void doSomething() {
             System.out.println("do something...");
         }
         public String handlerException(BlockException ex) {
             return ex.getClass().getCanonicalName();
         }
     }
     ```

   - controller调用doSomething方法

     ```java
     @RestController
     public class FlowLimitController {
         @Resource
         private FlowLimitService flowLimitService;
     
         @GetMapping("/testA")
         public String testA() {
             flowLimitService.doSomething();
             return "A";
         }
         @GetMapping("/testB")
         public String testB() {
             flowLimitService.doSomething();
             return "B";
         }
     }
     ```

   - 配置CommonFilter.WEB_CONTEXT_UNIFY=false，入口资源关闭聚合

     ```java
     @Configuration
     public class FilterContextConfig {
         @Bean
         public FilterRegistrationBean sentinelFilterRegistration() {
             FilterRegistrationBean registration = new FilterRegistrationBean();
             registration.setFilter(new CommonFilter());
             registration.addUrlPatterns("/*");
             //入口资源关闭聚合
             registration.addInitParameter(CommonFilter.WEB_CONTEXT_UNIFY, "false");
             registration.setName("sentinelFilter");
             registration.setOrder(1);
             return registration;
         }
     }
     ```

   - 将/testA为入口资源的doSomething添加链路流控

     ![](F:\截图\屏幕截图 2022-06-12 230629.png)

     ![](F:\截图\屏幕截图 2022-06-12 230656.png)

## 流控效果

1. 预热

   阈值除以coldFactor(默认为3)，经过预热时长后才会达到阈值

   ![](F:\截图\屏幕截图 2022-06-13 201815.png)

2. 排队等待

   匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效

   ![](F:\截图\屏幕截图 2022-06-13 202944.png)

## 服务降级

- RT（平均响应时间，秒级）

  平均响应时间超出阈值且在时间窗口内通过的请求大于等于5，两个条件同时满足后触发降级；窗口期过后关闭断路器

  RT最大4900（更大需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效）

- 异常比例（秒级）

  QPS大于等于5，且异常比例（秒级统计）超过阈值时，触发降级；时间窗口结束后关闭降级

- 异常数（分钟级）

  异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后关闭降级

1. 慢调用比例

   ![](F:\截图\屏幕截图 2022-06-13 210449.png)

2. 异常比例

   ![](F:\截图\屏幕截图 2022-06-13 211755.png)

3. 异常数

   ![](F:\截图\屏幕截图 2022-06-13 212545.png)

## 热点key

热点即为经常访问的数据，很多时候我们希望某个热点数据中访问频次最高的Top K数据，并对其访问进行限制，比如：

- 商品ID为参数，统计一段时间内最常购买的商品ID并进行限制
- 用户ID为参数，统计一段时间内最频繁访问的用户ID进行限制

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流，热点参数限流可以看做是一种特殊的流量控制的资源调用

1. controller

   ```java
   @GetMapping("/testHotKey")
   @SentinelResource(value = "testHotKey", blockHandler = "hotKeyExceptionHandler")
   public String testHotKey(@RequestParam(value = "p1", required = false) String p1,
                            @RequestParam(value = "p2", required = false) String p2) {
       return "testHotKey, O(∩_∩)O";
   }
   
   public String hotKeyExceptionHandler(String p1, String p2, BlockException exception) {
       return "hotKeyExceptionHandler, o(╥﹏╥)o";
   }
   ```

2. 配置

   ![](F:\截图\屏幕截图 2022-06-14 210701.png)

3. 参数列外项

   当参数值为5的时候，QPS可以达到200

   ![](F:\截图\屏幕截图 2022-06-14 212048.png)

## 系统规则

系统保护规则是从应用级别的入口流量进行控制，从单台机器的Ioad、CPU使用率、平均RT、入口QPS和并发线程数等几个维度监控应用指标，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。系统保护规则是应用整体维度的，而不是资源维度的，并且仅对入口流量生效。入口流量指的是进入应用的流量(EntryType.IN),比如Web服务或Dubbo服务端接收的清求，都属于入口流量。系统规则支持以下的模式：

- Load自适应（仅对Linux/Unix-like机器生效）：系统的load1作为启发指标，进行自适应系统保护。当系统load1超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护(BBR阶段)。系统容量由系统的maxQps * minRt估算得出。设定参考值一般是CPU cores * 2.5
- CPU usage(1.5.0+版本)：当系统CPU使用率超过阈值即触发系统保护（取值范围(0.0-1.0),比较灵敏。
- 平均RT：当单台机器上所有入口流量的平均RT达到阔值即触发系统保护，单位是毫秒。
- 并发线程数：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
- 入口QPS:当单台机器上所有入口流量的QPS达到阈值即触发系统保护。

![](F:\截图\屏幕截图 2022-06-14 213400.png)

## SentinelResource

1. 全局降级

   - 编写一个全局的handler

     ```java
     public class GlobalExceptionHandler {
         public static CommonResult globalExceptionHandler1(BlockException exception) {
             return new CommonResult(444, exception.getMessage() + "\tglobalExceptionHandler1");
         }
         public static CommonResult globalExceptionHandler2(BlockException exception) {
             return new CommonResult(444, exception.getMessage() + "\tglobalExceptionHandler2");
         }
     }
     ```

   - controller

     ```java
     @GetMapping("/globalExceptionHandler")
     @SentinelResource(value = "globalExceptionHandler",
             blockHandlerClass = GlobalExceptionHandler.class,
             blockHandler = "globalExceptionHandler1")
     //GlobalExceptionHandler中的globalExceptionHandler1方法
     public CommonResult globalExceptionHandler() {
         return new CommonResult(200, "ok", new Payment(1L, "wgx001"));
     }
     ```

## 服务熔断

1. 环境搭建

   搭建服务提供者cloudalibaba-provider-payment9003，cloudalibaba-provider-payment9004以及服务调用者cloudalibaba-consumer-nacos-order-80

2. CircleBreakerController无配置

   ```java
   @Slf4j
   @RestController
   @RequestMapping("/consumer")
   public class CircleBreakerController {
       @Value("${service-url.nacos-user-service}")
       private static String SERVICE_URL;
   
       @Resource
       private RestTemplate restTemplate;
   
       @GetMapping("/fallback/{id}")
       @SentinelResource(value = "fallback")
       public CommonResult<Payment> fallback(@PathVariable("id") Long id) {
           CommonResult result = restTemplate.getForObject(SERVICE_URL + "/get/" + id, CommonResult.class);
           if (id == 6) {
               throw new IllegalArgumentException("非法的参数, o(╥﹏╥)o");
           } else if (result.getData() == null) {
               throw new NullPointerException("未查询到记录, o(╥﹏╥)o");
           }
           return result;
       }
   }
   ```

3. fallback

   ```java
   @Slf4j
   @RestController
   @RequestMapping("/consumer")
   public class CircleBreakerController {
       @Value("${service-url.nacos-user-service}")
       private String serverURL;
   
       @Resource
       private RestTemplate restTemplate;
   
       @GetMapping("/fallback/{id}")
       @SentinelResource(value = "fallback", fallback = "fallbackHandler")
       public CommonResult<Payment> fallback(@PathVariable("id") Long id) {
           CommonResult result = restTemplate.getForObject(serverURL + "/payment/get/" + id, CommonResult.class);
           if (id == 6) {
               throw new IllegalArgumentException("非法的参数, o(╥﹏╥)o");
           } else if (result.getData() == null) {
               throw new NullPointerException("未查询到记录, o(╥﹏╥)o");
           }
           return result;
       }
   
       public CommonResult<Payment> fallbackHandler(@PathVariable("id") Long id, Throwable e) {
           return new CommonResult<>(444, "fallbackHandler:" + e.getMessage());
       }
   }
   ```

4. blockHandler

   ```java
   @Slf4j
   @RestController
   @RequestMapping("/consumer")
   public class CircleBreakerController {
       @Value("${service-url.nacos-user-service}")
       private String serverURL;
   
       @Resource
       private RestTemplate restTemplate;
   
       @GetMapping("/fallback/{id}")
       @SentinelResource(value = "fallback", blockHandler = "blockHandler")
       public CommonResult<Payment> fallback(@PathVariable("id") Long id) {
           CommonResult result = restTemplate.getForObject(serverURL + "/payment/get/" + id, CommonResult.class);
           if (id == 6) {
               throw new IllegalArgumentException("非法的参数, o(╥﹏╥)o");
           } else if (result.getData() == null) {
               throw new NullPointerException("未查询到记录, o(╥﹏╥)o");
           }
           return result;
       }
   
       public CommonResult<Payment> blockHandler(@PathVariable("id") Long id, BlockException e) {
           return new CommonResult<>(444, "blockHandler:" + e.getMessage());
       }
   }
   ```

5. 若blockHandler和fallback都进行了配置，则被限流降级而抛出的BlockException时只会进入blockHandler处理

6. exceptionToIgnore异常忽略

   ```java
   @SentinelResource(value = "fallback", fallback = "fallbackHandler", blockHandler = "blockHandler",
                   exceptionsToIgnore = {IllegalArgumentException.class})
   ```

## Sentinel整合Feign

1. 添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   ```

2. 在application.yaml中开启feign对sentinel的支持

   ```yaml
   feign:
     sentinel:
       enabled: true
   ```

3. 启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   @EnableFeignClients
   public class CloudalibabaConsumerNacosOrder80 {
       public static void main(String[] args) {
           SpringApplication.run(CloudalibabaConsumerNacosOrder80.class, args);
       }
   }
   ```

4. PaymentService

   ```java
   @FeignClient(value = "cloudalibaba-provider-payment", path = "/payment", fallback = PaymentFallbackService.class)
   public interface PaymentService {
       @GetMapping(value = "/get/{id}")
       CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
   }
   ```

5. PaymentFallbackService

   ```java
   @Component
   public class PaymentFallbackService implements PaymentService{
       @Override
       public CommonResult<Payment> getPaymentById(Long id) {
           return new CommonResult<>(444, "服务降级");
       }
   }
   ```

6. controller

   ```java
   @Resource
   private PaymentService paymentService;
   
   @GetMapping("/payment/get/{id}")
   public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
       return paymentService.getPaymentById(id);
   }
   ```

## 持久化

1. 添加依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.csp</groupId>
       <artifactId>sentinel-datasource-nacos</artifactId>
   </dependency>
   ```

2. application.yaml

   ```yaml
   server:
     port: 8401
   
   spring:
     application:
       name: cloudalibaba-sentinel-service
     cloud:
       nacos:
         discovery:
           server-addr: 47.100.85.66:8848
       sentinel:
         datasource:
           ds1:
             nacos:
               server-addr: 47.100.85.66:8848
               dataId: cloudalibaba-sentinel-service
               groupId: DEFAULT_GROUP
               data-type: json
               rule-type: flow
         transport:
           #配置Sentinel dashboard地址
           dashboard: localhost:8080
           #默认8719端口，假如被占用会依次从8719开始依次加一扫描，直到找到未被占用的端口
           port: 8719
         web-context-unify: false
   ```

3. 在nacos中添加配置

   ![](F:\截图\屏幕截图 2022-06-17 220227.png)

   ```json
   [
       {
   		"resource": "byResource",
       	"limitApp": "default",
       	"grade": 1,
       	"count": 1,
       	"strategy": 0,
       	"controlBehavior": 0,
       	"clusterMode": false
   	}
   ]
   ```

   - resource：资源名称
   - limitApp：来源应用
   - grade：阈值类型，0表示线程数，1表示QPS
   - count：单机阈值
   - strategy：流控模式，0表示直接，1表示关联，2表示链路
   - controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待
   - clusterMode：是否集群

# 十八，Seata

1. 术语

   - Transaction ID XID：全局唯一的事务ID

   - TC (Transaction Coordinator) - 事务协调者：维护全局和分支事务的状态，驱动全局事务提交或回滚。
   - TM (Transaction Manager) - 事务管理器：定义全局事务的范围：开始全局事务、提交或回滚全局事务。
   - RM (Resource Manager) - 资源管理器：管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

2. 执行流程

   ![](F:\截图\屏幕截图 2022-06-17 223337.png)

   - TM向TC申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的XID
   - XID在微服务调用的链路的上下文中传播
   - RM向TC注册分支事务，将其纳入XID对应全局事务的管辖
   - TM向TC发起针对XID的全局提交或回滚决议
   - TC调度XID下管辖的全部分支事务完成提交或回滚请求

3. seata服务端

   - 下载安装

   - 修改application.yaml

     ```yaml
     server:
       port: 7091
     
     spring:
       application:
         name: seata-server
     
     logging:
       config: classpath:logback-spring.xml
       file:
         path: ${user.home}/logs/seata
       extend:
         logstash-appender:
           destination: 127.0.0.1:4560
         kafka-appender:
           bootstrap-servers: 127.0.0.1:9092
           topic: logback_to_logstash
     
     console:
       user:
         username: seata
         password: seata
     
     seata:
       config:
         # support: nacos, consul, apollo, zk, etcd3
         type: nacos
         nacos:
           server-addr: 47.100.85.66:8848
           group: DEFAULT_GROUP
           data-id: seataServer.properties
           namespace: ""
           username: "nacos"
           password: "nacos"
       registry:
         # support: nacos, eureka, redis, zk, consul, etcd3, sofa
         type: nacos
         nacos:
           application: seata-server
           server-addr: 47.100.85.66:8848
           group: DEFAULT_GROUP
           namespace: ""
           username: "nacos"
           password: "nacos"
       store:
         # support: file 、 db 、 redis
         mode: db
         # db:
         #   datasource: dbcp
         #   db-type: mysql
         #   driver-class-name: com.mysql.jdbc.Driver
         #   url: jdbc:mysql://127.0.0.1:3306/seata?characterEncoding=utf8&rewriteBatchedStatements=true&useSSL=false
         #   user: root
         #   password: wgx
     #  server:
     #    service-port: 8091 #If not configured, the default is '${server.port} + 1000'
       security:
         secretKey: SeataSecretKey0c382ef121d778043159209298fd40bf3850a017
         tokenValidityInMilliseconds: 1800000
         ignore:
           urls: /,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-fe/public/**,/api/v1/auth/login
     ```

   - 在nacos上添加config（示例代码可以在官网上复制）

     ![](F:\截图\屏幕截图 2022-06-18 230932.png)

4. seata客户端

   - 引入依赖

     ```xml
     <!--排除spring-cloud-starter-alibaba-seata自带的客户端依赖，手动引入1.5.1-->
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
         <exclusions>
             <exclusion>
                 <groupId>io.seata</groupId>
                 <artifactId>seata-spring-boot-starter</artifactId>
             </exclusion>
         </exclusions>
     </dependency>
     <dependency>
         <groupId>io.seata</groupId>
         <artifactId>seata-spring-boot-starter</artifactId>
         <version>1.5.1</version>
     </dependency>
     ```

   - application.yaml

     ```yaml
     server:
       port: 2001
     
     seata:
       enabled: true
       application-id: ${spring.application.name}
       tx-service-group: default_tx_group
       config:
         type: nacos
         nacos:
           server-addr: 47.100.85.66:8848
           group: DEFAULT_GROUP
           data-id: seataServer.properties
           username: nacos
           password: nacos
       registry:
         type: nacos
         nacos:
           application: seata-server
           server-addr: 47.100.85.66:8848
           group: DEFAULT_GROUP
           username: nacos
           password: nacos
     
     spring:
       application:
         name: cloudalibaba-seata-order
       cloud:
         nacos:
           discovery:
             server-addr: 47.100.85.66:8848
             group: DEFAULT_GROUP
       datasource:
         url: jdbc:mysql://localhost:3306/seata_order?characterEncoding=utf8&rewriteBatchedStatements=true&useSSL=false
         username: root
         password: wgx
         type: com.alibaba.druid.pool.DruidDataSource
         driver-class-name: com.mysql.jdbc.Driver
     
     feign:
       hystrix:
         enabled: false
     
     logging:
       level:
         io:
           seata: info
     
     mybatis:
       mapper-locations: classpath:mapper/*.xml
       type-aliases-package: com.wgx.springcloud.bean
     
     mybatis-plus:
       configuration:
         lazy-loading-enabled: true
       #    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
       mapper-locations: classpath:mapper/*.xml
     
     management:
       endpoints:
         web:
           exposure:
             include: "*"
     ```

   - 在需要的业务上添加注解@GlobalTransactional

5. 原理介绍

   - 在一阶段，Seata会拦截“业务SQL”

     1. 解析SQL语义，找到业务SQL要更新的业务数据，在业务更新之前将其保存成“before image”
     2. 执行业务SQL，更新业务数据，在业务数据更新之后将其保存成”after image“，最后生成行锁

     以上的操作全部在一个数据库事务内完成，这样保证了一阶段的原子性

   - 二阶段提交

     因为业务SQL在一阶段已经提交至数据库，所以seata只需要将一阶段保存的快照数据和行锁删掉，完成数据的清理即可

   - 二阶段回滚
     二阶段如果是回滚的话，Seata就需要回滚一阶段已经执行的“业务SQL”，还原业务数据。回滚方式便是用“before image”还原业务数据；但在还原前要首先要校验脏写，对比“数据库当前业务数据”和“after image”，如果两份数据完全一致就说明没有脏写，可以还原业务数据，如果不一致就说明有脏写，出现脏写就需要转人工处理。
