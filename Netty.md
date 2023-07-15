# 第一章 NIO

## 一，NIO基本介绍

1. Java NIO全称java non-blocking IO，是指JDK提供的新API。从JDK1.4开始，Java提供了一系列改进的输入/输出的新特性，被统称为NIO(即New IO)，是同步非阻塞的
2. NIO有三大核心部分：Channel(通道)，Buffer(缓冲区)， Selector(选择器)
3. NIO是面向缓冲区，或者面向块编程的。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络
4. Java NIO的非阻塞模式，使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。
5. HTTP2.0使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1大了好几个数量级

## 二，Buffer

1. 基本介绍

   缓冲区(Buffer)：缓冲区本质上是一个可以读写数据的内存块，可以理解成是一个容器对象（含数组），该对象提供了一组方法，可以更轻松地使用内存块，缓冲区对象内置了一些机制，能够跟踪和记录缓冲区的状态变化情况。Channel提供从文件、网络读取数据的渠道，但是读取或写入的数据都必须经由Buffer

2. 常用Buffer子类

   ByteBuffer、ShortBuffer、IntBuffer、CharBuffer、LongBuffer、DoubleBuffer、FloatBuffer

3. Buffer的四个属性含义

   - Capacity：容量，即可以容纳的最大数据量，在缓冲区创建时设定并且不能改变
   - Limit：表示缓冲区的当前终点，不能对缓冲区超过极限的位置进行读写操作，且极限是可以修改的
   - Position：位置，下一个要被读写的元素的索引，每次读写缓冲区数据都会改变值，为下次读写做准备
   - Mark：标记

4. 案例

   ```java
   public static void main(String[] args) {
       //创建一个IntBuffer，大小为5，可以存放5个int
       IntBuffer intBuffer = IntBuffer.allocate(5);
       //存放数据
       for (int i = 0; i < intBuffer.capacity(); i++) {
           intBuffer.put(i);
       }
       //取出数据
       //将buffer转换，读写切换
       intBuffer.flip();
       while (intBuffer.hasRemaining()) {
           System.out.println(intBuffer.get());
       }
   }
   ```

## 三，Channel

1. 基本介绍

   - 通道可以同时进行读写，流只能读或者写
   - 通道可以实现异步读写数据
   - 通道可以从缓冲读数据，也可以写数据到缓冲
   - 常用的Channel类有：FileChannel.、DatagramChannel、ServerSocketChannel和 SocketChannel。
   - FileChannel用于文件的数据读写， DatagramChannel用于UDP的数据读写， ServerSocketChannel和SocketChannel用于TCP的数据读写。

2. FileChannel

   ```java
   public static void main(String[] args) throws IOException {
       //创建文件输入流
       FileInputStream fis = new FileInputStream("F:\\1.txt");
       //获取FileChannel
       FileChannel inChannel = fis.getChannel();
       //创建文件输出流
       FileOutputStream fos = new FileOutputStream("F:\\2.txt");
       FileChannel outChannel = fos.getChannel();
       //创建ByteBuffer
       ByteBuffer byteBuffer = ByteBuffer.allocate(1024 * 1024);
       while (true) {
           //读取文件
           int read = inChannel.read(byteBuffer);
           if (read == -1) {
               break;
           }
           //读写切换
           byteBuffer.flip();
           //写入数据
           outChannel.write(byteBuffer);
           //清空Buffer
           byteBuffer.compact();
       }
       //关闭流
       fis.close();
       byteBuffer.compact();
       inChannel.close();
       outChannel.close();
   }
   ```

3. 注意事项

   - ByteBuffer支持类型化的put和get，put放入的是什么数据类型，get就应该使用相应的数据类型来取出，否则可能有BufferUnderflowException异常。
   - 可以将一个普通Buffer转成只读Buffer
   - NIO还提供了MappedByteBuffer，可以让文件直接在内存（堆外的内存）中进行修改，而如何同步到文件由NIO来完成
   - 前面我们讲的读写操作，都是通过一个Buffer完成的，NIO还支持通过多个 Buffer（即Buffer数组）完成读写操作，即Scattering和Gatering

4. Scatter/Gather案例

   ```java
   public static void main(String[] args) throws IOException {
       ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
   
       serverSocketChannel.bind(new InetSocketAddress(9999));
   
       ByteBuffer[] buffers = new ByteBuffer[2];
       buffers[0] = ByteBuffer.allocate(5);
       buffers[1] = ByteBuffer.allocate(3);
   
       SocketChannel socketChannel = serverSocketChannel.accept();
       while (true) {
           long read = socketChannel.read(buffers);
           System.out.println("读取到的数据长度:" + read);
           Arrays.stream(buffers).forEach(byteBuffer -> {
               System.out.println("position:" + byteBuffer.position() + ",limit:" + byteBuffer.limit());
               byteBuffer.flip();
           });
           socketChannel.write(buffers);
           Arrays.stream(buffers).forEach(Buffer::clear);
       }
   }
   ```


## 四，Selector

