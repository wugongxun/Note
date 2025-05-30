# 一，RPC

RPC【Remote Procedure Call】是指远程过程调用，是一种进程间通信方式，他是一种技术的思想，而不是规范。它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。

![](F:\截图\屏幕截图 2023-08-22 172246.png)

# 二，环境搭建

## 搭建Zookeeper

1. 创建docker网络（容器间使用自定义网络联通）

   ```linux
   docker network create zk
   ```

2. 运行Zookeeper镜像

   ```linux
   docker run -d --name zookeeper --privileged=true -p 2181:2181 --network zk --network-alias zk -v /home/wgx/zookeeper/data:/data -v /home/wgx/zookeeper/conf:/conf -v /home/wgx/zookeeper/logs:/datalog zookeeper:3.5.7
   ```

## 搭建dubbo-admin

1. 修改配置文件

   ```linux
   admin.registry.address=zookeeper://zk:2181
   admin.config-center=zookeeper://zk:2181
   admin.metadata-report.address=zookeeper://zk:2181
   ```

2. 运行dubbo-admin镜像

   ```linux
   docker run --name dubbo-admin -p 38080:38080 -v /home/wgx/dubbo-admin/application.properties:/application.properties --network zk -d apache/dubbo-admin
   ```

## 搭建common

1. entity

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class User implements Serializable {
       private Long id;
       private String name;
       private Integer age;
   }
   ```

2. service

   ```java
   public interface UserService {
       List<User> getUsers();
   }
   ```

## 搭建服务提供者

1. 在服务提供者中引入dubbo和Zookeeper客户端

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>dubbo</artifactId>
       <version>2.6.2</version>
   </dependency>
   
   <dependency>
       <groupId>org.apache.curator</groupId>
       <artifactId>curator-framework</artifactId>
       <version>2.13.0</version>
   </dependency>
   ```

2. 编写provider.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
          xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
   
       <!-- 提供方应用信息，用于计算依赖关系 -->
       <dubbo:application name="User-service"  />
   
       <!-- 使用multicast广播注册中心暴露服务地址 -->
       <dubbo:registry address="zookeeper://192.168.200.128:2181" />
   
       <!-- 用dubbo协议在20880端口暴露服务 -->
       <dubbo:protocol name="dubbo" port="20880" />
   
       <!-- 声明需要暴露的服务接口 -->
       <dubbo:service interface="com.wgx.common.service.UserService" ref="userService" />
   
       <!-- 和本地bean一样实现服务 -->
       <bean id="userService" class="com.wgx.userservice.service.impl.UserServiceImpl" />
   </beans>
   ```

3. 编写启动类

   ```java
   public class Provider {
       public static void main(String[] args) throws IOException {
           ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("provider.xml");
           context.start();
           System.in.read(); // 按任意键退出
       }
   }
   ```

## 搭建服务消费者

1. 在服务消费者中引入dubbo和Zookeeper客户端

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>dubbo</artifactId>
       <version>2.6.2</version>
   </dependency>
   
   <dependency>
       <groupId>org.apache.curator</groupId>
       <artifactId>curator-framework</artifactId>
       <version>2.13.0</version>
   </dependency>
   ```

