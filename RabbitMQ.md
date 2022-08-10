# 第一章 基础部分

## 一，MQ的相关概念

1. 什么是MQ

   MQ(message queue),从字面意思上看，本质是个队列，FIFO先入先出，只不过队列中存放的内容是message而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ是一种非常常见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了MQ之后，消息发送上游只需要依赖MQ,不用依赖其他服务。

2. 为什么要使用MQ

   - 流量消峰

     举个例子，如果订单系统最多能做处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好。

   - 应用解耦

     以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，中单用户感受不到物流系统的故障，提升系统的可用性。

   - 异步处理

     有些服务间调用是异步的，例如A调用B,B需要花费很长时间执行，但是A需要知道B什么时候可以执行完，以前一般有两种方式，A过一段时间去调用B的查询api查询。或者A提供一个callback api,B执行完之后调用api通知A服务。这两种方式都不是很优雅，使用消息总线，可以很方便解决这个问题A调用B服务后，只需要监听B处理完成的消息，当B处理完成后，会发送一条消息给MQ,MQ会将此消息转发给A服务。这样A服务既不用循环调用B的查询api,也不用提供callback api。同样B服务也不用做这些操作。A服务还能及时的得到异步处理成功的消息。

3. MQ的分类

   - ActiveMQ

     - 优点：单机吞吐量万级，时效性ms级，可用性高，基于主从架构实现高可用性，消息可靠性较低的概率丢失数据
     - 缺点：官方社区现在对ActiveMQ5.x维护越来越少，高吞吐量场景较少使用

   - Kafka

     大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开Kafka,这款为大数据而生的消息中间件，以其百万级TPS的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被LinkedIn,Uber,Twitter,Netflix等大公司所采纳。

     - 优点：性能卓越，单机写入TPS约在百万条/秒，最大的优点，就是吞吐量高。时效性s级可用性非常高，kāfka是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用，消费者采用Pu‖方式获取消息，消息有序，通过控制能够保证所有消息被消费且仅被消费一次；有优秀的第三方Kafka Web管理界面Kafka-Manager;在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用
     - 缺点：Kafka单机超过64个队列/分区，Load会发生明显的飙高现象，队列越多，load越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢；

   - RocketMQ

     RocketMQ出自阿里巴巴的开源产品，用Java语言实现，在设计时参考了Kafka,并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog分发等场景。

     - 优点：单机吞吐量十万级，可用性非常高，分布式架构，消息可以做到0丢失，MQ功能较为完善，还是分布式的，扩展性好，支持10亿级别的消息堆积，不会因为堆积导致性能下降，源码是java我们可以自己阅读源码，定制自己公司的MQ
     - 缺点：支持的客户端语言不多，目前是java及c++,其中c++不成熟；社区活跃度一般，没有在MQ核心中去实现JMS等接口，有些系统要迁移需要修改大量代码

   - RabbitMQ

     2007年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。

     - 优点：由于erlang语言的高并发特性，性能较好；吞吐量到万级，MQ功能比较完备，健壮、稳定、易用、跨平台、支持多种语言如：Python、Ruby、.NET、Java、JMS、C.PHP、ActionScript、.XMPP、STOMP等，支持AJAX文档齐全；开源提供的管理界面非常棒，用起来很好用，社区活跃度高；更新频率相当高https://www.rabbitmq.com/news.html
     - 缺点：商业版需要收费，学习成本较高

4. MQ的选择

   - Kafka

     Kafka主要特点是基于Pu的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，如果有日志采集功能，肯定是首选Kafka。

   - RocketMQ

     天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RoketMQ在稳定性上可能更值得信赖，这些业务场景在阿里双11已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择RocketMQ。

   - RabbitMQ

     结合erlang语言本身的并发优势，性能微好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的数据量没有那么大，中小型公司优先选择功能比较完备的RabbitMQ。

## 二，RabbitMQ的相关概念

1. RabbitMQ的概念

   RāobitMQ是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑RabbitMQ是一个快递站，一个快递员帮你传递快件。RabbitMQ与快递站的主要区别在于，它不处理快件而是接收，存储和转发消息数据。

2. 四大核心概念

   - 生产者：产生数据发送消息的程序是生产者
   - 交换机：交换机是RabbitMQ非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定
   - 队列：队列是RabbitMQ内部使用的一种数据结构，尽管消息流经RabbitMQ和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式
   - 消费者：消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

3. RabbitMQ核心部分

   ![](F:\截图\屏幕截图 2022-04-28 171456.png)

   - Broker：接受和分发消息的应用，RabbitMQ Server就是Message Broker
   - Virtual host：出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚以的分组中，类以于网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ server提供的服务时，可以划分出多个vhost,每个用户在自己的host创建exchange/queue等
   - Connection：publisher/consumer和broker之间的TCP连接
   - Channel：如果每一次访问RabbitMQ都建立一个Connection,在消息量大的时候建立TCPConnection的开销将是巨大的，效率也较低。Channel是在connection内建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，AMQP method包含了channel id帮助客户端和message broker识别channel,.所以channel之间是完全隔离的。Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销
   - Exchange：message到达broker的第一站，根据分发规则，匹配查询表中的routing key,分发消息到queue中去。常用的类型有：direct(point--to-point)),topic(publish-subscribe)and fanout(multicast)
   - Queue：消息最终被送到这里等待consumer取走
   - Binding：exchange和queue之间的虚拟连接，binding中可以包含routing key,Binding信息被保存到exchange中的查询表中，用于message的分发依据

## 三，安装

1. 官网下载，上传服务器

2. 启动

   systemctl start rabbitmq-server.service

3. 开启web管理插件

   rabbitmq-plugins enable rabbitmq_management

4. 访问ip地址加端口号15672

5. 创建账号：rabbitmqctl add_user 账号 密码

6. 设置用户角色：rabbitmqctl set_user_tags 账号 administrator

7. 设置用户权限：

   set_permissions [-p \<vhostpath>] \<user> \<conf> \<write> \<read>

   rabbitmqctl set_permissions -p "/" 账号 “.\*” ".\*" ".\*"

## 四，java代码