1. 基本介绍
   - Java的NIO，用非阻塞的IO方式。可以用一个线程，处理多个的客户端连接，就会使用到Selector（选择器）
   - Selector能够检测多个注册的通道上是否有事件发生（注意：多个Channel以事件的方式可以注册到同一个Selector），如果有事件发生，便获取事件然后针对每个事件进行相应的处理。这样就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求。
   - 只有在连接/通道真正有读写事件发生时，才会进行读写，就大大地减少了系统开销，并且不必为每个连接都创建一个线程，不用去维护多个线程
   - 避免了多线程之间的上下文切换导致的开销

2. SelectionKey

   SelectionKey表示Selector和网络通道的注册关系,共四种：

   - OP_ACCEPT：有新的网络连接可以accept，值为16
   - OP_CONNECT：代表连接已经建立，值为8
   - OP_READ：代表读操作，值为
   - OP_WRITE：代表写操作，值为4

3. 案例

   ```java
   public static void main(String[] args) throws IOException {
       //创建ServerSocketChannel，并且绑定端口
       ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
       serverSocketChannel.bind(new InetSocketAddress(9999));
       //设置非阻塞
       serverSocketChannel.configureBlocking(false);
       //创建Selector
       Selector selector = Selector.open();
       //将ServerSocketChannel注册到Selector
       serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
       ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
       while (true) {
           //判断是否有通道准备就绪
           if (selector.selectNow() > 0) {
               //取出所有就绪的通道
               Set<SelectionKey> selectedKeys = selector.selectedKeys();
               Iterator<SelectionKey> iterator = selectedKeys.iterator();
               while (iterator.hasNext()) {
                   SelectionKey selectedKey = iterator.next();
                   if (selectedKey.isAcceptable()) {
                       System.out.println("连接成功");
                       try {
                           //获取新连接的客户端通道，设置非阻塞，并且注册到Selector
                           SocketChannel socketChannel = serverSocketChannel.accept();
                           socketChannel.configureBlocking(false);
                           socketChannel.register(selector, SelectionKey.OP_READ);
                       } catch (IOException e) {
                           e.printStackTrace();
                       }
                   } else if (selectedKey.isReadable()) {
                       try {
                           //获取可读的通道
                           SocketChannel channel = (SocketChannel) selectedKey.channel();
                           //读取数据
                           int read = channel.read(byteBuffer);
                           byteBuffer.flip();
                           System.out.println(new String(byteBuffer.array(), 0, read));
                           byteBuffer.compact();
                       } catch (IOException e) {
                           e.printStackTrace();
                       }
                   }
                   //移除就绪的selectedKey
                   iterator.remove();
               }
           }
       }
   }
   ```

## 五，NIO与零拷贝

1. 基本介绍

   - 零拷贝是网络编程的关键，很多性能优化都离不开。
   - 我们说零拷贝，是从操作系统的角度来说的。因为内核缓冲区之间，没有数据是重复的（只有kernel buffer有一份数据）
   - 零拷贝不仅仅带来更少的数据复制，还能带来其他的性能优势，例如更少的上下文切换，更少的CPU缓存伪共享以及无CPU校验和计算。
   - 在Java程序中，常用的零拷贝有mmap（内存映射）和sendFile。
   - mmap适合小数据量读写，sendFile适合大文件传输。
   - mmap需要4次上下文切换，3次数据拷贝；sendFile需要3次上下文切换，最少2次数据拷贝。 
   - sendFile可以利用DMA方式，减少CPU拷贝，mmap则不能（必须从内核拷贝到Socket缓冲区）

2. mmap

   mmap通过内存映射，将文件映射到内存缓冲区，同时用户可以共享内核空间的数据。这样，在进行网络传输时，就可以减少内核空间到用户空间的拷贝次数

3. sendFile

   Linux2.1版本提供了sendFile函数，数据根本不经过用户态，直接从内存缓冲区进入到Socket Buffer，同时由于和用户态完全无关，就减少了一次上下文切换

   Linux2.4版本中做了一些修改，避免了缓冲区拷贝到Socket Buffer的操作，直接拷贝到协议栈，从而在减少一次数据拷贝

# 第二章 Netty

## 一，线程模型

1. 基本介绍

   - 目前存在的线程模型有：传统阻塞I/O服务模型、Reactor模式
   - 根据Reactor的数量和处理资源池线程的数量不同，有3种典型的实现
     - 单Reactor单线程
     - 单Reactor多线程
     - 主从Reactor多线程
   - Netty线程模式（Netty主要基于主从Reactor多线程模型做了一定的改进，其中主从Reactor多线程模型有多个Reactor） 

2. 传统阻塞I/O服务模型

   - 采用阻塞IO模式获取输入的数据
   - 每个连接都需要独立的线程完成数据的输入，业务处理，数据返回

3. Reactor模式

   - 基于I/O复用模型，多个连接共用一个阻塞对象，应用程序只需要在一个阻塞对象等待，无需阻塞等待所有连接。当某个连接有新的数据可以处理时，操作系统通知应用程序，线程从阻塞状态返回，开始进行业务处理
   - 基于线程池复用线程资源。不必再为每个连接创建线程，将连接完成后的业务处理任务分配给线程进行处理，一个线程可以处理多个连接业务
   - ![](F:\截图\屏幕截图 2023-06-17 170126.png)

4. Reactor中核心组成

   - Reactor：Reactor在一个单独的线程中运行，负责监听和分发事件，分发给适当的处理程序来对O事件做出反应。
   - Handlers：处理程序执行I/O事件要完成的实际事件，Reactor通过调度适当的处程序来响应I/O事件，处理程序执行非阻塞操作。

