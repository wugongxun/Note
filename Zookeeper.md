# 第一章 Zookeeper基本概念

1. 基本概念

   Zookeeper是一个分布式的、开源的分布式应用程序的协调服务。Zookeeper提供的主要功能包括：

   - 配置管理
   - 分布式锁
   - 集群管理

2. 安装与配置

   - 上传安装包

   - 解压、

     tar -xvf apache-zookeeper-3.8.0-bin.tar.gz

   - 修改配置文件

     dataDir=/opt/zookeeper/zkdata

   - 在bin目录中使用zkServer.sh启动

     ./zkServer.sh start

     ==注意==

     Zookeeper会占用8080端口，如果8080端口被占用则无法启动

     修改Zookeeper占用端口

     admin.serverPort=9090

   - 停止

     ./zkServer.sh stop

# 第二章 Zookeeper命令操作

## 一，数据模型

1. ZooKeeper是一个树形目录服务，其数据横型和Unix的文件系统目录树很类似，拥有一个层次化结构。
2. 这里面的每一个节点都被称为：ZNode,每个节点上都会保存自己的数据和节点信息
3. 节点可以拥有子节点，同时也允许少量(1MB)数据存储在该节点之下。
4. 节点可以分为四大类：
   - PERSISTENT持久化节点
   - EPHEMERAL临时节点：-e
   - PERSISTENT_SEQUENTIAL持久化顺序节点：-s
   - EPHEMERAL_SEQUENTIAL临时顺序节点：-es

## 二，Zookeeper服务端命令

1. 启动ZooKeeper服务：/zkServer.sh start
2. 查看ZooKeeper服务状态：./zkServer.sh status
3. 停I止ZooKeeper服务：/zkServer.sh stop
4. 重启ZooKeeper服务：./zkServer.sh restart

## 三，Zookeeper客户端命令

1. 连接server

   ./zkCli.sh -server localhost:2181

   **连接本机可以直接使用：./zkCli.sh**

2. 查看当前节点下有那些子节点：ls 路径

   ls /

3. 创建节点：create 节点 数据

   create /wgx wugongxun

4. 设置数据：set 节点 数据

   set /wgx wugongxun

5. 获取数据：get 节点

   get /wgx

6. 删除节点：delete 节点

   delete /wgx

7. 删除非空节点：deleteall 节点

   deleteall /wgx

8.  创建临时节点

   create -e

9. 创建顺序节点

   create -s

10. 创建临时顺序节点

    create -es

11. 查看路径的节点状态属性

    ls -s /

    - czxid:节点被创建的事务ID
    - ctime:创建时间
    - mzxid:最后一次被更新的事务ID
    - mtime:修改时间
    - pzxid:子节点列表最后一次被更新的事务ID
    - cversion:子节点的版本号
    - dataversion:数据版本号
    - aclversion:权限版本号
    - ephemeralOwner:用于临时节点，代表临时节点的事务lD,如果为持久节点则为0
    - dataLength:节点存的数据的长度
    - numChildren:当前节点的子节点个数

# 第三章 javaAPI操作

## 一，Curator简介

1. Curator是Apache Zookeeper的java客户端库，目标是简化
2. 常见的Zookeeper Java API
   - 原生Java API
   - ZkClient
   - Curator

## 二，Curator API常用操作