1. 生产者代码

   ```java
   public class Producer {
       //队列名称
       public static final String QUEUE_NAME = "hello";
   
       public static void main(String[] args) throws Exception {
           //创建工厂
           ConnectionFactory factory = new ConnectionFactory();
           //设置IP，用户名，密码
           factory.setHost("47.100.85.66");
           factory.setUsername("admin");
           factory.setPassword("wgx");
           //创建连接
           Connection connection = factory.newConnection();
           //获取信道
           Channel channel = connection.createChannel();
           /**
            * 生成一个队列
            * 1. 队列名称
            * 2. 队列里面的消息是否持久化（默认情况消息存储在内存中）
            * 3. 该队列是否只供一个消费者进行消费，是否进行消费的共享，true可以多个消费者消费，false只能一个消费者消费
            * 4. 是否自动删除，最后一个消费者断开连接以后，该队列是否自动删除，true自动删除，false不自动删除
            * 5. 其他参数
            */
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           String message = "hello, world";
           /**
            * 发送一个消息
            * 1. 发送到那个交换机
            * 2. 路由的key值是哪个， 本次是队列的名称
            * 3. 其他参数信息
            * 4. 发送消息的消息体
            */
           channel.basicPublish("", QUEUE_NAME, null, message.getBytes(StandardCharsets.UTF_8));
           System.out.println("发送完毕");
       }
   }
   ```

2. 消费者代码

   ```java
   public class Consumer {
       //队列名称
       public static final String QUEUE_NAME = "hello";
   
       public static void main(String[] args) throws Exception {
           //创建工厂
           ConnectionFactory factory = new ConnectionFactory();
           //设置IP，用户名，密码
           factory.setHost("47.100.85.66");
           factory.setUsername("admin");
           factory.setPassword("wgx");
           //创建连接
           Connection connection = factory.newConnection();
           //获取信道
           Channel channel = connection.createChannel();
           //接受消息回调
           DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println(new String(message.getBody()));
           //取消消息的回调
           CancelCallback cancelCallback = consumerTag -> System.out.println("消息消费被中断");
           /**
            * 消费者消费消息
            * 1. 消费那个队列
            * 2. 消费成功之后是否要自动应答，true自动应答，false手动应答
            * 3. 消费者成功消费的回调
            * 4. 消费者取消消费回调
            */
           channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
       }
   }
   ```

## 五，工作队列原理

工作队列（又称任务队列）的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

## 六，消息应答

1. 概念

   消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。RabbitMQ一旦向消费者传递了一条消息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费这的消息，因为它无法接收到。

   为了保证消息在发送过程中不丢失，rabbitmq引入消息应答机制，消息应答就是：**消费者在接收到消息并且处理该消息之后，告诉rabbitmq它己经处理了，rabbitmq可以把该消息删除了。**

2. 自动应答

   消息发送后立即被认为已经传送成功，这种模式需要在**高吞吐量和数据传输安全性方面做权衡，**因为这种模式如果消息在接收到之前，消费者那边出现连接或者channel关闭，那么消息就丢失了，当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还米不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，**所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。**

3. 手动应答

   - Channel.basicdAck()：用于肯定确认

     RabbitMQ已知道消息并且成功的处理消息，可以将其丢弃了

   - Channel.basicNack()：用于否定确认

   - Channel.basicReject()：用于否定确认

     与Channel.basicNack()相比少一个参数，不处理该消息了直接拒绝，可以将其丢弃了

4. 批量应答

   multiple的true和false代表不同意思

   - true代表批量应答channel上未应答的消息比如说channel上有传送tag的消息5,6,7,8当前tag是8那么此时5-8的这些还未应答的消息都会被确认收到消息应答
   - false同上面相比只会应答tag=8的消息5,6,7这三个消息依然不会被确认收到消息应答

5. 消息自动重新入队

   如果消费者由于某些原因失去连接（其通道已关闭，连接己关闭或TCP连接丢失），导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

6. 消息手动应答代码

   - 生产者

     ```java
     public class Producer {
         public static final String QUEUE_NAME = "ack_queue";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             Connection connection = factory.newConnection();
             Channel channel = connection.createChannel();
             channel.queueDeclare(QUEUE_NAME, false, false, false, null);
             String message = "hello, world";
             channel.basicPublish("", QUEUE_NAME, null, message.getBytes(StandardCharsets.UTF_8));
             System.out.println("发送完毕");
         }
     }
     ```

   - 消费者1

     ```java
     public class Consumer1 {
         //队列名称
         public static final String QUEUE_NAME = "ack_queue";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             //创建连接
             Connection connection = factory.newConnection();
             //获取信道
             Channel channel = connection.createChannel();
             //接受消息回调
             DeliverCallback deliverCallback = (consumerTag, message) -> {
                 try {
                     //C1沉睡10秒
                     Thread.sleep(10 * 1000);
                     System.out.println("C1接受到消息:" + new String(message.getBody()));
                     /**
                      * 手动应答
                      * 1. 消息的标记tag
                      * 2. 是否批量应答，true批量应答，false不批量应答
                      */
                     channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
                 } catch (InterruptedException e) {
                     e.printStackTrace();
                 }
             };
             //取消消息的回调
             CancelCallback cancelCallback = consumerTag -> System.out.println("消息消费被中断");
             /**
              * 消费者消费消息
              * 1. 消费那个队列
              * 2. 消费成功之后是否要自动应答，true自动应答，false手动应答
              * 3. 消费者成功消费的回调
              * 4. 消费者取消消费回调
              */
             channel.basicConsume(QUEUE_NAME, false, deliverCallback, cancelCallback);
         }
     }
     ```

   - 消费者2

     ```java
     public class Consumer2 {
         //队列名称
         public static final String QUEUE_NAME = "ack_queue";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             //创建连接
             Connection connection = factory.newConnection();
             //获取信道
             Channel channel = connection.createChannel();
             //接受消息回调
             DeliverCallback deliverCallback = (consumerTag, message) -> {
                 try {
                     //C1沉睡10秒
                     Thread.sleep(30 * 1000);
                     System.out.println("C1接受到消息:" + new String(message.getBody()));
                     /**
                      * 手动应答
                      * 1. 消息的标记tag
                      * 2. 是否批量应答，true批量应答，false不批量应答
                      */
                     channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
                 } catch (InterruptedException e) {
                     e.printStackTrace();
                 }
             };
             //取消消息的回调
             CancelCallback cancelCallback = consumerTag -> System.out.println("消息消费被中断");
             /**
              * 消费者消费消息
              * 1. 消费那个队列
              * 2. 消费成功之后是否要自动应答，true自动应答，false手动应答
              * 3. 消费者成功消费的回调
              * 4. 消费者取消消费回调
              */
             channel.basicConsume(QUEUE_NAME, false, deliverCallback, cancelCallback);
         }
     }
     ```