5. 单Reactor单线程优缺点

   - ![](F:\截图\屏幕截图 2023-06-17 172106.png)

   - 优点

     模型简单，没有多线程、进程通信、竞争的问题，全部都在一个线程中完成

   - 缺点

     - 性能问题，只有一个线程，无法完全发挥多核CPU的性能。Handler在处理某个连接上的业务时，整个进程无法处理其他连接事件，很容易导致性能瓶颈
     - 可靠性问题，线程意外终止，或者进入死循环，会导致整个系统通信模块不可用，不能接收和处理外部消息，造成节点故障

6. 单Reactor多线程

   - ![](F:\截图\屏幕截图 2023-06-17 172012.png)

   - 优点

     可以充分利用多核CPU

   - 缺点

     多线程数据共享和访问比较复杂，Reactor处理所有的事件的监听和响应，在高并发场景容易出性能瓶颈

7. 主从Reactor多线程

   - ![](F:\截图\屏幕截图 2023-06-17 172427.png)
   - 优点
     - 响应快，不必为单个同步时间所阻塞，虽然Reactor本身依然是同步的
     - 可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销
     - 扩展性好，可以方便的通过增加Reactor实例个数来充分利用CPU资源
     - 复用性好，Reactor模型本身与具体事件处理逻辑无关，具有很高的复用性

## 二，Netty模型

![](F:\截图\屏幕截图 2023-06-29 174407.png)

1. Netty抽象出两组线程池BossGroup专门负责接受客户端的连接，WorkerGroup专门负责网络的读写
2. BossGroup和WorkerGroup类型都是NioEventLoopGroup
3. NioEventLoopGroup相当于一个事件循环组，这个组中含有多个事件循环，每一个事件循环都是NioEventLoop
4. NioEventLoop表示一个不断循环的执行处理任务的线程，每个NioEventLoop都有一个Selector，用于监听绑定在其上的socket的网络通讯
5. NioEventLoopGroup可以有多个线程，即可以含有多个NioEventLoop
6. 每个BossNioEventLoop执行步骤有三步
   - 轮询accept事件
   - 处理accept事件，与client建立连接，生成NioSocketChannel，并将其注册到某个WorkerNioEventLoop上的selector
   - 处理任务队列的任务，即runAllTasks
7. 每个WorkerNioEventLoop执行步骤
   - 轮询read，write事件
   - 处理I/O事件，即read、write事件，在对应的NioSocketChannel处理
   - 处理任务队列的任务，即runAllTasks
8. 每个WorkerNioEventLoop处理业务时，会使用pipeline（管道），pipeline中包含了Channel，即通过pipeline可以获取到对应通道，管道中维护了很多的处理器

## 三，TCP服务

1. 服务端

   - NettyServer

     ```java
     public static void main(String[] args) throws InterruptedException {
         //创建BossGroup，只处理连接请求
         NioEventLoopGroup bossGroup = new NioEventLoopGroup();
         //创建WorkerGroup，处理业务
         NioEventLoopGroup workerGroup = new NioEventLoopGroup();
         try {
             //创建服务器端的启动对象，配置参数
             ServerBootstrap bootstrap = new ServerBootstrap();
             bootstrap.group(bossGroup, workerGroup) //设置两个线程组
                     .channel(NioServerSocketChannel.class)  //设置使用NioServerSocketChannel作为服务器的通道实现
                     .option(ChannelOption.SO_BACKLOG, 128) //设置线程队列得到连接个数
                     .childOption(ChannelOption.SO_KEEPALIVE, true)  //设置保持活动连接状态
                     .childHandler(new ChannelInitializer<SocketChannel>() {
                         @Override
                         protected void initChannel(SocketChannel socketChannel) throws Exception {
                             //
                             socketChannel.pipeline().addLast(new NettyServerHandler());
                         }
                     });  //给WorkerGroup的EventLoop对应的管道设置处理器
             //绑定端口并且同步，生成一个ChannelFuture
             //启动服务器
             ChannelFuture channelFuture = bootstrap.bind(9999).sync();
             //对关闭通道进行监听
             channelFuture.channel().closeFuture().sync();
         } finally {
             bossGroup.shutdownGracefully();
             workerGroup.shutdownGracefully();
         }
     }
     ```

   - 自定义Handler

     ```java
     public class NettyServerHandler extends ChannelInboundHandlerAdapter {
         /**
          * 读取消息
          * @param ctx 上下文对象，含有管道pipeline，通道channel，地址
          * @param msg 客户端发送的数据
          */
         @Override
         public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
             System.out.println("ChannelHandlerContext:" + ctx);
             ByteBuf buf = (ByteBuf) msg;
             System.out.println("收到消息:" + buf.toString(CharsetUtil.UTF_8));
             System.out.println("客户端的地址:" + ctx.channel().remoteAddress());
         }
     
         /**
          * 数据读取完毕
          * @param ctx
          */
         @Override
         public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
             //将数据写入到缓存，并刷新
             ctx.writeAndFlush(Unpooled.copiedBuffer("hello", CharsetUtil.UTF_8));
         }
     
         /**
          * 处理异常
          * @param ctx
          * @param cause 异常
          */
         @Override
         public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
             ctx.close();
         }
     }
     ```