1.  建立连接

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.apache.curator</groupId>
         <artifactId>curator-framework</artifactId>
         <version>5.2.1</version>
     </dependency>
     <dependency>
         <groupId>org.apache.curator</groupId>
         <artifactId>curator-recipes</artifactId>
         <version>5.2.1</version>
     </dependency>
     ```

   - 创建测试类

     ```java
     public class CuratorTest {
         @Test
         public void testConnect() {
             /**
              * @param connectString – 连接字符串
              * @param sessionTimeoutMs – 会话超时时间，ms
              * @param connectionTimeoutMs – 连接超时时间，ms
              * @param retryPolicy – 重试策略
              */
             RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
             //第一种方式
             CuratorFramework client = CuratorFrameworkFactory.newClient("47.100.85.66:2181", 60 * 1000, 15 * 1000, retryPolicy);
             //开启连接
             client.start();
     
             //第二种方式
             CuratorFrameworkFactory.builder()
                     .connectString("47.100.85.66:2181")
                     .sessionTimeoutMs(60 * 1000)
                     .connectionTimeoutMs(15 * 1000)
                     .retryPolicy(retryPolicy)
                     .build();
         }
     }
     ```

2. 添加节点

   ```java
   /**
    * 创建节点：create 持久 顺序 临时 数据
    * 1. 基本创建
    * 2. 创建节点带有数据
    * 3. 设置节点的类型
    * 4. 创建多级节点
    */
   @Test
   public void testCreate() throws Exception {
       //1. 基本创建
       //如果创建节点没有指定数据，默认将当前客户端的ip作为数据储存
       //client.create().forPath("/wgx");
       //2. 创建节点带有数据
       //client.create().forPath("/wgx", "wugongxun".getBytes(StandardCharsets.UTF_8));
       //3. 设置节点的类型(默认是持久化)
       //client.create().withMode(CreateMode.EPHEMERAL).forPath("/wgx", "wugongxun".getBytes(StandardCharsets.UTF_8));
       //保持连接10秒
       //Thread.sleep(10 * 1000);
       //4. 创建多级节点
       client.create().creatingParentsIfNeeded().forPath("/wgx/1", "wugongxun".getBytes(StandardCharsets.UTF_8));
   }
   ```

3. 删除节点

   ```java
   /**
        * 删除节点
        * 1. 删除单个节点
        * 2. 删除带有子节点的节点
        * 3. 必须成功的删除
        * 4. 回调
        */
   @Test
   public void testDelete() throws Exception {
       //删除单个节点
       //client.delete().forPath("/wgx");
       //删除带有子节点的节点
       //client.delete().deletingChildrenIfNeeded().forPath("/wgx");
       //必须成功的删除
       //client.delete().guaranteed().forPath("/wgx");
       //回调
       client.delete().guaranteed().inBackground(new BackgroundCallback() {
           @Override
           public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
               System.out.println("删除完成");
               System.out.println(curatorEvent);
           }
       }).forPath("/wgx");
   }
   ```

4. 修改节点

   ```java
   /**
    * 修改节点
    * 1. 修改数据
    * 2. 根据版本修改
    */
   @Test
   public void testSet() throws Exception {
       //修改数据
       //client.setData().forPath("/wgx", "wugongxun".getBytes(StandardCharsets.UTF_8));
       //根据版本修改
       Stat stat = new Stat();
       client.getData().storingStatIn(stat).forPath("/wgx");
       client.setData().withVersion(stat.getVersion()).forPath("/wgx", "wugongxun".getBytes(StandardCharsets.UTF_8));
   }
   ```

5. 查询节点

   ```java
   /**
    * 查询数据
    * 1. 查询节点
    * 2. 查询子节点
    * 3. 查询节点状态信息
    */
   @Test
   public void testGet() throws Exception {
       //查询节点
       System.out.println(new String(client.getData().forPath("/wgx")));
       //查询子节点
       List<String> nodes = client.getChildren().forPath("/");
       System.out.println(nodes);
       //查询节点状态信息
       Stat stat = new Stat();
       client.getData().storingStatIn(stat).forPath("/");
       System.out.println(stat);
   }
   ```

6. Watch事件监听

   - ZooKeeper允许用户在指定节点上注册一些Watcher,并且在一些特定事件触发的时候，ZooKeeper服务端会将事件通知到感兴趣的客户端上去，该机制是ZooKeeper实现分布式协调服务的重要特性。

   - ZooKeeper中引入了Watcher机制来实现了发布/订阅功能能，能够让多个订阅者同时监听某一个对象，当一个对象自身状态变化时，会通知所有订阅者。

   - Curator引入了Cache来实现对ZooKeeper服务端事件的监听。

   - Zookeeper提供三种Watcher

     - NodeCache:只是监听某一个特定的节点
     - PathChildrenCache:监控一个ZNode的子节点
     - TreeCache:可以监控整个树上的所有节点，类似于PathChildrenCache和NodeCachel的组合

   - ```java
     /**
      * NodeCache:给指定一个节点注册监听器
      */
     @Test
     public void testNodeCache() throws Exception {
         //1. 创建NodeCache对象
         NodeCache nodeCache = new NodeCache(client, "/wgx");
         //2. 注册监听
         nodeCache.getListenable().addListener(() -> {
             System.out.println("节点变化了");
             //获取修改后的数据
             byte[] data = nodeCache.getCurrentData().getData();
             System.out.println(new String(data));
         });
         //3. 开启监听，如果设置为true则开启监听，加载缓冲数据
         nodeCache.start(true);
         while (true) {
     
         }
     }
     ```

   - ```java
     /**
      * PathChildrenCache:监听某个节点的所有子节点们
      */
     @Test
     public void testPathChildrenCache() throws Exception {
         PathChildrenCache pathChildrenCache = new PathChildrenCache(client, "/wgx", true);
         pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
             @Override
             public void childEvent(CuratorFramework curatorFramework, PathChildrenCacheEvent pathChildrenCacheEvent) throws Exception {
                 System.out.println("子节点变化了");
                 //获取修改后的数据
                 byte[] data = pathChildrenCacheEvent.getData().getData();
                 System.out.println(new String(data));
             }
         });
         pathChildrenCache.start(true);
         while (true) {
     
         }
     }
     ```

   - ```java
     /**
      * TreeCache:监控整个树上的所有节点
      */
     @Test
     public void testTreeCache() throws Exception {
         TreeCache treeCache = new TreeCache(client, "/wgx");
         treeCache.getListenable().addListener(new TreeCacheListener() {
             @Override
             public void childEvent(CuratorFramework curatorFramework, TreeCacheEvent treeCacheEvent) throws Exception {
                 System.out.println("节点变化了");
                 byte[] data = treeCacheEvent.getData().getData();
                 System.out.println(new String(data));
             }
         });
         treeCache.start();
         while (true) {
     
         }
     }
     ```

## 三，分布式锁

1. 介绍

   - 在我们进行单机应用开发，涉及并发同步的时候，我们往往采用synchronized或者Lock的方式来解决多线程间的代码同步问题，这时多线程的运行都是在同一个JVM之下，没有任何问题。
   - 但当我们的应用是分布式集群工作的情况下，属于多JVM下的工作环境，跨M之间已经无法通过多线程的锁解决同步问题。
   - 那么就需要一种更加高级的锁机制，来处理种跨机器的进程之间的数据同步问题一这就是分布式锁。

2. Zookeeper分布式锁原理

   - 核心思想：当客户端要获取锁，则创建节点，使用完锁，则删除该节点。
   - 客户端获取锁时，在lock节点下创建临时顺序节点。
   - 然后获取lock下面的所有子节点，客户端获取到所有的子节点之后，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁。使用完锁后，将该节点删除。
   - 如果发现自己创建的节点并非lock所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到比自己小的那个节点，同时对其注册事件监听器，监听删除事件。
   - 如果发现比自己小的那个节点被删除，则客户端的Watchers会收到相应通知，此时再次判断自己创建的节点是否是lock子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听。

3. 代码实现

   在Curator中有五种锁方案

   - InterProcessSemaphoreMutex:分布式排它锁（非可重入锁）
   - InterProcessMutex:分布式可重入排它锁
   - InterProcessReadWriteLock:分布式读写锁
   - InterProcessMultiLock:将多个锁作为单个实体管理的容器
   - InterProcessSemaphoreV2:共享信号量

# 第四章 集群

## 一，介绍

1. Leader选举
   - Serverid:服务器ID比如有三台服务器，编号分别是1,2,3。编号越大在选择算法中的权重越大。
   - Zxid:数据ID服务器中存放的最大数据ID.值越大说明数据越新在选举算法中数据越新权重越大。
   - 在Leader选举的过程中，如果某台ZooKeeper获得了超过半数的选票，则此ZooKeeper就可以成为Leader了。

## 二，集群搭建

1. 准备工作

   - 在data目录中创建一个myid文件，内容为每个服务器的ID

   - 修改zoo.cfg

     server.1=localhost:2881:3881
     server.2=localhost:2882:3882
     server.3=localhost:2883:3883

   - 把三个服务器都启动

## 三，集群角色

1. Leader领导者
   - 处理事务请求
   - 集群内部各服务的调度者
2. Follower跟随者
   - 处理客户端非事务请求，转发事务请求给Leader服务器
   - 参与Leader选举投票
3. Observer观察者
   - 处理客户端非事务请求，转发事务请求给Leader服务器