## 七，RabbitMQ持久化

1. 概念

   刚刚我们已经看到了如何处理任务不丢失的情况，但是如何保障当RabbitMQ服务停掉以后消息生产者发送过米的消息不丢失。默认情况下RabbitMQ退出或由于某种原因崩溃时，它忽视队列和消息，除非告知它不要这样做。确保消息不会丢失需要做两件事，**我们需要将队列和消息都标记为持久化。**

2. 队列实现持久化

   之前我们创建的队列都是非持久化的，rabbitmq如果重启的化，该队列就会被删除掉，如果要队列实现持久化需要在声明队列的时候把durable参数设置为持久化。

   ```java
   channel.queueDeclare(QUEUE_NAME, true, false, false, null);
   ```

   但是需要注意的就是如果之前声明的队列不是持久化的，需要把原先队列先别除，或者重新创建一个持久化的队列，不然就会出现错误。

3. 消息持久化

   要想让消息实现持久化需要在消息生产者修改代码，MessageProperties.PERSISTENT_TEXT_PLAIN添加这个属性

   ```java
   channel.basicPublish("", QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes(StandardCharsets.UTF_8));
   ```

   将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉RabbitMQ将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候但是还没有存储完，消息还在缓存的一个间隔点。此时并没有真正写入磁盘。持久性保证性不强，但是对于我们的简单任务队列而言，这己经绰绰有余了。如果需要更强有力的特久化策略，参考后边课件发布确认章节。

4. 不公平分发

   在最开始的时候我们学习到RabbitMQ分发消息采用的轮训分发，但是在某种场景下这种策略并不是很好，比方说有两个消费者在处理任务，其中有个消费者1处理任务的速度非常快，而另外一个消费者2处理速度却很慢，这个时候我们还是采用轮训分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是RabbitMQ并不知道这种情况它依然很公平的进行分发。

   为了避免这种情况，我们可以设置参数channel.basicQos(1):

   ```java
   //设置不公平分发
   channel.basicQos(1);
   ```

5. 预取值

   本身消息的发送就是异步发送的，所以在任何时候，channel上肯定不止只有一个消息另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，**因此希望开发人员能限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。**这个时候就可以通过使用basic.qos方法设置“预取计数”值来完成的。**该值定义通道上允许的未确认消息的最大数量。**一旦数量达到配置的数量，RabbitMQ将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认，例如，假设在通道上有未确认的消息5、6、7、8，并且通道的预取计数设置为4，此时RabbitMQ将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被ack。比方说tag=6这个消息刚刚被确认ACK,RabbitMQ将会感知这个情况到并再发送一条消息。消息应答和QoS预取值对用户吞吐量有重大影响。通常，增加预取将提高向消费者传递消息的速度。**虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的RAM消耗**（随机存取存储器）应该小心使用具有无限预欧处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同100到300范围内的值通常可提供最佳的吞叶量，并且不会给消费者带来太大的风险。预取值为1是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中，对于大多数应用来说，稍微高一点的值将是最佳的

   ```java
   //设置预取值为5
   channel.basicQos(5);
   ```

## 八，发布确认

1. 发布确认原理

   生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都将会被指派一个唯一的ID(从1开始)，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者（包含消息的唯一ID),这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消总写入磁盘之后发出，broker回传给生产者的确认消息中delivery-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。confirm模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。

2. 开启确认方法

   发布确认默认是没有开启的，如果要开启需要调用方法confirmSelect,每当你要想使用发布确认，都需要在channel上调用该方法

   ```java
   //开启发布确认
   channel.confirmSelect();
   ```

3. 单个发布确认

   这是一种简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布，waitForConfirmsOrDie(Iong)这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。这种确认方式有一个最大的缺点就是：**发布速度特别的慢**，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能己经足够了。

4. 批量发布确认

   上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

5. 异步确认发布

   异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说，他是利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功，下面就让我们来详细讲解异步确认是怎么实现的。

   ```java
   //消息确认成功回调函数
   ConfirmCallback ackCallback = (deliveryTag, multiple) -> {};
   //消息确认失败回调函数
   ConfirmCallback nackCallback = (deliveryTag, multiple) -> {};
   channel.addConfirmListener(ackCallback, nackCallback);
   ```

6. 三种发布确认速度对比

   - 单独发布消息

     同步等待确认，简单，但吞吐量非常有限。

   - 批量发布消息

     批量同步等待确认，简单，合理的吞吐量，一旦出现间题但很难推断出是那条消息出现问题

   - 异步处理

     最佳性能和资源使用，在出现错误的清况下可以很好的控制，但是实现起来比较麻烦

## 九，交换机

在上一节中，我们创建了一个工作队列。我们假设的是工作队列背后，每个任务都恰好交付给一个消费者（工作进程）。在这一部分中，我们将做一些完全不同的事情我们将消息传达给多个消费者。这种模式称为"发布/订阅”为了说明这种模式，我们将构建一个简单的日志系统。它将由两个程序组成：第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘另外一个消费者接收到消息后把消息打印在屏幕上，事实上第一个程序发出的日志消息将广播给所有消费者者

1. 交换机的概念

   RabbitMQ消息传递模型的核心思想是：**生产者生产的消息从不会直接发送到队列**。实际上，通常生产者甚至都不知道这些消息传递传递到了那些队列中。

   相反，**生产者只能将消息发送到交换机(exchange)**，交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把他们到许多队列中还是说应该丢弃它们。这就的由交换机的类型来决定

2. 交换机的类型

   直接(direct)，主题(topic)，标题(headers)，扇出(fanout)

   无名exchange

   默认交换机，通过空字符串("")进行标识

3. 临时队列

   每当我们连接到Rabbit时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。其次一旦我们断开了消费者的连接，队列将被自动删除。

   创建临时队列的方式：String queueName channel.queueDeclare().getQueue();

4. 绑定

   什么是bingding呢，binding其实是exchange和queue之间的桥梁，它告诉我们exchange和那个队列进行了绑定关系。比如说下面这张图告诉我们的就是X与Q1和Q2进行了绑定

   ![](F:\截图\屏幕截图 2022-04-29 205430.png)