2. 客户端

   - NettyClient

     ```java
     public static void main(String[] args) throws InterruptedException {
         //客户端需要一个事件循环组
         NioEventLoopGroup eventExecutors = new NioEventLoopGroup();
         try {
             //创建客户端启动对象
             Bootstrap bootstrap = new Bootstrap();
             bootstrap.group(eventExecutors) //设置线程组
                     .channel(NioSocketChannel.class)    //设置客户端通道的实现类
                     .handler(new ChannelInitializer<SocketChannel>() {
                         @Override
                         protected void initChannel(SocketChannel socketChannel) throws Exception {
                             socketChannel.pipeline().addLast(new NettyClientHandler());
                         }
                     });
             //连接服务端
             ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 9999).sync();
             //对关闭通道进行监听
             channelFuture.channel().closeFuture().sync();
         } finally {
             eventExecutors.shutdownGracefully();
         }
     }
     ```

   - 自定义Handler

     ```java
     public class NettyClientHandler extends ChannelInboundHandlerAdapter {
         /**
          * 当通道就绪就会触发
          * @param ctx
          * @throws Exception
          */
         @Override
         public void channelActive(ChannelHandlerContext ctx) throws Exception {
             System.out.println("ChannelHandlerContext:" + ctx);
             ctx.writeAndFlush(Unpooled.copiedBuffer("hello server", CharsetUtil.UTF_8));
         }
     
         @Override
         public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
             ByteBuf buf = (ByteBuf) msg;
             System.out.println("收到来自服务端的消息:" + buf.toString(CharsetUtil.UTF_8));
             System.out.println("服务端的地址:" + ctx.channel().remoteAddress());
         }
     
         @Override
         public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
             ctx.close();
         }
     }
     ```


## 四，TaskQueue

1. 用户程序自定义的普通任务

   ```java
   @Override
   public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
       ByteBuf buf = (ByteBuf) msg;
       System.out.println("收到来自客户端的消息:" + buf.toString(CharsetUtil.UTF_8));
       ctx.channel().eventLoop().execute(() -> {
           try {
               Thread.sleep(10 * 1000);
               ctx.writeAndFlush(Unpooled.copiedBuffer("hello client", CharsetUtil.UTF_8));
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
       });
   }
   ```

2. 用户自定义定时任务

   ```java
   @Override
   public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
       ByteBuf buf = (ByteBuf) msg;
       System.out.println("收到来自客户端的消息:" + buf.toString(CharsetUtil.UTF_8));
       ctx.channel().eventLoop().schedule(() -> {
           ctx.writeAndFlush(Unpooled.copiedBuffer("hello client", CharsetUtil.UTF_8));
       }, 10, TimeUnit.SECONDS);
   }
   ```

3. Netty模型再说明

   - Netty抽象出两组线程池，BossGroup专门负责接受客户端连接，WorkerGroup专门负责网络读写操作
   - NioEventLoop表示一个不断循环执行处理任务的线程，每个NioEventLoop都有一个selector，用于监听绑定在其上的socket网络通道
   - NioEventLoop内部采用串行化设计，从消息的读取->解码->处理->编码->发送，始终由IO线程NioEventLoop负责
   - NioEventLoopGroup下包含多个NioEventLoop
   - 每个NioEventLoop中包含有一个Selector，一个TaskQueue
   - 每个NioEventLoop的Selector上可以注册监听多个NioChannel
   - 每个NioChannel只会绑定唯一的NioEventLoop
   - 每个NioChannel都绑定有一个自己的ChannelPipeline

## 五，异步模型

1. 异步的概念和同步相对。当一个异步过程调用发出后，调用者不能立刻得到结果。实际处理这个调用的组件在完成后，通过状态、通知和回调来通知调用者。

2. Netty中的I/O操作是异步的，包括Bind、Write、Connect等操作会简单的返回一个ChannelFuture

3. 调用者并不能立刻获得结果，而是通过Future-Listener机制，用户可以方便的主动获取或者通过通知机制获得IO操作结果

4. Netty的异步模型是建立在future和callback的之上的，callback就是回调。Future的核心思想是：假设一个方法fun，计算过程可能非常耗时，等待fun返回显然不合适。那么可以在调用fun的时候，立马返回一个Future，后续可以通过 Future去监控方法fun的处理过程（即：Future-Listener机制）

5. Future-Listener机制

   - 当Future对象刚刚创建时，处于非完成状态，调用者可以通过返回的ChannelFuture来获取操作执行的状态，注册监听函数来执行完成后的操作。
   - 常见有如下操作
     - 通过isDone方法来判断当前操作是否完成
     - 通过isSuccess方法来判断已完成的当前操作是否成功
     - 通过getCause方法来获取已完成的当前操作失败的原因
     - 通过isCancelled方法来判断已完成的当前操作是否被取消
     - 通过addListener方法来注册监听器，当操作已完成(isDone方法返回完成)，将会通知指定的监听器。如果Future对象已完成，则通知指定的监听器

6. 示例

   ```java
   //绑定端口并且同步，生成一个ChannelFuture
   //启动服务器
   ChannelFuture channelFuture = bootstrap.bind(9999).sync();
   
   channelFuture.addListener(future -> {
       if (future.isSuccess()) {
           System.out.println("监听端口成功");
       } else {
           System.out.println("监听端口失败");
       }
   });
   ```