2. 编写 consumer.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
          xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
   
       <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
       <dubbo:application name="order-service"  />
   
       <!-- 使用zookeeper注册中心暴露发现服务地址 -->
       <dubbo:registry address="zookeeper://192.168.200.128:2181" />
   
       <!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
       <dubbo:reference id="userService" interface="com.wgx.common.service.UserService" />
   </beans>
   ```

3. 编写启动类，并调用提供者的接口

   ```java
   public static void main(String[] args) {
       ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"META-INF/spring/dubbo-demo-consumer.xml"});
       context.start();
       UserService userService = (UserService) context.getBean("userService"); // 获取远程服务代理
       List<User> users = userService.getUsers();// 执行远程方法
       System.out.println(users); // 显示调用结果
   }
   ```

# 三，整合SpringBoot

## 创建服务提供者

1. 引入依赖

   ```xml
   <dependency>
       <groupId>com.wgx</groupId>
       <artifactId>common</artifactId>
       <version>1.0-SNAPSHOT</version>
   </dependency>
   
   <!-- dubbo -->
   <dependency>
       <groupId>org.apache.dubbo</groupId>
       <artifactId>dubbo-spring-boot-starter</artifactId>
       <version>3.2.0</version>
   </dependency>
   
   <dependency>
       <groupId>org.apache.dubbo</groupId>
       <artifactId>dubbo-registry-zookeeper</artifactId>
       <version>3.2.0</version>
   </dependency>
   ```

2. 编写Service实现类

   ```java
   @DubboService
   public class UserServiceImpl implements UserService {
       @Override
       public List<User> getUsers() {
           return Arrays.asList(new User(1L, "wgx", 24), new User(2L, "WGX", 24));
       }
   }
   ```

3. application.yml

   ```yaml
   server:
     port: 8080
   
   dubbo:
     application:
       name: dubbo-boot-provider
     protocol:
       name: dubbo
       port: -1
     registry:
       address: zookeeper://${zookeeper.address:127.0.0.1}:2181
   
   zookeeper:
     address: 47.113.148.39
   ```

## 创建服务消费者

1. 引入依赖

   ```xml
   <dependency>
       <groupId>com.wgx</groupId>
       <artifactId>common</artifactId>
       <version>1.0-SNAPSHOT</version>
   </dependency>
   
   <!-- dubbo -->
   <dependency>
       <groupId>org.apache.dubbo</groupId>
       <artifactId>dubbo-spring-boot-starter</artifactId>
       <version>3.2.0</version>
   </dependency>
   
   <dependency>
       <groupId>org.apache.dubbo</groupId>
       <artifactId>dubbo-registry-zookeeper</artifactId>
       <version>3.2.0</version>
   </dependency>
   ```

2. 调用服务

   ```java
   @RestController
   public class OrderController {
       @DubboReference(check = false)
       private UserService userService;
   
       @GetMapping("/getUsers")
       public List<User> getUsers() {
           return userService.getUsers();
       }
   }
   ```

3. application.yml

   ```yaml
   server:
     port: 8081
   
   dubbo:
     application:
       name: dubbo-boot-consumer
       qos-enable: false
     registry:
       address: zookeeper://${zookeeper.address:127.0.0.1}:2181
   
   zookeeper:
     address: 47.113.148.39
   ```

# 四，配置

## 配置优先级

![](F:\截图\屏幕截图 2023-08-30 163123.png)

1. JVM -D 参数：当你部署或者启动应用时，它可以轻易地重写配置，比如，改变 dubbo 协议端口；
2. XML：XML 中的当前配置会重写dubbo.properties中的；
3. Properties：默认配置，仅仅作用于以上两者没有配置时。

**注意**

1. 如果在classpath下有超过一个dubbo.properties文件，比如，两个 jar 包都各自包含了 dubbo.properties，dubbo将随机选择一个加载，并且打印错误日志。
2. 如果id没有在protocol中配置，将使用name作为默认属性。

## 不同粒度配置的覆盖关系

![](F:\截图\屏幕截图 2023-08-30 164629.png)

1. 方法级优先，接口级次之，全局配置再次之。
2. 如果级别一样，则消费方优先，提供方次之。

## 启动检查

1. 取消启动时对提供者的检查

   ```xml
   <dubbo:reference id="userService" interface="com.wgx.common.service.UserService" check="false"/>
   ```

   ```java
   @DubboReference(check = false)
   private UserService userService;
   ```

2. 取消所有消费者的启动检查

   ```xml
   <dubbo:consumer check="false"/>
   ```

   ```yaml
   dubbo:
     consumer:
       check: false
   ```

3. 取消对注册中心的检查

   ```xml
   <dubbo:registry check="false"/>
   ```

   ```yaml
   dubbo:
     registry:
       check: false
   ```

## 超时设置

1. 单个设置

   ```xml
   <dubbo:reference id="userService" interface="com.wgx.common.service.UserService" timeout="3000"/>
   ```

   ```java
   @DubboReference(timeout = 3000)
   private UserService userService;
   ```

2. 全局设置

   ```xml
   <dubbo:consumer timeout="3000"/>
   ```

   ```yaml
   dubbo:
     consumer:
       timeout: 3000
   ```

3. 单个方法配置

   ```xml
   <dubbo:service interface="com.wgx.common.service.UserService" ref="userService">
       <dubbo:method name="getUsers" timeout="3000"/>
   </dubbo:service>
   ```

## 重试次数

同超时配置。。。

重试次数不包含第一次，例如retries="3"，会调用4次

## 多版本

1. 服务提供方设置版本

   ```xml
   <dubbo:service interface="com.wgx.common.service.UserService" ref="userService" version="1.0.0"/>
   ```

   ```java
   @DubboService(version = "1.0")
   ```

2. 服务消费方设置版本

   ```xml
   <dubbo:reference id="userService" interface="com.wgx.common.service.UserService" version="1.0.0"/>
   ```

   ```java
   @DubboReference(version = "1.0")
   ```

## 本地存根

1. 编写Stub的实现

   ```java
   public class UserServiceStub implements UserService {
       private final UserService userService;
   
       public UserServiceStub(UserService userService) {
           this.userService = userService;
       }
   
       @Override
       public List<User> getUsers() {
           System.out.println("UserServiceStub call...");
           return userService.getUsers();
       }
   }
   ```

2. 开启本地存根

   ```java
   @DubboReference(stub = "com.wgx.bootorderservice.controller.UserServiceStub", stubevent = true)
   private UserService userService;
   ```

# 五，高可用

## Zookeeper宕机与Dubbo直连

Zookeeper注册中心宕机，还可以消费Dubbo暴露的服务

- 监控中心宕掉不影响使用，只是丢失部分采样数据
- 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
- 注册中心对等集群，任意一台宕掉后，将自动切换到另一台注册中心
- **全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯**
- 服务提供者无状态，任意一台宕掉后，不影响使用
- 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

Dubbo直连

```java
@DubboReference(url = "127.0.0.1:20880")
private UserService userService;
```

## 负载均衡

1. Random LoadBalance

   随机，按权重设置随机概率。在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

2. RoundRobin LoadBalance

   轮循，按公约后的权重设置轮循比率。存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

3. LeastActive LoadBalance

   最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

4. ConsistentHash LoadBalance

   一致性Hash,相同参数的请求，总是发到同一提供者。当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动

服务提供者配置

```java
@DubboService(loadbalance = LoadbalanceRules.RANDOM)
```

服务消费者配置

```java
@DubboReference(loadbalance = LoadbalanceRules.RANDOM)
```

## 集群容错模式

1. Failover Cluster

   失败自动切换，当出现失败，重试其他服务器。通常用于读操作，但重试会带来更长延迟。可通过retries="2"来设置重试次数

2. Failfast Cluster

   快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作

3. Failsafe Cluster

   失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作

4. Failback Cluster

   失败自动恢复，后台记录是失败的请求，定时重发。通常用于消息通知操作

5. Forking Cluster

   并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过fork="2"来设置最大并行数

6. Broadcast Cluster

   广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或者本地资源信息

7. 设置

   ```java
   @DubboReference(cluster = ClusterRules.FAIL_OVER)
   ```

# 六，Dubbo3新特性

## Triple协议

## SERVER_STREAM