5. fanout交换机

   - 介绍

     Fanout这种类型非常简单。正如从名称中猜到的那样，它是将接收到的所有消息广播到它知道的所有队列中。系统中默认有些exchange类型

   - 使用

     生产者

     ```java 
     public class EmitLog {
         public static final String EXCHANGE_NAME = "logs";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             //创建连接
             Connection connection = factory.newConnection();
             //获取信道
             Channel channel = connection.createChannel();
             //声明一个交换机
             channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
     
             String message = "hello, world";
             channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes(StandardCharsets.UTF_8));
             System.out.println("发送成功");
         }
     }
     ```

     消费者

     ```java
     public class ReceiveLog1 {
         public static final String EXCHANGE_NAME = "logs";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             //创建连接
             Connection connection = factory.newConnection();
             //获取信道
             Channel channel = connection.createChannel();
             //声明一个交换机
             channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
             //声明一个临时队列
             String queueName = channel.queueDeclare().getQueue();
             //绑定交换机与队列
             channel.queueBind(queueName, EXCHANGE_NAME, "");
             DeliverCallback deliverCallback = (s, delivery) -> System.out.println("C1接受到消息" + new String(delivery.getBody(), "UTF-8"));
             channel.basicConsume(queueName, true, deliverCallback, s -> {});
         }
     }
     ```

     其他消费者同上

6. direct交换机

   在上一节中，我们构建了一个简单的日志记录系统。我们能够向许多接收者广播日志消息。在本节我们将向其中添加一些特别的功能比方说我们只让某个消费者订阅发布的部分消息，例如我们只把严重错误消息定向存储到日志文件（以节省磁盘空间），同时仍然能够在控制台上打印所有日志消息。

   我们再次来回顾一下什么是bindings,绑定是交换机和队列之间的桥梁关系。也可以这么理解：队列只对它绑定的交换机的消息感兴趣。绑定用参数：routingKey来表示也可称该参数为binding key创建绑定我们用代码：channel.queueBind(queueName,EXCHANGE_NAME,"routingKey"):绑定之后的意义由其交换类型决定

7. topic交换机

   在上一个小节中，我们改进了日志记录系统。我们没有使用只能进行随意广播的fanout交换机，而是使用了direct交换机，从而有能实现有选择性地接收日志。

   尽管使用direct交换机改进了我们的系统，但是它仍然存在局限性-比方说我们想接收的日志类型有info.base和info.advantage,某个队列只想info.base的消息，那这个时候direct就办不到了。这个时候就只能使用topic类型

   - Topic的要求

     发送到类型是topic交换机的消息的routing_key不能随意写，必须满足一定的要求，**它必须是一个单词列表，以点号分隔开。**这些单词可以是任意单词，比如说："stock.usd.nyse","nyse.vmw","quick.orange.rabbit'"这种类型的。当然这个单词列表最多不能超过255个字节。

     ==注意==

     *(星号)可以代替一个单词

     #(井号)可以替代零个或多个单词

   - Topic匹配案例

     \*.orange.\*：中间带orange的3个单词的字符串

     \*.\*.rabbit：最后一个单词是rabbit的3个单词的字符串

     lazy.#：第一个单词是lazy的多个单词

## 十，死信队列

1. 死信的概念

   先从概念解释上搞清楚这个定义，死信，顾名思义就是无法被消费的消息，字面意思可以这样理解，一般来说，producer将消息投递到broker或者直接到queue里了，consumer从queue取出消息进行消费，但某些时候由于特定的原因导致queue中的某些消息无法被消费，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列。

   应用场景：为了保证订单业务的消息数据不丢失，需要使用到RābbitMQ的死信队列机制，当消息消费发生异常时，将消息投入死信队列中，还有比如说：用户在商城下单成功并点击去支付后在指定时间未支付时自动失效

2. 死信的来源

   - 消息TTL过期
   - 队列达到最大长度（队列满了，无法再添加数据到mq中）
   - 消息被拒绝（basic.reject或basic.nack）并且requeue=false

3. 代码

   - 消费者1

     ```java
     public class Consumer1 {
         //普通交换机
         public static final String NORMAL_EXCHANGE = "normal_exchange";
         //死信交换机
         public static final String DEAD_EXCHANGE = "dead_exchange";
         //普通队列
         public static final String NORMAL_QUEUE = "normal_queue";
         //死信队列
         public static final String DEAD_QUEUE = "dead_queue";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             //创建连接
             Connection connection = factory.newConnection();
             //获取信道
             Channel channel = connection.createChannel();
             //声明死信交换机和普通交换机
             channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
             channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
             //声明死信队列和普通队列
             /**
              * 普通队列需要添加参数，指定死信交换机
              * 死信交换机：x-dead-letter-exchange
              * 死信交换机的routing_key：x-dead-letter-routing-key
              * 过期时间：x-message-ttl（一般在发送消息的时候设置）
              */
             HashMap<String, Object> arguments = new HashMap<>();
             arguments.put("x-dead-letter-exchange", DEAD_EXCHANGE);
             arguments.put("x-dead-letter-routing-key", "wgx");
             //arguments.put("x-message-ttl", 10 * 1000);
             channel.queueDeclare(NORMAL_QUEUE, false, false, false, arguments);
             channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
             //绑定普通的交换机与普通队列
             channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "wgx");
             //绑定死信的交换机与死信队列
             channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "wgx");
             DeliverCallback deliverCallback = (consumerTag, message) -> {
                 System.out.println(message);
             };
             channel.basicConsume(NORMAL_QUEUE, deliverCallback, (consumerTag, sig) -> {});
     
         }
     }
     ```

   - 生产者

     ```java
     public class Producer {
         //普通交换机
         public static final String NORMAL_EXCHANGE = "normal_exchange";
         //普通队列
         public static final String NORMAL_QUEUE = "normal_queue";
     
         public static void main(String[] args) throws Exception {
             //创建工厂
             ConnectionFactory factory = new ConnectionFactory();
             //设置IP，用户名，密码
             factory.setHost("47.100.85.66");
             factory.setUsername("admin");
             factory.setPassword("wgx");
             //创建连接
             Connection connection = factory.newConnection();
             //获取信道
             Channel channel = connection.createChannel();
             //设置TTL
             AMQP.BasicProperties properties = new AMQP.BasicProperties()
                     .builder().expiration("10000").build();
             for (int i = 0; i < 10; i++) {
                 String message = "info" + (i + 1);
                 channel.basicPublish(NORMAL_EXCHANGE, "wgx", properties, message.getBytes(StandardCharsets.UTF_8));
             }
         }
     }
     ```

   - 消费者2

     略，就是一个普通的消费者