# 第三章 Netty核心模块组件

## 一，BootStrap，ServerBootStrap

1. Bootstrap意思是引导，一个Netty应用通常由一个Bootstrap开始，主要作用是配置整个Netty程序，串联各个组件，Netty中BootStrap类是客户端瑞程序的启动引导类， ServerBootStrap是服务端启动引导类
2. 常见的方法有
   - public ServerBootstrap group(EventLoopGroup parentGroup, EventLoopGroup childGroup)；用于服务器端，用来设置两个EventLoop
   - public B group(EventLoopGroup group)；该方法用于客户端，用来设置一个EventLoop
   - public B channel(Class<?extends C>channelClass),该方法用来设置一个服务器端的通道实现
   - public<T\> B option(ChannelOption<T\>option, T value)；用来给ServerChannel添加配置
   - public<T\> ServerBootstrap childOption(ChannelOption<T\>childOption, Tvalue)；用来给接收到的通道添加配置
   - public ServerBootstrap childHandler(ChannelHandler childHandler)；该方法用来设置业务处理类（自定义的handler）
   - public ChannelFuture bind(int inetPort)；该方法用于服务器端，用来设置占用的端口号
   - public ChannelFuture connect(String inetHost, int inetPort)；该方法用于客户端，用来连接服务器

## 二，Future，ChannelFuture

1. Netty中所有的IO操作都是异步的，不能立刻得知消息是否被正确处理。但是可以过一会等它执行完成或者直接注册一个监听，具体的实现就是通过Future和 ChannelFutures，他们可以注册一个监听，当操作执行成功或失败时监听会自动触发注册的监听事件
2. 常见的方法有
   - Channel channel()；返回当前正在进行IO操作的通道
   - ChannelFuture sync()；等待异步操作执行完毕

## 三，Channel

1. Netty网络通信的组件，能够用于执行网络I/O操作。
2. 通过Channel可获得当前网络连接的通道的状态
3. 通过Channel可获得网络连接的配置参数（例如接收缓冲区大小）
4. Channel提供异步的网络/O操作（如建立连接，读写，绑定端口），异步调用意味着任何/0调用都将立即返回，并且不保证在调用结束时所请求的I/O操作已完成
5. 调用立即返回一个ChannelFuture实例，通过注册监听器到ChannelFuture上，可以I/O操作成功、失败或取消时回调通知调用方
6. 支持关联I/O操作与对应的处理程序
7. 不同协议、不同的阻塞类型的连接都有不同的Channel类型与之对应，常用的Channel类型有、
   - NioSocketChannel，异步的客户端TCP Socket连接。
   - NioServerSocketChannel，异步的服务器端TCP Socket连接。
   - NioDatagramChannel，异步的UDP连接。
   - NioSctpChannel，异步的客户端Sctp连接。
   - NioSctpServerChannel，异步的Sctp服务器端连接，这些通道涵盖了UDP和TCP网络IO以及文件IO。

## 四，Selector

1. Netty基于Selector对象实现I/O多路复用，通过Selector一个线程可以监听多个连接的Channel事件。
2. 当向一个Selector中注册Channel后，Selector内部的机制就可以自动不断地查询(Select)这些注册的Channel是否有已就绪的I/O事件（例如可读，可写，网络连接完成等)，这样程序就可以很简单地使用一个线程高效地管理多个Channel

## 五，ChannelHandler

1. ChannelHandler是一个接口，处理I/O事件或者拦截I/O操作，将其转发到其他ChannelPipeline中的下一个处理程序
2. ChannelHandler本身没有提供很多的方法，因为这个接口有多个的方法需要实现，方便时间可以继承它的子类
3. ChannelPipeline提供了ChannelHandler链的容器。以客户端应用程序为例，如果事件的运动方向是从客户端到服务端的，那么我们称这些事件为出站的，即客户端发送给服务端的数据会通过pipeline中的一系列 ChannelOutboundHandler，并被这些Handler处理，反之则称为入站的

## 六，Pipeline，ChannelPipeline

1. ChannelPipeline是一个Handler的集合，它负责处理和拦截inbound或者 outbound的事件和操作，相当于一个贯穿Netty的链。（也可以这样理解： ChannelPipeline是保存ChannelHandler的List，用于处理或拦截Channel的入站事件和出站操作）

2. ChannelPipeline实现了一种高级形式的拦截过滤器模式，使用户可以完全控制事件的处理方式，以及Channel中各个的ChannelHandler如何相互交互

3. 在Netty中每个Channel都有且仅有一个ChannelPipeline与之对应

   ![](F:\截图\屏幕截图 2023-06-30 171241.png)

   一个Channel包含了一个ChannelPipeline，而ChannelPipeline中又维护了一个由ChannelHandlerContext组成的双向链表，并且每个ChannelHandlerContext中又关联着一个ChannelHandler

   入站事件和出站事件在一个双向链表中，入站事件会从链表head往后传递到最后一个入站的handler，出站事件会从链表tail往前传递到最前一个出站的handler，两种类型的handler互不干扰

## 七，ChannelHandlerContext

