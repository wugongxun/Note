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

     