4. 队列达到最大长度

   ```java
   arguments.put("x-max-length", 6);
   ```

5. 消息被拒绝

   ```java
   DeliverCallback deliverCallback = (consumerTag, message) -> {
       if ("info5".equals(new String(message.getBody()))) {
           //拒接接受info5
           channel.basicReject(message.getEnvelope().getDeliveryTag(), false);
       } else {
           System.out.println(new String(message.getBody()));
       }
   };
   ```

## 十一，延迟队列

1. 延迟队列概念

   延时队列，队列内部是有序的，最重要的特性就体现在它的延时属性上，延时队列中的元素是希望在指定时间到了以后或之前取出和处理，简单来说，延时队列就是用来存放需要在指定时间被处理的元素的队列。

2. 使用场景

   - 订单在十分钟之内未支付则自动取消
   - 新创建的店铺，如果在十天内都没有上传过商品，则自动发送消息提醒。
   - 用户注册成功后，如果三天内没有登陆则进行短信提醒。
   - 用户发起退款，如果三天内没有得到处理则通知相关运营人员。
   - 预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议。

## 十二，整合SpringBoot

1. 创建项目，配置属性

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-amqp</artifactId>
   </dependency>
   ```

   ```yaml
   spring:
     rabbitmq:
       host: 47.100.85.66
       port: 5672
       username: admin
       password: wgx
   ```

2. 配置文件类代码

   ```java
   @Configuration
   public class TtlQueueConfig {
       //普通交换机
       public static final String X_EXCHANGE = "X";
       //死信交换机
       public static final String Y_DEAD_LETTER_EXCHANGE = "Y";
       //普通队列
       public static final String QUEUE_A = "QA";
       public static final String QUEUE_B = "QB";
       //死信队列
       public static final String DEAD_LETTER_QUEUE = "QD";
       //声明xExchange
       @Bean("xExchange")
       public DirectExchange xExchange() {
           return new DirectExchange(X_EXCHANGE);
       }
       //声明yExchange
       @Bean("yExchange")
       public DirectExchange yExchange() {
           return new DirectExchange(Y_DEAD_LETTER_EXCHANGE);
       }
       //声明队列
       @Bean("queueA")
       public Queue queueA() {
           return QueueBuilder.durable(QUEUE_A).deadLetterExchange(Y_DEAD_LETTER_EXCHANGE).deadLetterRoutingKey("wgx").ttl(10 * 1000).build();
       }
       @Bean("queueB")
       public Queue queueB() {
           return QueueBuilder.durable(QUEUE_B).deadLetterExchange(Y_DEAD_LETTER_EXCHANGE).deadLetterRoutingKey("wgx").ttl(40 * 1000).build();
       }
       @Bean("queueD")
       public Queue queueD() {
           return QueueBuilder.durable(DEAD_LETTER_QUEUE).build();
       }
       //绑定
       @Bean
       public Binding queueABindingX(@Qualifier("queueA") Queue queueA, @Qualifier("xExchange") DirectExchange xExchange) {
           return BindingBuilder.bind(queueA).to(xExchange).with("wgx_10");
       }
       @Bean
       public Binding queueBBindingX(@Qualifier("queueB") Queue queueB, @Qualifier("xExchange") DirectExchange xExchange) {
           return BindingBuilder.bind(queueB).to(xExchange).with("wgx_40");
       }
       @Bean
       public Binding queueDBindingY(@Qualifier("queueD") Queue queueD, @Qualifier("yExchange") DirectExchange yExchange) {
           return BindingBuilder.bind(queueD).to(yExchange).with("wgx");
       }
   }
   ```

3. 生产者

   ```java
   @Slf4j
   @RestController
   @RequestMapping("/ttl")
   public class SendMessageController {
       @Autowired
       private RabbitTemplate rabbitTemplate;
   
       @GetMapping("/sendMessage/{message}")
       public void sendMessage(@PathVariable("message") String message) {
           log.info("[{}]发送一条消息:{}", new Date().toString(), message);
           rabbitTemplate.convertAndSend("X", "wgx_10", "消息来自ttl为10s的队列:" + message);
           rabbitTemplate.convertAndSend("X", "wgx_40", "消息来自ttl为40s的队列:" + message);
       }
   }
   ```

4. 死信队列消费者

   ```java
   @Slf4j
   @Component
   public class DeadLetterQueueConsumer {
       @RabbitListener(queues = "QD")
       public void receiver(Message message, Channel channel) {
           String msg = new String(message.getBody());
           log.info("[{}]收到死信队列消息:{}", new Date().toString(), msg);
       }
   }
   ```

## 十三，延迟队列的优化

1. 配置类

   ```java
   //QC
   @Bean("queueC")
   public Queue queueC() {
       return QueueBuilder.durable(QUEUE_C).deadLetterExchange(Y_DEAD_LETTER_EXCHANGE).deadLetterRoutingKey("wgx").build();
   }
   @Bean
   public Binding queueCBindingX(@Qualifier("queueC") Queue queueC, @Qualifier("xExchange") DirectExchange xExchange) {
       return BindingBuilder.bind(queueC).to(xExchange).with("wgx");
   }
   ```

2. 生产者

   ```java
   @GetMapping("/sendMessage/{message}/{ttlTime}")
   public void sendMessage(@PathVariable("/message") String message, @PathVariable("/ttlTime") String ttlTime) {
       log.info("[{}]发送一条过期时间为{}消息:{}", new Date().toString(), ttlTime, message);
       rabbitTemplate.convertAndSend("X", "wgx_C", "消息来自queueC:" + message, msg -> {
           //设置过期时间
           msg.getMessageProperties().setExpiration(ttlTime);
           return msg;
       });
   }
   ```

3. 看起来以乎没什么问题，但是在最开始的时候，就介绍过如果使用在消息属性上设置TTL的方式，消息可能并不会按时“死亡“，因为RabbitMQ只会检查第一个消息是否过期，如果过期侧丢到死信队列，如果第一个消息的诞时时长很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行。

### RabbitMQ插件实现延迟队列

1. 安装插件

   - 下载rabbitmq_delayed_message_exchange-3.9.0.ez，并放在rabbitmq的安装目录下的plugins目录下

   - 执行rabbitmq-plugins enable rabbitmq_delayed_message_exchange

   - 重启RabbitMQ

2. 配置文件代码

   ```java
   @Configuration
   public class DelayedQueueConfig {
       //队列
       public static final String DELAYED_QUEUE = "delayed.queue";
       //交换机
       public static final String DELAYED_EXCHANGE = "delayed.exchange";
       //routingkey
       public static final String DELAYED_ROUTING_KEY = "delayed.routingkey";
   
       //声明交换机
       @Bean("delayedExchange")
       public CustomExchange delayedExchange() {
           /**
            * 1. 交换机的名称
            * 2. 交换机的类型
            * 3. 是否需要持久化
            * 4. 是否需要自动删除
            * 5. 其他参数
            */
           HashMap<String, Object> args = new HashMap<>();
           args.put("x-delayed-type", "direct");
           return new CustomExchange(DELAYED_EXCHANGE, "x-delayed-message", true, false, args);
       }
       //声明队列
       @Bean("delayedQueue")
       public Queue delayedQueue() {
           return QueueBuilder.durable(DELAYED_QUEUE).build();
       }
       //绑定
       @Bean
       public Binding delayedQueueBindingDelayedExchange(@Qualifier("delayedQueue") Queue delayedQueue, @Qualifier("delayedExchange") CustomExchange delayedExchange) {
           return BindingBuilder.bind(delayedQueue).to(delayedExchange).with(DELAYED_ROUTING_KEY).noargs();
       }
   }
   ```

3. 生产者

   ```java
   @Slf4j
   @RestController
   @RequestMapping("/ttl")
   public class SendMessageController {
       @Autowired
       private RabbitTemplate rabbitTemplate;
   
       @GetMapping("/sendMessage/{message}")
       public void sendMessage(@PathVariable("message") String message) {
           log.info("[{}]发送一条消息:{}", new Date().toString(), message);
           rabbitTemplate.convertAndSend("X", "wgx_A", "消息来自ttl为10s的队列:" + message);
           rabbitTemplate.convertAndSend("X", "wgx_B", "消息来自ttl为40s的队列:" + message);
       }
       @GetMapping("/sendExpirationMessage/{message}/{ttlTime}")
       public void sendMessage(@PathVariable("message") String message, @PathVariable("ttlTime") String ttlTime) {
           log.info("[{}]发送一条过期时间为{}消息:{}", new Date().toString(), ttlTime, message);
           rabbitTemplate.convertAndSend("X", "wgx_C", "消息来自queueC:" + message, msg -> {
               //设置过期时间
               msg.getMessageProperties().setExpiration(ttlTime);
               return msg;
           });
       }
       @GetMapping("/sendDelayedMessage/{message}/{delayTime}")
       public void sendMessage(@PathVariable("message") String message, @PathVariable("delayTime") Integer delayedTime) {
           log.info("[{}]发送一条过期时间为{}消息到延迟队列:{}", new Date().toString(), delayedTime, message);
           rabbitTemplate.convertAndSend(DelayedQueueConfig.DELAYED_EXCHANGE,
                   DelayedQueueConfig.DELAYED_ROUTING_KEY,
                   message,
                   msg -> {
               msg.getMessageProperties().setDelay(delayedTime);
               return msg;
                   });
       }
   }
   ```

4. 消费者

   ```java
   @Slf4j
   @Component
   public class DelayedQueueConsumer {
       @RabbitListener(queues = DelayedQueueConfig.DELAYED_QUEUE)
       public void receiveDelayedQueue(Message message) {
           String msg = new String(message.getBody());
           log.info("[{}]收到延迟队列消息:{}", new Date().toString(), msg);
       }
   }
   ```

# 第二章 高级部分

## 一，发布确认高级

在生产环境中由于一些不明原因，导致rabbitmq重启，在RabbitMQ重启期间生产者消息投递失败，导致消息丢失，需要手动处理和恢复。于是，我们开始思考，如何才能进行RabbitMQ的消息可靠投递呢？特别是在这样比较极端的情况，RabbitMQ集群不可用的时候，无法投递的消息该如何处理呢：

1. 回调接口

   ```java
   @Slf4j
   @Component
   public class MyCallback implements RabbitTemplate.ConfirmCallback {
       @Autowired
       private RabbitTemplate rabbitTemplate;
       
       //注入
       @PostConstruct
       public void init() {
           rabbitTemplate.setConfirmCallback(this);
       }
   
       /**
        * 交换机确认回调方法
        * @param correlationData 保存回调消息的ID及相关信息
        * @param ack 交换机接受到消息ack=true，没有接受到消息ack=false
        * @param cause 交换机接受成功为null，失败为失败的原因
        */
       @Override
       public void confirm(CorrelationData correlationData, boolean ack, String cause) {
           String id = (correlationData != null) ? correlationData.getId() : "";
           if (ack) {
               log.info("收到id为{}的消息", id);
           } else {
               log.info("因为{}没有收到id为{}消息", cause, id);
           }
       }
   }
   ```

2. 配置文件

   ```yaml
   #NONE:禁用发布确认模式，是默认值
   #CORRELATED:发布消息成功到交换机后会触发回调方法
   #SIMPLE:有两种效果，一种效果和CORRELATED一样会触发回调方法
   #       另一种在发布消息成功之后使用rabbitTemplate调用waitForConfirms或者
   #       waitForConfirmsOrDie方法等到broker节点返回结果，根据结果来判定下一步的逻辑，要注意的是
   #       waitForConfirmsOrDie方法返回false则会关闭channel，则接下来无法发送消息到broker
   publisher-confirm-type: correlated
   ```

3. 回退消息

   - Mandatory参数

     在仅开启了生产者确认机制的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息，如果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道消息被丢弃这个事件的。那么如何让无法被路由的消息帮我想办法处理一下？最起码通知我一声，我好自己处理啊。通过设置mandatory参数可以在当消息传递过程中不可达目的地时将消息返回给生产者。

   - 配置文件

     ```yaml
     spring:
       rabbitmq:
         publisher-returns: true
     ```

   - 配置类

     ```java
     //注入
     @PostConstruct
     public void init() {
         rabbitTemplate.setReturnsCallback(this::returnedMessage);
         rabbitTemplate.setConfirmCallback(this::confirm);
     }
     
     @Override
     public void returnedMessage(ReturnedMessage returned) {
         log.error("消息[{}]被回退了,交换机[{}],routingkey[{}],回退原因[{}]",
                 new String(returned.getMessage().getBody()),
                 returned.getExchange(),
                 returned.getRoutingKey(),
                 returned.getReplyText());
     }
     ```

## 二，备份交换机

1. 配置类

   ```java
   //备份交换机
   @Bean("backupExchange")
   public FanoutExchange backupExchange() {
       return new FanoutExchange(BACKUP_EXCHANGE);
   }
   
   //备份队例
   @Bean("backupQueue")
   public Queue backupQueue() {
       return QueueBuilder.durable(BACKUP_QUEUE).build();
   }
   
   //报警队列
   @Bean("warningQueue")
   public Queue warningQueue() {
       return QueueBuilder.durable(WARNING_QUEUE).build();
   }
   
   //绑定
   @Bean
   public Binding backupQueueBindingBackupExchange(@Qualifier("backupExchange") FanoutExchange backupExchange,
                                                   @Qualifier("backupQueue") Queue backupQueue) {
       return BindingBuilder.bind(backupQueue).to(backupExchange);
   }
   @Bean
   public Binding warningQueueBindingBackupExchange(@Qualifier("backupExchange") FanoutExchange backupExchange,
                                                   @Qualifier("warningQueue") Queue warningQueue) {
       return BindingBuilder.bind(warningQueue).to(backupExchange);
   }
   
   //声明交换机
   @Bean("confirmExchange")
   public DirectExchange confirmExchange() {
       //绑定备份交换机
       return ExchangeBuilder.directExchange(CONFIRM_EXCHANGE).durable(true).withArgument("alternate-exchange", BACKUP_EXCHANGE).build();
   }
   ```

2. 报警消费

   ```java
   @Slf4j
   @Component
   public class WarningConsumer {
       @RabbitListener(queues = ConfirmConfig.WARNING_QUEUE)
       public void receiveMessage(Message message) {
           String msg = new String(message.getBody());
           log.error("检测到一条不可路由的消息:{}", msg);
       }
   }
   ```

3. 注意

   mandatory参数与备份交换机可以一起使用的时候，如果两者同时开启，消息究竟何去何从？谁优先级高，经过上面结果显示答案是备份交换机优先级高。

## 三，RabbitMQ其他知识点

### 幂等性

1. 概念

   用户对于同一操作发起的一次请求或者多次请求的结果是一致的，不会因为多次点击而产生了副作用。举个最简单的例子，那就是支付，用户购买商品后支付，支付扣款成功，但是返回结果的时候网络异常，此时钱已经扣了，用户再次点击按钮，此时会进行第二次扣款，返回结果成功，用户查询余额发现多扣钱了，流水记录也变成了两条。在以前的单应用系统中，我们只需要把数据操作放入事务中即可，发生错误立即回滚，但是再响应客户端的时候也有可能出现网络中断或者异常等等

2. 消息重复消费

   消费者在消费MQ中的消息时，MQ已把消息发送给消费者，消费者在给MQ返回ck时网络中断，故MQ未收到确认信息，该条消息会重新发给其他的消费者，或者在网络重连后再次发送给该消费者，但实际上该消费者已成功消费了该条消息，造成消费者消费了重复的消息。

3. 解决思路

   MQ消费者的隔等性的解决一般使用全局ID或者写个唯一标识比如时间戳或者UUID或者订单消费者消费MQ中的消息也可利用MQ的该id来判断，或者可按自己的规则生成一个全局唯一id,每次消费消息时用该id先判该消息是香已消费过。

4. 消费端的幂等性保障

   在海量订单生成的业务高峰期，生产端有可能就会重复发生了消息，这时候消费端就要实现幂等性，这就意味着我们的消息永远不会被消费多次，即使我们收到了一样的消息，业界主流的幂等性有两种操作：唯一ID+指纹码机制，利用数据库主键去重，b.利用Redis的原子性去实现

5. 唯一ID+指纹码机制

   指纹码：我们的一些规则或者时间戳加别的服务给到的唯一信息码，它并不一定是我们系统生成的，基本都是由我们的业务规则拼接而来，但是一定要保证唯一性，然后就利用查询语句进行判断这个是香存在数据库中，优势就是实现简单就一个拼接，然后查询判断是否重复；劣势就是在高并发时，如果是单个数据库就会有写入性瓶颈当然也可以采用分库分表提升性能，但也不是我们最推荐的方式。

6. Redis原子性

   利用redis执行setnx命令，天然具有幂等性。从而实现不重复消费

### 优先级队列

1. 使用场景

   在我们系统中有一个订单催付的场景，我们的客户在天猫下的订单，淘宝会及时将订单推送给我们，如果在用户设定的时间内未付款那么就会给用户推送一条短信提醒，很简单的一个功能对吧，但是，tmall商家对我们来说，肯定是要分大客户和小客户的对吧，比如像苹果，小米这样大商家一年起码能给我们创造很大的利润，所以理应当然，他们的订单必须得到优先处理，而曾经我们的后端系统是使用redis来存放的定时轮询，大家都知道redis只能用List做一个简简单单的消息队列，并不能实现一个优先级的场景，所以订单量大了后采用RabbitMQ进行改造和优化，如果发现是大客户的订单给一个相对北比较高的优先级，否则就是默认优先级。

2. 代码实现

   ```java
   HashMap<String, Object> arguments = new HashMap<>();
   arguments.put("x-max-priority", 10);
   channel.queueDeclare(QUEUE_NAME, false, false, false, arguments);
   ```

   ```java
   AMQP.BasicProperties properties = new AMQP.BasicProperties.Builder().priority(5).build();
   channel.basicPublish("", QUEUE_NAME, properties, message.getBytes(StandardCharsets.UTF_8));
   ```

### 惰性队列

1. 使用场景

   RabbitMQ从3.6.0版本开始引入了惰性队列的概念。惰性队列会尽可能的将消息存入磁盘中，而在消费者消费到相应的消息时才会被加载到内存中，它的一个重要的设计目标是能够支持更长的队列，即支持更多的消息存储。当消费者由于各种各样的原因（比如消费者下线、宕机亦或者是由于维护而关闭等）而致使长时间内不能消费消息造成堆积时，惰性队列就很有必要了。

   默认情况下，当生产者将消息发送到RabbitMQ的时侯，队列中的消息会尽可能的存储在内存之中，这样可以更加快速的将消息发送给消费者。即使是持久化的消息，在被写入磁盘的同时也会在内存中驻留一份备份。当RabbitMQ需要释放内存的时候，会将内存中的消息换页至磁盘中，这个操作会耗费较长的时间，也会阻塞队列的操作，进而无法接收新的消息，虽然RabbitMQ的开发者们一直在升级相关的算法，但是效果始终不太理想，尤其是在消息量特别大的时候。

2. 两种模式

   队列具备两种模式：default和lazy,默认的为default模式，在3.6.0之前的版本无需做任何变更，lazy模式即为惰性队列的模式，可以通过调用channel.queueDeclare方法的时候在参数中设置，也可以通过Policy的方式设置，如果一个队列同时使用这两种方式设置的话，那么Policy的方式具备更高的优洗级，如果要通过声明的方式改变已有队列的模式的话，那么只能先制除队列，然后再重新声明一个新的。

   在队列声明的时候可以通过x-queue-mode"参数来设置队列的模式，取值为"default'和lazy'"。

   ```java
   HashMap<String, Object> arguments = new HashMap<>();
   arguments.put("x-queue-mode", "lazy");
   channel.queueDeclare(QUEUE_NAME, false, false, false, arguments);
   ```

# 第三章 集群部分

## 一，集群搭建

1. 修改3台机器的主机名称

   vim /etc/hostname

2. 配置各个节点的hosts文件，让各个节点都能互相识别对方

3. 以确保各个节点的cookie文件使用的是同一个值

   在host1上

   scp /var/lib/rabbitmq/.erlang.cookie root@node2:/var/lib/rabbitmq/.erlang.cookie

   scp /var/lib/rabbitmq/.erlang.cookie root@node3:/var/lib/rabbitmq/.erlang.cookie

4. 启动RabbitMQ服务，顶带启动Erlang虚拟机和RabbitMQ应用服务

   rabbitmq-server -detached

5. 将node2和node3加入到node1

   - rabbitmqctl stop_app(rabbitmqctl stop会将Erlang虚拟机关闭，rabbitmqctl stop_app只关闭RabbitMQ服务)
   - rabbitmqctl reset
   - rabbitmqctl join_cluster rabbit@node1
   - rabbitmqctl start_app(只启动应用服务)

6. 查看集群状态

   rabbitmqctl cluster_status

7. 需要重新设置用户

   - rabbitmqctl add_user 账号 密码
   - rabbitmqctl set_user_tags 账号 administrator
   - rabbitmqctl set_permissions -p "/" 账号 “.\*” ".\*" ".\*"

8. 删除集群节点

   rabbitmqctl forget_cluster_node rabbit@node2(node1机器上执行)

## 二，镜像队列

1. 使用镜像的原因

   如果RabbitMQ集群中只有一个Broker节点，那么该节，点的失效将导致整体服务的临时性不可用，并且也可能会导致消息的丢失.可以将所有消息都设置为持久化，并且对应队列的durable属性也设置为true,但是这样仍然无法避免由于缓存导致的问题：因为消息在发送之后和被写入磁盘并执行刷盘动作之间存在一个短暂却会产生问题的时间窗.通过publisherconfirm机制能够确保客户端知道哪些消息己经存入磁盘，尽管如此，一般不希望遇到因单点故障导致的服务不可用。

   引入镜像队列(Mirror Queue)的机制，可以将队列镜像到集群中的其他Broker节点之上，如果集群中的一个节点失效了，队列能自动地切换到镜像中的另一个节点上以保证服务的可用性。

2. 配置

   ![](F:\截图\屏幕截图 2022-05-01 200635.png)

## 三，Federation Exchange

1. 使用Federation Exchange的原因

   (broker北京)，(broker深圳)彼此之间相距甚远，网络延迟是一个不得不面对的问题。有一个在北京的业务(Client北京)需要连接broker北京)，向其中的交换器exchangeA发送消息，此时的网络延迟很小(Client北京)可以迅速将消息发送至exchangeA中，就算在开启了publisherconfirm机制或者事务机制的情况下，也可以迅速收到确认信息。此时又有个在深圳的业务(Client深圳)需要向exchangeA发送消息，那么(Client深圳)(broker北京)之间有很大的网络延迟，(Client深圳)将发送消息至exchangeA会经历一定的延迟，尤其是在开启了publisherconfirm机制或者事务机制的情况下，(Client深圳)会等待很长的延迟时间来接收(boker北京)的确认信息，进而必然造成这条发送线程的性能降低，甚至造成一定程度上的阻塞。

   将业务(Client深圳部署到北京的机房可以解决这个问题，但是如果(Client深圳)调用的另些服务都陪部署在深圳，那么又会引发新的时延问题，总不见得将所有业务全部部署在一个机房，那么容灾又何以实现？这里使用Federation插件就可以很好地解决这个问题。

2. 搭建步骤

   - 保证每台节点单独运行

   - 在每台机器上开启federation相关插件

     rabbitmg-plugins enable rabbitmq_federation

     rabbitmq-plugins enable rabbitmq_federation_management

## 四，Federation Queue

1. 使用原因

   联邦队列可以在多个Boker节点（或者集群）之间为单个队列提供均衡负载的功能。一个联邦队列可以连接一个或者多个上游队列(upstream queue),并从这些上游队列中获取消息以满足本地消费者消费消息的需求。

## 五，Shovel

1. 使用原因

   Federation具备的数据转发功能类似，Shovel够可靠、持续地从一个Broker中的队列（作为源端，即source)拉取数据并转发至另一个Broker中的交换器（作为目的端，即destination)。作为源端的队列和作为目的端的交换器可以同时位于同一个Broker,也可以位于不同的Broker上。Shovel可以翻译为"铲子"，是一种比较形象的比喻，这个"铲子"可以将消息从一方"铲子"另一方。Shovel行为就像优秀的客户端应用程序能够负责连接源和目的地、负责消息的读写及负责连接失败问题的处理。

2. 搭建步骤

   - 开启插件

      rabbitmq-plugins enable rabbitmq_shovel

     rabbitmq-plugins enable rabbitmq_shovel_management