1. 保存Channel相关的所有上下文信息，同时关联一个ChannelHandler对象
2. 即ChannelHandlerContext中包含一个具体的事件处理器ChannelHandler，同时ChannelHandlerContext中也绑定了对应的pipeline和Channel的信息，方便对ChannelHandler进行调用
3. 常用方法
   - ChannelFuture close()；关闭通道
   - ChannelOutboundInvoker flush()；刷新
   - ChannelFuture writeAndFlush(Object msg)；将数据写到ChannelPipeline中当前ChannelHandler的下一个ChannelHandler开始处理（出站）

## 八，ChannelOption

1. Netty在创建Channel实例后，一般都要设置ChannelOption参数

2. ChannelOption参数如下

   - ChannelOption.SO_BACKLOG

     对应TCP/IP协议listen函数中的backlog参数，用来初始化服务器可连接队列大小。服务端处理客户端连接请求是顺序处理的，所以同一时间只能处理一个客户端连接。多个客户端来的时候，服务端将不能处理的客户端连接请求放在队列中等待处理，backlog参数指定了队列的大小。

   - ChannelOption.SO_KEEPALIVE

     一直保持连接活动状态

## 九，EventLoopGroup，NioEventLoopGroup

1. EventLoopGroup是一组EventLoop的抽象，Netty为了更好的利用多核CPU资源，般会有多个EventLoop同时工作，每个EventLoop维护着一个Selector实例
2.  EventLoopGroup提供next接口，可以从组里面按照一定规则获取其中一个 EventLoop来处理任务。在Netty服务器端编程中，我们一般都需要提供两个 EventLoopGroup，例如：BossEventLoopGroup和WorkerEventLoopGroup
3. 通常一个服务端口即一个ServerSocketChannel对应一个Selector和一个EventLoop线程。BossEventLoop负责接收客户端的连接并将SocketChannel交给 WorkerEventLoopGroup来进行IO处理

## 十，Unpooled

1. Netty提供一个专门用来操作缓冲区（即Netty的数据容器）的工具类

2. ```java
   //创建一个ByteBuf，包含一个byte[10]
   ByteBuf buf = Unpooled.buffer(10);
   //存入数据
   //可写区域为writerIndex到capacity
   for (int i = 0; i < 10; i++) {
       buf.writeByte(i);
   }
   //Netty的buffer中不需要使用flip反转
   //取出数据
   //可读区域为readerIndex到writerIndex
   for (int i = 0; i < buf.writerIndex(); i++) {
       System.out.println(buf.readByte());
   }
   ```

3. ```java
   ByteBuf buf = Unpooled.copiedBuffer("hello", CharsetUtil.UTF_8);
   if (buf.hasArray()) {
       System.out.println(buf.readableBytes());
       byte[] array = buf.array();
       System.out.println(new String(array, 0, buf.readableBytes(), CharsetUtil.UTF_8));
   }
   ```

# 第四章 WebSocket

1. 服务器

   ```java
   public static void main(String[] args) {
       NioEventLoopGroup bossGroup = new NioEventLoopGroup(1);
       NioEventLoopGroup workerGroup = new NioEventLoopGroup();
   
       ServerBootstrap bootstrap = new ServerBootstrap();
       try {
           ChannelFuture channelFuture = bootstrap.group(bossGroup, workerGroup)
                   .channel(NioServerSocketChannel.class)
                   .option(ChannelOption.SO_BACKLOG, 128)
                   .childOption(ChannelOption.SO_KEEPALIVE, true)
                   .handler(new LoggingHandler(LogLevel.INFO))
                   .childHandler(new ChannelInitializer<SocketChannel>() {
                       @Override
                       protected void initChannel(SocketChannel ch) throws Exception {
                           ChannelPipeline pipeline = ch.pipeline();
                           //websocket基于http协议，需要加入http的编码和解码器
                           pipeline.addLast(new HttpServerCodec());
                           //以块的方式写，添加ChunkedWriteHandler处理器
                           pipeline.addLast(new ChunkedWriteHandler());
                           //因为http数据在传输过程中是分段的，HttpObjectAggregator可以将多个段聚合
                           //因此当浏览器发送大量数据的时候，会出现多次http请求
                           pipeline.addLast(new HttpObjectAggregator(8192));
                           //WebSocket的数据是以帧（frame）的形式传递的
                           //WebSocketServerProtocolHandler的核心功能是将http协议升级为ws协议，保持长连接
                           pipeline.addLast(new WebSocketServerProtocolHandler("/"));
                           pipeline.addLast(null);
                       }
                   }).bind(9999).sync();
           channelFuture.channel().closeFuture().sync();
       } catch (InterruptedException e) {
           e.printStackTrace();
       } finally {
           bossGroup.shutdownGracefully();
           workerGroup.shutdownGracefully();
       }
   }
   ```

2. 处理器

   ```java
   public class MyTextWebSocketFrameHandler extends SimpleChannelInboundHandler<TextWebSocketFrame> {
   
       //处理消息
       @Override
       protected void channelRead0(ChannelHandlerContext ctx, TextWebSocketFrame msg) throws Exception {
           System.out.println("服务器收到信息:" + msg.text());
           ctx.channel().writeAndFlush(new TextWebSocketFrame(new Date().toString()));
       }
   
       //收到连接
       @Override
       public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
           System.out.println("收到连接:" + ctx.channel().id().asLongText());
       }
   
       //连接断开
       @Override
       public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
           System.out.println("连接断开:" + ctx.channel().id().asLongText());
       }
   
       @Override
       public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
           cause.printStackTrace();
           ctx.channel().close();
       }
   }
   ```


# 第五章 Protobuf

## 一，基本介绍

1. Protobuf是Google发布的开源项目，全称Google Protocol Buffers，是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，或者说序列化，适合做数据存储或RPC数据交换格式
2. Protobuf是以message的方式来管理数据的
3. 支持夸平台，跨语言
4. 高性能，搞可靠性
5. 使用Protobuf编译器能自动生成代码，Protobuf是将类的定义使用.proto文件进行描述。
6. 然后通protoc.exe编译器根据.proto自动生成.java文件

## 二，案例

1. 编写proto文件

   ```protobuf
   syntax = "proto3";//版本
   option java_outer_classname = "StudentPojo";//java的类名
   //protobuf使用message管理数据
   message Student {//会在StudentPojo外部类生成一个内部类Student
     int32 id = 1;//定义一个数据，类型为int32，名称为id，属性序号为1（不是值）
     string name = 2;
     int32 age = 3;
   }
   ```

2. 使用protoc.exe将proto文件编译成java文件

   ```
   protoc.exe --java_out=. Student.proto
   ```

3. 客户端加入Protobuf的编码器

   ```java
   public static void main(String[] args) throws InterruptedException {
       //客户端需要一个事件循环组
       NioEventLoopGroup eventExecutors = new NioEventLoopGroup();
       try {
           //创建客户端启动对象
           Bootstrap bootstrap = new Bootstrap();
           bootstrap.group(eventExecutors) //设置线程组
                   .channel(NioSocketChannel.class)    //设置客户端通道的实现类
                   .handler(new ChannelInitializer<SocketChannel>() {
                       @Override
                       protected void initChannel(SocketChannel socketChannel) throws Exception {
                           ChannelPipeline pipeline = socketChannel.pipeline();
                           pipeline.addLast(new ProtobufEncoder());
                           pipeline.addLast(new NettyClientHandler());
                       }
                   });
           //连接服务端
           ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 9999).sync();
           //对关闭通道进行监听
           channelFuture.channel().closeFuture().sync();
       } finally {
           eventExecutors.shutdownGracefully();
       }
   }
   ```

4. 客户端发送数据

   ```java
   public class NettyClientHandler extends ChannelInboundHandlerAdapter {
       /**
        * 当通道就绪就会触发
        * @param ctx
        * @throws Exception
        */
       @Override
       public void channelActive(ChannelHandlerContext ctx) throws Exception {
           StudentPojo.Student student = StudentPojo.Student.newBuilder().setId(1).setName("wgx").setAge(24).build();
           ctx.writeAndFlush(student);
       }
   }
   ```

5. 服务端添加Protobuf的解码器

   ```java
   public static void main(String[] args) throws InterruptedException {
       //创建BossGroup，只处理连接请求
       NioEventLoopGroup bossGroup = new NioEventLoopGroup();
       //创建WorkerGroup，处理业务
       NioEventLoopGroup workerGroup = new NioEventLoopGroup();
       try {
           //创建服务器端的启动对象，配置参数
           ServerBootstrap bootstrap = new ServerBootstrap();
           bootstrap.group(bossGroup, workerGroup) //设置两个线程组
                   .channel(NioServerSocketChannel.class)  //设置使用NioServerSocketChannel作为服务器的通道实现
                   .option(ChannelOption.SO_BACKLOG, 128) //设置线程队列得到连接个数
                   .childOption(ChannelOption.SO_KEEPALIVE, true)  //设置保持活动连接状态
                   .childHandler(new ChannelInitializer<SocketChannel>() {
                       @Override
                       protected void initChannel(SocketChannel socketChannel) throws Exception {
                           //添加处理器
                           ChannelPipeline pipeline = socketChannel.pipeline();
                           pipeline.addLast(new ProtobufDecoder(StudentPojo.Student.getDefaultInstance()));
                           pipeline.addLast(new NettyServerHandler());
                       }
                   });  //给WorkerGroup的EventLoop对应的管道设置处理器
           //绑定端口并且同步，生成一个ChannelFuture
           //启动服务器
           ChannelFuture channelFuture = bootstrap.bind(9999).sync();
           //对关闭通道进行监听
           channelFuture.channel().closeFuture().sync();
       } finally {
           bossGroup.shutdownGracefully();
           workerGroup.shutdownGracefully();
       }
   }
   ```

6. 服务端读取数据

   ```java
   public class NettyServerHandler extends ChannelInboundHandlerAdapter {
       /**
        * 读取消息
        * @param ctx 上下文对象，含有管道pipeline，通道channel，地址
        * @param msg 客户端发送的数据
        */
       @Override
       public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
           StudentPojo.Student student = (StudentPojo.Student) msg;
           System.out.println("收到客户端的Student对象:[id=" + student.getId() + "\tname=" + student.getName() + "\tage=" + student.getAge() + "]");
       }
   }
   ```

## 三，传输多种类型

1. 编写包含多个类型的proto文件

   ```protobuf
   syntax = "proto3";
   option optimize_for = SPEED;//加快解析
   option java_package = "protobuf.optimize";//指定生成到哪个包下
   option java_outer_classname = "MessagePojo";//指定外部类名称
   
   //可以使用message管理其他message
   message MyMessage {
     //定义一个枚举类型
     enum DataType {
       StudentType = 0;//在proto3中，枚举类型从0开始
       TeacherType = 1;
     }
     //用data_type来表示传递的是哪一个枚举类型
     DataType data_type = 1;
     //表示每次枚举类型最多只能出现其中一个
     oneof dataBody {
       Student student = 2;
       Teacher teacher = 3;
     }
   }
   
   message Student {
     int32 id = 1;
     string name = 2;
     int32 age = 3;
   }
   message Teacher {
     int32 id = 1;
     string name = 2;
   }
   ```

2. 客户端发送数据

   ```java
   public class NettyClientHandler extends ChannelInboundHandlerAdapter {
       /**
        * 当通道就绪就会触发
        * @param ctx
        * @throws Exception
        */
       @Override
       public void channelActive(ChannelHandlerContext ctx) throws Exception {
           int random = new Random().nextInt(3);
           MessagePojo.MyMessage message;
           //随机创建Student或者Teacher
           if (random == 0) {
               message = MessagePojo
                       .MyMessage
                       .newBuilder()
                       .setDataType(MessagePojo.MyMessage.DataType.StudentType)
                       .setStudent(MessagePojo.Student.newBuilder().setId(1).setName("wgx").setAge(24).build()).build();
           } else {
               message = MessagePojo
                       .MyMessage
                       .newBuilder()
                       .setDataType(MessagePojo.MyMessage.DataType.TeacherType)
                       .setTeacher(MessagePojo.Teacher.newBuilder().setId(2).setName("WGX").build()).build();
           }
           ctx.writeAndFlush(message);
       }
   }
   ```

3. 服务端添加解码器

   ```java
   @Override
   protected void initChannel(SocketChannel socketChannel) throws Exception {
       //添加处理器
       ChannelPipeline pipeline = socketChannel.pipeline();
       pipeline.addLast(new ProtobufDecoder(MessagePojo.MyMessage.getDefaultInstance()));
       pipeline.addLast(new NettyServerHandler());
   }
   ```

4. 服务端读取数据

   ```java
   public class NettyServerHandler extends SimpleChannelInboundHandler<MessagePojo.MyMessage> {
       @Override
       protected void channelRead0(ChannelHandlerContext ctx, MessagePojo.MyMessage msg) throws Exception {
           if (msg.hasStudent()) {
               MessagePojo.Student student = msg.getStudent();
               System.out.println("收到客户端的Student对象:[id=" + student.getId() + ",name=" + student.getName() + ",age=" + student.getAge() + "]");
           } else {
               MessagePojo.Teacher teacher = msg.getTeacher();
               System.out.println("收到客户端的Teacher对象:[id=" + teacher.getId() + ",name=" + teacher.getName() + "]");
           }
       }
   }
   ```

# 第六章 Handler链调用机制

## 一，入站和出站

1. Netty的主要组件有Channel、EventLoop、ChannelFuture、 ChannelHandler、ChannelPipe等
2. ChannelHandler充当了处理入站和出站数据的应用程序逻辑的容器。例如，实现ChannellnboundHandler接口（或ChannellnboundHandlerAdapter）,你就可以接收入站事件和数据，这些数据会被业务逻辑处理。当要给客户端发送响应时，也可以从ChannellnboundHandler冲刷数据。业务逻辑通常写在一个或者多个ChannellnboundHandler中。ChannelOutboundHandler原理一样，只不过它是用来处理出站数据的
3. ChannelPipeline提供了ChannelHandler链的容器。以客户端应用程序为例，如果事件的运动方向是从客户端到服务端的，那么我们称这些事件为出站的，即客户端发送给服务端的数据会通过pipeline中的一系列 ChannelOutboundHandler，并被这些Handler处理，反之则称为入站的

## 二，编码器和解码器

1. 当Netty发送或者接受一个消息的时候，就将会发生一次数据转换。入站消息会被解码：从字节转换为另一种格式（比如java对象），如果是出站消息，它会被编码成字节。
2.  Netty提供一系列实用的编解码器，他们都实现了ChannellnboundHadnler或者 ChannelOutboundHandler接口。在这些类中，channelRead方法已经被重写了。以入站为例，对于每个从入站Channel读取的消息，这个方法会被调用。随后，它将调用由解码器所提供的decod()方法进行解码，并将已经解码的字节转发给ChannelPipeline中的下一个ChannellnboundHandler。

## 三，其他常用的解码器和编码器

1. LineBasedFrameDecoder：这个类在Netty内部也有使用，它使用行尾控制字符(\n或者\r\n)作为分隔符来解析数据。 

2. DelimiterBasedFrameDecoder：使用自定义的特殊字符作为消息的分隔符。 

3. HttpObjectDecoder：一个HTTP数据的解码器

4. LengthFieldBasedFrameDecoder：通过指定长度来标识整包消息，这样就可以自动的处理黏包和半包消息。

5. 其他编码器

   ![](F:\截图\屏幕截图 2023-07-07 172624.png)



