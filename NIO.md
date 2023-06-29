# 第一章 Java NIO概述

Java NIO(New IO或Non Blocking IO)是从Java1.4版本开始引入的一个新的IO API,可以替代标准的Java IO API。NIO支持面向缓冲区的、基于通道的IO操作。NO将以更加高效的方式进行文件的读写操作。

1. 阻塞IO

   通常在进行同步I/O操作时，如果读取数据，代码会阻塞直至有可供读取的数据。同样，写入调用将会阻塞直至数据能够写入。传统的Server/Client模式会基于TPR(Thread per Request),服务器会为每个客户端请求建立一个线程，由该线程单独负责处理一个客户请求。这种模式带来的一个问题就是线程数量的剧增，大量的线程会增大服务器的开销。大多数的实现为了避免这个问题，都采用了线程池模型，并设置线程池线程的最大数量，这由带来了新的问题，如果线程池中有100个线程，而有100个用户都在进行大文件下载，会导致第101个用户的请求无法及时处理，即便第101个用户只想请求一个几KB大小的页面。

2. 非阻塞IO

   NIO中非阻塞l/O采用了基于Reactor模式的工作方式，I/O调用不会被阻塞，相反是注册感兴趣的特定I/O事件，如可读数据到达，新的套接字连接等等，在发生特定事件时，系统再通知我们。NIO中实现非阻塞I/O的核心对象就是Selector,Selector就是注册各种I/O事件地方，而且当我们感兴趣的事件发生时，就是这个对象告诉我们所发生的事件。

3. NIO概述

   Java NIO有以下几个核心部分组成

   - Channels
   - Buffers
   - Selectors

   虽然Java NIO中除此之外还有很多类和组件，但Channel,Buffer和Selector构成了核心的APl。其它组件，如Pipe和FileLock,只不过是与三个核心组件共同使用的工具类。

## 一，Channel

首先说一下Channel,可以翻译成"通道”。Channel和IO中的Stream(流)是差不多一个等级的。只不过Stream是单向的，譬如：InputStream,OutputStream.而Channel是双向的，既可以用来进行读操作，，又可以用来进行写操作。NlO中的Channel的主要实现有：FileChannel、DatagramChannel.SocketChannel和ServerSocketChannel,这里看名字就可以猜出个所以然来：分别可以对应文件IO、UDP和TCP(Server和Client)。

## 二，Buffer

NlO中的关键Buffer实现有：ByteBuffer,CharBuffer,DoubleBuffer,FloatBuffer,IntBuffer,LongBuffer,ShortBuffer,分别对应基本数据类型：byte,char,double,float,int,long,short.

## 三，Selector

Selector运行单线程处理多个Channel,如果你的应用打开了多个通道，但每个连接的流量都很低，使用Selector就会很方便。例如在一个聊天服务器中。要使用Selector,得向Selector注册Channel,然后调用它的select()方法。这个方法会一直阻塞到某个注册的通道有事件就绪。一旦这个方法返回，线程就可以处理这些事件，事件的例子有如新的连接进来、数据接收等。

# 第二章 Channel

## 一，概述

Channel是一个通道，可以通过它读取和写入数据，它就像水管一样，网络数据通过Channel读取和写入。通道与流的不同之处在于通道是双向的，流只是在一个方向上移动（一个流必须是InputStream或者OutputStream的子类），而且通道可以用于读、写或者同时用于读写。因为Channel是全双工的，所以它可以比流更好地映射底层操作系统的API。

NIO中通过channel封装了对数据源的操作，通过channel我们可以操作数据源，但又不必关心数据源的具体物理结构。这个数据源可能是多种的。比如，可以是文件，也可以是网络socket。.在大多数应用中，channel与文件描述符或者socket是一一对应的。Channel用于在字节缓冲区和位于通道另一侧的实体（通常是一个文件或套接字)之间有效地传输数据。

与缓冲区不同，通道API主要由接口指定。不同的操作系统上通道实现(Channel Implementation)会有根本性的差异，所以通道API仅仅描述了可以做什么。因此很自然地，通道实现经常使用操作系统的本地代码。通道接口允许您以一种受控且可移植的方式来访问底层的/O服务。

Channel是一个对象，可以通过它读取和写入数据。拿NIO与原来的I/O做个比较，通道就像是流。所有数据都通过Buffer对象来处理。您永远不会将字节直接写入通道中，相反，您是将数据写入包含一个或者多个字节的缓冲区。同样，您不会直接从通道中读取字节，而是将数据从通道读入缓冲区，再从缓冲区获取这个字节。

Java NIO的通道类似流，但又有些不同：

- 既可以从通道中读取数据，又可以写数据到通道。但流的读写通常是单向的。
- 通道可以异步地读写。
- 通道中的数据总是要先读到一个Buffer,或者总是要从一个Buffer中写入。

## 二，Channel实现

1. FileChannel：从文件中读写数据
2. DatagramChannel：能通过UDP读写网络中的数据
3. SocketChannel：能通过TCP读写网络中的数据
4. ServerSocketChannel可以监听新进来的TCP连接，将Web服务器那样。对每一个新进来的连接都会创建一个SocketChannel

## 三，FileChannel

FileChannel类可以实现常用的read,write以及scatter/gather操作，同时它也提供了很多专用于文件的新方法。这些方法中的许多都是我们所熟悉的文件操作。

1. 读操作

   ```java
   //实现读取操作
   public class FileChannelDemo {
       public static void main(String[] args) throws IOException {
           //创建FileChannel
           RandomAccessFile accessFile = new RandomAccessFile("F:\\111.txt", "rw");
           FileChannel channel = accessFile.getChannel();
           //创建Buffer
           ByteBuffer buf = ByteBuffer.allocate(1024);
           //读取数据到buffer中
           int read = channel.read(buf);
           while (read != -1) {
               System.out.println("读取到了:" + read);
               buf.flip();
               while (buf.hasRemaining()) {
                   System.out.println((char) buf.get());
               }
               buf.clear();
               read = channel.read(buf);
           }
           accessFile.close();
           System.out.println("读取结束");
       }
   }
   ```

2. 写操作

   ```java
   public class FileChannelDemo2 {
       public static void main(String[] args) throws IOException {
           //打开FileChannel
           RandomAccessFile accessFile = new RandomAccessFile("F:\\222.txt", "rw");
           FileChannel channel = accessFile.getChannel();
           //创建Buffer对象
           ByteBuffer buffer = ByteBuffer.allocate(1024);
           buffer.clear();
           //写入数据
           String data = "wgx";
           buffer.put(data.getBytes(StandardCharsets.UTF_8));
           buffer.flip();
           //FileChannel完成最终写入
           while (buffer.hasRemaining()) {
               channel.write(buffer);
           }
           channel.close();
       }
   }
   ```

3. FileChannel的position方法

   有时可能需要在FileChannel的某个特定位置进行数据的读/写操作。可以通过调用position()方法获取FileChannel的当前位置。也可以通过调用position(long pos)方法设置FileChannel的当前位置。

4. FileChannel的size方法

    FileChannel的size()方法将返回该实例所关联文件的大小

5. FileChannel的truncate方法

   可以使用FileChannel.truncate()方法截取一个文件。截取文件时，文件将中指定长度后面的部分将被删除。

   如：channel.truncate(1024);截取文件的前1024个字节

6. FileChannel的force方法

   FileChannel.force()方法将通道里尚未写入磁盘的数据强制写到磁盘上。出于性能方面的考虑，操作系统会将数据缓存在内存中，所以无法保证写入到FileChannel里的数据一定会即时写到磁盘上。要保证这一点，需要调用force()方法。force()方法有一个boolean类型的参数，指明是否同时将文件元数据（权限信息等）写到磁盘上。

7. FileChannel的transferTo和transferFrom方法

   通道之间的数据传输：

   如果两个通道中有一个是FileChannel,那你可以直接将数据从一个channel传输到另外一个channel。、

   ```java
   public class FileChannelDemo3 {
       public static void main(String[] args) throws IOException {
           //创建两个FileChannel
           RandomAccessFile accessFile1 = new RandomAccessFile("F:\\111.jpg", "rw");
           FileChannel fromChannel = accessFile1.getChannel();
   
           RandomAccessFile accessFile2 = new RandomAccessFile("F:\\222.jpg", "rw");
           FileChannel toChannel = accessFile2.getChannel();
   
           //fromChannel传输到toChannel
           long position = 0;
           long size = fromChannel.size();
           toChannel.transferFrom(fromChannel, position, size);
   
           accessFile1.close();
           accessFile2.close();
       }
   }
   ```

## 四，SocketChannel

- 新的socket通道类可以运行非阻塞模式并且是可选择的，可以激活大程序（如网络服务器和中间件组件)巨大的可伸缩性和灵活性。本节中我们会看到，再也没有为每个socket连接使用一个线程的必要了，也避免了管理大量线程所需的上下文交换开销。借助新的NIO类，一个或几个线程就可以管理成百上干的活动socket连接了并且只有很少甚至可能没有性能损失。所有的socket通道类(DatagramChannel、SocketChannel和ServerSocketChannel)都继承了位于java.nio.channels.spi包，中的AbstractSelectableChannel。这意味着我们可以用一个Selector对象来执行socket通道的就绪选择(readiness selection)。

- 请注意DatagramChannel和SocketChannel实现定义读和写功能的接口而ServerSocketChannel不实现。ServerSocketChannel负责监听传入的连接和创建新的SocketChannel对象，它本身从不传输数据。

- 在我们具体讨论每一种socket通道前，您应该了解socket和socket通道之间的关系。通道是一个连接I/O服务导管并提供与该服务交互的方法。就某个socket而言，它不会再次实现与之对应的socket通道类中的socket协议APl，而java.net中已经存在的socket通道都可以被大多数协议操作重复使用。~全部socket通道类(DatagramChannel、.SocketChannel和ServerSocketChannel)在被实例化时都会创建一个对等socket对像。这些是我们所熟悉的来自java.net的类(Socket、ServerSocket和DatagramSocket),它们已经被更新以识别通道。对等socket可以通过调用socket()方法从一个通道上获取。此外，这三个java.net类现在都有getChannel()方法。

- 要把一个socket通道置于非阻塞模式，我们要依靠所有socket通道类的公有超级类：SelectableChannel。就绪选择(readiness selection)是一种可以用来查询通道的机制，该查询可以判断通道是否准备好执行一个目标操作，如读或写。非阻塞I/O和可选择性是紧密相连的，那也正是管理阻塞模式的API代码要在SelectableChannel超级类中定义的原因。

  设置或重新设置一个通道的阻塞模式是很简单的，只要调用configureBlocking()方法即可，传递参数值为true则设为阻塞模式，参数值为false值设为非阻塞模式。可以通过调用isBlocking()方法来判断某个socket通道当前处于哪种模式。

1. ServerSocketChannel

   ServerSocketChannel是一个基于通道的socket监听器。它同我们所熟悉的java.net.ServerSocket执行相同的任务，不过它增加了通道语义，因此能够在非阻塞模式下运行。

   由于ServerSocketChannel没有bind()方法，因此有必要取出对等的socket并使用它来绑定到一个端口以开始监听连接。我们也是使用对等ServerSocket的API来根据需要设置其他的socket选项。

   同java.net.ServerSocket一样，ServerSocketChannel也有accept()方法。一旦创建了一个ServerSocketChannel并用对等socket绑定了它，然后您就可以在其中一个上调用accept()。如果您选择在ServerSocket上调用accept()方法，那么它会同任何其他的ServerSocket表现一样的行为：总是阻塞并返回一个java.net.Socket对象。如果您选择在ServerSocketChannel上调用accept()方法则会返回SocketChannel类型的对象，返回的对象能够在非阻塞模式下运行。

   换句话说：ServerSocketChannel的accept()方法会返回SocketChannel类型对象，SocketChannel可以在非阻塞模式下运行。

   ```java
   //ServerSocketChannel实现
   public class ServerSocketChannelDemo {
       public static void main(String[] args) throws IOException, InterruptedException {
           int port = 9999;
   
           //创建buffer
           ByteBuffer buffer = ByteBuffer.wrap("hello".getBytes(StandardCharsets.UTF_8));
   
           //创建ServerSocketChannel
           ServerSocketChannel ssc = ServerSocketChannel.open();
           //绑定
           ssc.socket().bind(new InetSocketAddress(port));
           //设置非阻塞模式
           ssc.configureBlocking(false);
   
   
           //监听
           while (true) {
               SocketChannel sc = ssc.accept();
               System.out.println("等待连接");
               if (sc == null) {
                   System.out.println("没有连接传入");
                   TimeUnit.SECONDS.sleep(2);
               } else {
                   System.out.println("已连接");
                   buffer.rewind();//指针指向0
                   sc.write(buffer);//写入数据
                   sc.close();
               }
           }
       }
   }
   ```

2. SocketChannel

   Java NIO中的SocketChannel是一个连接到TCP网络套接字的通道。SocketChannel是一种面向流连接sockets套接字的可选择通道。从这里可以看出：

   - SocketChannel是用来连接Socket套接字
   - SocketChannel主要用途用来处理网络I/O的通道
   - SocketChannel是基于TCP连接传输
   - SocketChannel实现了可选择通道，可以被多路复用的

3. SocketChannel的特征

   - 对于已经存在的socket不能创建SocketChannel

   - SocketChannel中提供的open接口创建的Channel并没有进行网络级联，需要使用connect接口连接到指定地址

   - 未进行连接的SocketChannel执行l/O操作时，会抛出NotYetConnectedException

   - SocketChannel支持两种I/O模式：阻塞式和非阻塞式

   - SocketChannel支持异步关闭。如果SocketChannel在一个线程上read阻塞，另一个线程对该SocketChannel调用shutdownInput则读阻塞的线程将返回-1表示没有读取任何数据；如果SocketChannel在一个线程上write阻塞，另一个线程对该SocketChannel调用shutdownWrite,则写阻塞的线程将抛出AsynchronousCloseException

   - SocketChannel支持设定参数

     SO_SNDBUF：套接字发送缓冲区大小

     SO_RCVBUF：套接字接收缓冲区大小

     SO_KEEPALIVE：保活连接

     SO_REUSEADDR：复用地址

     SO_LINGER：有数据传输时延缓关闭Channel(只有在非阻塞模式下有用)

     TCP_NODELAY：禁用Nagle算法

4. SocketChannel的使用

   ```java
   public class SocketChannelDemo {
       public static void main(String[] args) throws IOException {
           //创建SocketChannel
           SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 9999));
   //        SocketChannel socketChannel = SocketChannel.open();
   //        socketChannel.connect(new InetSocketAddress("www.baidu.com", 80));
   
           //连接检验
   //        socketChannel.isOpen();//测试是否为open状态
   //        socketChannel.isConnected();//测试是否已经被连接
   //        socketChannel.isConnectionPending();//测试是否正在进行连接
   //        socketChannel.finishConnect();//校验正在进行套接字连接的SocketChannel是否已经完成连接
   
           //设置阻塞与非阻塞模式
           socketChannel.configureBlocking(false);
   
           //设置和获取参数
   //        socketChannel.setOption(StandardSocketOptions.SO_KEEPALIVE, true);
   //        socketChannel.getOption(StandardSocketOptions.SO_REUSEADDR);
   
           //读操作
           ByteBuffer buffer = ByteBuffer.allocate(16);
           int read = socketChannel.read(buffer);
           while (read != -1) {
               System.out.println("读到了" + read);
               buffer.flip();
               while (buffer.hasRemaining()) {
                   System.out.println((char) buffer.get());
               }
               buffer.clear();
               read = socketChannel.read(buffer);
           }
           System.out.println();
           socketChannel.close();
       }
   }
   ```

5. DatagramChannel

   正如SocketChannel对应Socket,ServerSocketChannel对应ServerSocket,每一个DatagramChannel对象也有一个关联的DatagramSocket对象。正如SocketChannel模拟连接导向的流协议（如TCP/IP),DatagramChannel则模拟包导向的无连接协议（如UDP/IP)。DatagramChannel是无连接的，每个数据报(datagram)都是一个自包含的实体，拥有它自己的目的地址及不依赖其他数据报的数据负载。与面向流的的socket不同，DatagramChannel可以发送单独的数据报给不同的目的地址。同样，DatagramChannel对象也可以接收来自任意地址的数据包。每个到达的数据报都含有关于它来自何处的信息（源地址）

6. DatagramChannel实现

   ```java
   public class DatagramChannelDemo {
       //发送
       @Test
       public void sendDatagram() throws IOException, InterruptedException {
           //创建DatagramChannel
           DatagramChannel sendChannel = DatagramChannel.open();
           InetSocketAddress sendAddress = new InetSocketAddress("127.0.0.1", 9999);
   
           //发送
           while (true) {
               ByteBuffer buffer = ByteBuffer.wrap("hello".getBytes(StandardCharsets.UTF_8));
               sendChannel.send(buffer, sendAddress);
               System.out.println("发送完毕");
               TimeUnit.SECONDS.sleep(1);
           }
       }
   
       //接受
       @Test
       public void receiveDatagram() throws IOException {
           //创建DatagramChannel
           DatagramChannel receiveChannel = DatagramChannel.open();
           InetSocketAddress receiveAddress = new InetSocketAddress("127.0.0.1", 9999);
   
           //绑定
           receiveChannel.bind(receiveAddress);
   
           //接受
           ByteBuffer buffer = ByteBuffer.allocate(1024);
           while (true) {
               buffer.clear();
               receiveChannel.receive(buffer);
               buffer.flip();
               System.out.println(Charset.forName("UTF-8").decode(buffer));
           }
       }
   }
   ```

7. Datagram连接

   ```java
   //连接
   @Test
   public void connect() throws IOException {
       //创建DatagramChannel
       DatagramChannel connectChannel = DatagramChannel.open();
   
       //绑定
       connectChannel.bind(new InetSocketAddress(9999));
   
       //连接
       connectChannel.connect(new InetSocketAddress("127.0.0.1", 9999));
   
       //write方法
       connectChannel.write(ByteBuffer.wrap("hello".getBytes(StandardCharsets.UTF_8)));
   
       //read方法
       ByteBuffer buffer = ByteBuffer.allocate(1024);
       while (true) {
           buffer.clear();
           connectChannel.read(buffer);
           buffer.flip();
           System.out.println(Charset.forName("UTF-8").decode(buffer));
       }
   }
   ```

## 五，Scatter/Gather

Java NIO开始支持scatter/gather，scatter/gather用于描述从Channel中读取或者写入到Channel的操作。

**分散（scatter）**从Channel中读取是指在读操作时将读取的数据写入多个buffer中。因此，Channel将从Channel中读取的数据"分散(scatter)”"到多个Buffer中。

**聚集（gather）**写入Channel是指在写操作时将多个buffer的数据写入同一个Channel,因此，Channel将多个Buffer中的数据“聚集(gather)”后发送到Channel。

scatter/gather经常用于需要将传输的数据分开处理的场合，例如传输一个由消息头和消息体组成的消息，你可能会将消息体和消息头分散到不同的buffer中，这样你可以方便的处理消息头和消息体。

# 第三章 Buffer

## 一，概述

Java NIO中的Buffer用于和NIO通道进行交互。数据是从通道读入缓冲区，从缓冲区写入到通道中的。

缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存。这块内存被包装成NIO Buffer对象，并提供了一组方法，用来方便的访问该块内存。缓冲区实际上是一个容器对象，更直接的说，其实就是一个数组，在NIO库中，所有数据都是用缓冲区处理的。在读取数据时，它是直接读到缓冲区中的，在写入数据时，它也是写入到缓冲区中的；任何时候访问NIO中的数据，都是将它放到缓冲区中。而在面向流I/O系统中，所有数据都是直接写入或者直接将数据读取到Stream对象中。

在NIO中，所有的缓冲区类型都继承于抽象类Buffer,最常用的就是ByteBuffer,对于Java中的基本类型，基本都有一个具体Buffer类型与之相对应。

## 二，Buffer的基本使用

1. 使用Buffer读写数据，一般遵循以下四个步骤

   - 写入数据到Buffer
   - 调用flip()方法
   - 从Buffer中读取数据
   - 调用clear()方法，或者compact()方法

   当向buffer写入数据时，buffer会记录下写了多少数据。一旦要读取数据，需要通过flip()方法将Buffer从写模式切换到读模式。在读模式下，可以读取之前写入到buffer的所有数据。一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。有两种方式能清空缓冲区：调用clear()或compact()方法。clear()方法会清空整个缓冲区。compact()方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。

2. 使用Buffer实例

   - 使用ByteBuffer

     ```java
     @Test
     public void buffer1() throws IOException {
         //FileChannel
         RandomAccessFile file = new RandomAccessFile("F:\\111.txt", "rw");
         FileChannel channel = file.getChannel();
     
         //创建Buffer
         ByteBuffer buffer = ByteBuffer.allocate(1024);
     
         //读
         int read = channel.read(buffer);
     
         while (read != -1) {
             //read模式
             buffer.flip();
     
             while (buffer.hasRemaining()) {
                 System.out.println((char) buffer.get());
             }
             buffer.clear();
             channel.read(buffer);
         }
     }
     ```

   - 使用IntBuffer

     ```java
     @Test
     public void buffer2() {
         IntBuffer buffer = IntBuffer.allocate(8);
     
         //放数据
         for (int i = 0; i < buffer.capacity(); i++) {
             buffer.put(i);
         }
     
         //重置缓冲区
         buffer.flip();
     
         //读数据
         while (buffer.hasRemaining()) {
             int value = buffer.get();
             System.out.println(value);
         }
     }
     ```

3. capacity、position和limit

   position和limit的含义取决于Buffer处在读模式还是写模式。不管Buffer处在什么模式，capacity的含义总是一样的。

   ![](F:\截图\屏幕截图 2022-07-14 101017.png)

   - capacity

     作为一个内存块，Buffer有一个固定的大小值，也叫“capacity”。你只能往里写capacity个byte、long,char等类型。一旦Buffer满了，需要将其清空（通过读数据或者清除数据)才能继续写数据往里写数据。

   - position

     写数据到Buffer中时，position表示写入数据的当前位置，position的初始值为0当一个byte、long等数据写到Buffer后，position会向下移动到下一个可插入数据的Buffer单元。position最大可为capacity-1(因为position的初始值为0)

     读数据到Buffer中时，position表示读入数据的当前位置，如position=2时表示已开始读入了3个byte，或从第3个byte开始读取。通过ByteBuffer.flip()切换到读模式时position会被重置为0，当Buffer从position读入数据后，position会下移到下一个可读入的数据Buffer单元。

   - limit

     写数据时，limit表示可对Buffer最多写入多少个数据。写模式下，limit等于Buffer的capacity。

     读数据时，limit表示Buffer里有多少可读数据(not null的数据)，因此能读到之前写入的所有数据(limit被设置成已写数据的数量，这个值在写模式下就是position)。

## 三，Buffer分配和写数据

1. 要想获得一个Buffer对象首先要进行分配。每一个Buffer类都有一个allocate方法。

2. 向Buffer中写数据

   - 从Channel写到Buffer
   - 通过Buffer的put()方法写到Buffer

3. flip()方法

   flip方法将Buffer从写模式切换到读模式。调用flip()方法会将position设回0,并将limit设置成之前position的值。换句话说，position现在用于标记读的位置，limit表示，之前写进了多少个byte、char等（现在能读取多少个byte、char等）。

4. 从Buffer中读取数据

   - 从Buffer读取数据到Channel中
   - 使用get()方法从Buffer中读取数据

## 四，Buffer的几个方法

1. rewind()方法

   Buffer.rewind(将position设回0，所以你可以重读Buffer中的所有数据。limit保持不变，仍然表示能从Buffer中读取多少个元素(byte、char等)。

2. clear()和compact()方法

   一旦读完Buffer中的数据，需要让Buffer准备好再次被写入。可以通过clear()或compact()方法来完成。

   如果调用的是clear()方法，position将被设回0，limit被设置成capacity的值。换句话说，Buffer被清空了。Buffer中的数据并未清除，只是这些标记告诉我们可以从哪里开始往Buffer里写数据。

   如果Buffer中有一些未读的数据，调用clear()方法，数据将"被遗忘”，意味着不再有任何标记会告诉你哪些数据被读过，哪些还没有。

   如果Buffer中仍有未读的数据，且后续还需要这些数据，但是此时想要先写些数据，那么使用compact()方法。

   compact()方法将所有未读的数据拷贝到Buffer起始处。然后将position设到最后一个未读元素正后面。limit属性依然像clear()方法一样，设置成capacity。现在Buffer准备好些数据了，但是不会覆盖未读的数据。

3. mark()和reset()方法

   通过调用Buffer.mark()方法，可以标记Buffer中的一个特定position。之后可以通过调用Buffer.reset()方法恢复到这个position。

## 五，缓冲区操作

1. 缓冲区分片

   在NIO中，除了可以分配或者包装一个缓冲区对象外，还可以根据现有的缓冲区对象来创建一个子缓冲区，即在现有缓冲区上切出一片来作为一个新的缓冲区，但现有的缓冲区与创建的子缓冲区在底层数组层面上是数据共享的，也就是说，子缓冲区相当于是现有缓冲区的一个视图窗口。调用slice()方法可以创建一个子缓冲区。

   ```java
   @Test
   public void buffer1() {
       ByteBuffer buffer = ByteBuffer.allocate(10);
       for (int i = 0; i < buffer.capacity(); i++) {
           buffer.put((byte) i);
       }
   
       //创建子缓冲区
       buffer.position(3);
       buffer.limit(7);
       ByteBuffer slice = buffer.slice();
   
       //修改子缓冲区的内容
       for (int i = 0; i < slice.capacity(); i++) {
           slice.put(i, (byte) (slice.get(i) * 10));
       }
   
       buffer.position(0);
       buffer.limit(buffer.capacity());
   
       //读取
       while (buffer.hasRemaining()) {
           System.out.println(buffer.get());
       }
   }
   ```

2. 只读缓冲区

   只读缓冲区非常简单，可以读取它们，但是不能向它们写入数据。可以通过调用缓冲区的asReadOnlyBuffer(方法，将任何常规缓冲区转换为只读缓冲区，这个方法返回一个与原缓冲区完全相同的缓冲区，并与原缓冲区共享数据，只不过它是只读的。如果原缓冲区的内容发生了变化，只读缓冲区的内容也随之发生变化

   ```java
   @Test
   public void buffer2() {
       ByteBuffer buffer = ByteBuffer.allocate(10);
       for (int i = 0; i < buffer.capacity(); i++) {
           buffer.put((byte) i);
       }
   
       //创建只读缓冲区
       ByteBuffer readOnlyBuffer = buffer.asReadOnlyBuffer();
   
       for (int i = 0; i < buffer.capacity(); i++) {
           buffer.put(i, (byte) (buffer.get(i) * 10));
       }
   
       readOnlyBuffer.position(0);
       readOnlyBuffer.limit(readOnlyBuffer.capacity());
   
       while (readOnlyBuffer.hasRemaining()) {
           System.out.println(readOnlyBuffer.get());
       }
   }
   ```

3. 直接缓冲区

   直接缓冲区是为加快I/O速度，使用一种特殊方式为其分配内存的缓冲区，JDK文档中的描述为：给定一个直接字节缓冲区，Java虚拟机将尽最大努力直接对它执行本机I/O操作。也就是说，它会在每一次调用底层操作系统的本机I/O操作之前（或之后），尝试避免将缓冲区的内容拷贝到一个中间缓冲区中或者从一个中间缓冲区中拷贝数据。要分配直接缓冲区，需要调用allocateDirect()方法，而不是allocate()方法，使用方式与普通缓冲区并无区别。

   ```java
   @Test
   public void buffer3() throws IOException {
       String inFilePath = "F:\\111.jpg";
       FileInputStream fileInputStream = new FileInputStream(inFilePath);
       FileChannel fileInputStreamChannel = fileInputStream.getChannel();
   
       String outFilePath = "F:\\222.jpg";
       FileOutputStream fileOutputStream = new FileOutputStream(outFilePath);
       FileChannel fileOutputStreamChannel = fileOutputStream.getChannel();
   
       //创建直接缓冲区
       ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
       //复制文件
       while (true) {
           buffer.clear();
           int read = fileInputStreamChannel.read(buffer);
           if (read == -1) {
               break;
           }
           buffer.flip();
           fileOutputStreamChannel.write(buffer);
       }
       fileInputStreamChannel.close();
       fileInputStream.close();
       fileOutputStreamChannel.close();
       fileOutputStream.close();
   }
   ```

4. 内存映射文件I/O

   内存映射文件I/O是一种读和写文件数据的方法，它可以比常规的基于流或者基于通道的I/O快的多。内存映射文件I/O是通过使文件中的数据出现为内存数组的内容来完成的，这其初听起来似乎不过就是将整个文件读到内存中，但是事实上并不是这样。一般来说，只有文件中实际读取或者写入的部分才会映射到内存中。

   ```java
   static private final int start = 0;
   static private final int size = 0;
   
   @Test
   public void buffer4() throws IOException {
       RandomAccessFile file = new RandomAccessFile("F:\\111.txt", "rw");
       FileChannel fileChannel = file.getChannel();
       MappedByteBuffer mappedByteBuffer = fileChannel.map(FileChannel.MapMode.READ_WRITE, start, size);
   
       mappedByteBuffer.put(0, (byte) 97);
       mappedByteBuffer.put(1023, (byte) 122);
       fileChannel.close();
       file.close();
   }
   ```

# 第四章 Selector

## 一，Selector简介

1. Selector和Channel的关系

   Selector一般称为选择器，也可以翻译为多路复用器。它是Java NIO核心组件中的一个，用于检查一个或多个NIO Channel(通道)的状态是否处于可读、可写。如此可以实现单线程管理多个channels,也就是可以管理多个网络链接。

   ![](F:\截图\屏幕截图 2022-07-14 134932.png)

   使用Selector的好处在于：使用更少的线程就可以来处理通道了，相比使用多个线程，避免了线程上下文的切换带来的开销

2. 可选择通道（SelectableChannel）

   - 不是所有的Channel都可以被Selector复用的。比方说，FileChannel就不能被选择器复用。判断一个Channel能被Selector复用，有一个前提：判断他是否继承了一个抽象类SelectableChanne。如果继承了SelectableChanne,则可以被复用，否则不能。
   - SelectableChannel类提供了实现通道的可选择性所需要的公共方法。它是所有支持就绪检查的通道类的父类。所有socket通道，都继承了SelectableChannel类都是可选择的，包括从管道(Pipe)对象的中获得的通道。而FileChannel类，没有继承SelectableChannel，因此不是可选通道。
   - 一个通道可以被注册到多个选择器上，但对每个选择器而言只能被注册一次。通道和选择器之间的关系，使用注册的方式完成。SelectableChannel可以被注册到Selector对象上，在注册的时候，需要指定通道的哪些操作，是Selector感兴趣的。

3. Channel注册到Selector

   - 使用Channel.register(Selector sel, int ops),方法，将-个通道注册到一个选择器时。第一个参数，指定通道要注册的选择器。第二个参数指定选择器需要查询的通道操作。

   - 可以供选择的查询的通道操作，从类型来分，包括以下四种：

     可读：SelectionKey.OP_READ

     可写：SelectionKey.OP_WRITE

     连接：SelectionKey.OP_CONNECT

     接收：SelectionKey.OP_ACCEPT

     如果Selector对通道的多操作类型感兴趣，可以使用“位或”操作符来实现：

     int key = SelectionKey.OP_READ | SelectionKey.OP_WRITE

   - 选择器查询的不是通道的操作，而是通道的某个操作的一种就绪状态。什么是操作的就绪状态？一旦通道具备完成某个操作的条件，表示该通道的某个操作已经就绪，就可以被Selector查询到，程序可以对通道进行对应的操作。比方说，某个SocketChannel通道可以连接到一个服务器，则处于"连接就绪”(OP_CONNECT)。再比方说，一个ServerSocketChannel服务器通道准备好接收新进入的连接，则处于“接收就绪”(OP_ACCEPT)状态。还比方说，一个有数据可读的通道，可以说是“读就绪”(OP_READ)。一个等待写数据的通道可以说是“写就绪”(OP_WRITE)。

4. 选择键（SelectorKey）

   - Channel注册到后，并且一旦通道处于某种就绪的状态，就可以被选择器查询到。这个工作，使用选择器Selector的select()方法完成。select方法的作用，对感兴趣的通道操作，进行就绪状态的查询。
   - Selector可以不断的查询Channel中发生的操作的就绪状态。并且挑选感兴趣的操作就绪状态。一旦通道有操作的就绪状态达成，并且是Selector感兴趣的操作，就会被Selector选中，放入选择键集合中。
   - 一个选择键，首先是包含了注册在Selector的通道操作的类型，比方说SelectionKey.OP_READ。也包含了特定的通道与特定的选择器之间的注册关系。开发应用程序是，选择键是编程的关键。NIO的编程，就是根据对应的选择键，进行不同的业务逻辑处理。
   - 选择键的概念和事件的概念比较相似。一个选择键类似监听器模式里边的一个事件。由于Selector不是事件触发的模式，而是主动去查询的模式，所以不叫事件Event，而是叫SelectionKey选择键。

## 二，Selector的使用方法

```java
public class SelectorDemo {
    public static void main(String[] args) throws IOException {
        //创建Selector
        Selector selector = Selector.open();
        //通道
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        //设置通道为非阻塞
        serverSocketChannel.configureBlocking(false);
        //绑定连接
        serverSocketChannel.bind(new InetSocketAddress(9999));
        //将通道注册到选择器上
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        //查询已经就绪的通道操作
        Set<SelectionKey> selectionKeys = selector.selectedKeys();
        //遍历集合
        Iterator<SelectionKey> iterator = selectionKeys.iterator();
        while (iterator.hasNext()) {
            SelectionKey selectionKey = iterator.next();
            //判断key的就绪状态操作
            if (selectionKey.isAcceptable()) {

            } else if (selectionKey.isConnectable()) {

            } else if (selectionKey.isReadable()) {

            } else if (selectionKey.isWritable()) {

            }
            iterator.remove();
        }
    }
}
```

wakeup()方法：通过调用Selector对象的wakeup()方法让处在阻塞状态的select()方法立刻返回，该方法使得选择器上的第一个还没有返回的选择操作立即返回。如果当前没有进行中的选择操作，那么下一次对select()方法的一次调用将立即返回

close()方法：通过close()方法关闭Selector，该方法使得任何一个在选择操作中阻塞的线程都被唤醒（类似wakeup())，同时使得注册到该Selector的所有Channel被注销，所有的键将被取消，但是Channe本身并不会关闭。

```java
public class SelectorDemo2 {
    @Test
    public void clientDemo() throws IOException {
        //创建SocketChannel
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 9999));
        //设置非阻塞
        socketChannel.configureBlocking(false);
        //创建缓冲区
        ByteBuffer buffer = ByteBuffer.wrap("hello".getBytes(StandardCharsets.UTF_8));
        //向通道写入数据
//        buffer.flip();
        socketChannel.write(buffer);
        buffer.clear();
        socketChannel.close();
    }

    @Test
    public void serverDemo() throws IOException {
        //获取ServerSocketChannel
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        //设置非阻塞
        serverSocketChannel.configureBlocking(false);
        //创建缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        //绑定端口号
        serverSocketChannel.bind(new InetSocketAddress(9999));
        //创建选择器
        Selector selector = Selector.open();
        //将通道注册到选择器
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        //判断是否有事件
        while (selector.select() > 0) {
            //取出所有的选择键
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            //遍历
            Iterator<SelectionKey> iterator = selectionKeys.iterator();
            while (iterator.hasNext()) {
                SelectionKey key = iterator.next();
                //判断就绪状态
                if (key.isAcceptable()) {
                    //获取连接
                    SocketChannel accept = serverSocketChannel.accept();
                    //切换到非阻塞
                    accept.configureBlocking(false);
                    //注册
                    accept.register(selector, SelectionKey.OP_READ);
                } else if (key.isReadable()) {
                    SocketChannel channel = (SocketChannel) key.channel();
                    buffer.clear();
                    //读取数据
                    int readLen = channel.read(buffer);
                    if (readLen != -1) {
                        buffer.flip();
                        System.out.println(new String(buffer.array(), 0, readLen));
                        buffer.clear();
                    }
                }
                iterator.remove();
            }
        }
    }
}
```

# 第五章 Pipe和FileLock

## 一，Pipe（管道）

Java NIO管道是2个线程之间的单向数据连接。Pipe有一个source通道和一个sink通道。数据会被写到sink通道，从source通道读取。

![](F:\截图\屏幕截图 2022-07-14 150114.png)

1. 创建管道

   Pipe pipe = Pipe.open();

2. 写入管道

   向管道写数据，需要访问sink通道

   Pipe.SinkChannel sink = pipe.sink();

   调用SinkChannel的write()方法，将数据写入SinkChannel

3. 从管道读取数据

   从管道读数据，需要访问source通道

   Pipe.SourceChannel source = pipe.source();

   调用source通道的read()方法来读取数据，read()方法的返回值为读取了多少字节的数据

4. 实例

   ```java
   public class PipeDemo {
       public static void main(String[] args) throws IOException {
           //获取管道
           Pipe pipe = Pipe.open();
           //获取sink通道
           Pipe.SinkChannel sink = pipe.sink();
           //创建缓冲区
           ByteBuffer buffer = ByteBuffer.allocate(1024);
           buffer.put("hello".getBytes(StandardCharsets.UTF_8));
           buffer.flip();
           //写入数据
           sink.write(buffer);
           //获取source通道
           Pipe.SourceChannel source = pipe.source();
           //读取数据
           int readLen = 0;
           while (readLen != -1) {
               buffer.clear();
               readLen = source.read(buffer);
               buffer.flip();
               System.out.println(new String(buffer.array(), 0, readLen));
           }
       }
   }
   ```

## 二，FileLock

1. 简介

   文件锁在OS中很常见，如果多个程序同时访问、修改同一个文件，很容易因为文件数据不同步而出现问题。给文件加一个锁，同一时间，只能有一个程序修改此文件，或者程序都只能读此文件，这就解决了同步问题。

   文件锁是进程级别的，不是线程级别的。文件锁可以解决多个进程并发访问、修改同一个文件的问题，但不能解决多线程并发访问、修改同一文件的问题。使用文件锁时，同一进程内的多个线程，可以同时访问、修改此文件。

   文件锁是当前程序所属的JVM实例持有的，一旦获取到文件锁（对文件加锁），要调用release()，或者关闭对应的FileChannel对象，或者当前JVM退出，才会释放这个锁。一旦某个进程（比如说JVM实例）对某个文件加锁，则在释放这个锁之前，此进程不能再对此文件加锁，就是说JVM实例在同一文件上的文件锁是不重叠的（进程级别不能重复在同一文件上获取锁)。

2. 文件锁的分类

   - 排它锁：又叫独占锁。对文件加排它锁后，该进程可以对此文件进行读写，该进程独占此文件，其他进程不能读写此文件，直到该进程释放文件锁。
   - 共享锁：某个进程对文件加共享锁，其他进程也可以访问此文件，但这些进程都只能读此文件，不能写。线程是安全的。只要还有一个进程持有共享锁，此文件就只能读，不能写。

3. 获取文件锁的方法

   - lock()：对整个文件加锁，默认为排他锁

   - lock(long position, long size, boolean shared)：自定义加锁方式，前两个参数指定要加锁的部分（可以只对此文件的部分内容加锁），第三个参数值指定是否是共享锁

   - tryLock()：对整个文件加锁，默认为排它锁。

   - tryLock(long position, long size, boolean shared)：自定义加锁方式。如果指定为共享锁，则其它进程可读此文件，所有进程均不能写此文件，如果某进程试图对此文件进行写操作，会抛出异常。

   - lock与tryLock的区别

     lock是阻塞式的，如果未获取到文件锁，会一直阻塞当前线程，直到获取文件锁tryLock和lock的作用相同，只不过tryLock是非阻塞式的，tryLock是尝试获取文件锁，获取成功就返回锁对象，否则返回null,不会阻塞当前线程。

# 第六章 其他

## 一，Path

1. 创建绝对路径

   Path path = Paths.get("F:\\\111.jpg");

2. 创建相对路径

   Path path = Paths.get("F:", "111.jpg");

3. Path.normalize()

   Path接口的normalize()方法可以使路径标准化。标准化意味着它将移除所有在路径字符串的中间的.和..代码，并解析路径字符串所引用的路径。

## 二，Files

Java NIO Files类(ava.nio.file.Files))提供了几种操作文件系统中的文件的方法。。java.nio.file.Files类与java.nio.fle.Path实例一起工作，因此在学习Files类之前，需要先了解Path类。

1. Files.createDirectory()

   Files.createDirectory()方法，用于根据Path实例创建一个新目录，如果该目录已经存在，则是抛出一个java.nio.file.FileAlreadyExistsException。如果出现其他错误，可能会抛出IOException。例如，如果想要的新目录的父目录不存在，则可能会抛出IOException。

2. Files.copy()

   Files.copy()方法从一个路径拷贝一个文件到另外一个目录，如果目标文件已经存在，则抛出一个java.nio.file.FileAlreadyExistsException异常。如果有其他错误，则会抛出一个IOException。例如，如果将该文件复制到不存在的目录，则会抛出IOException。Files.copy()方法的第三个参数。如果目标文件已经存在，这个参数指示copy()方法覆盖现有的文件。

3. Files.move()

   Files.move()用于将文件从一个路径移动到另一个路径。移动文件与重命名相同，但是移动文件既可以移动到不同的目录，也可以在相同的操作中更改它的名称。

4. Files.delete()

   Files.delete()方法可以删除一个文件或者目录。

5. Files.walkFileTree()

   - Files.walkFileTree()方法包含递归遍历目录树功能，将Path实例和FileVisitor作为参数。Path实例指向要遍历的目录，FileVisitor在遍历期间被调用。

   - FileVisitor是一个接口，必须自己实现FileVisitor接口，并将实现的实例传递给walkFileTree()方法。在目录遍历过程中，您的FileVisitor实现的每个方法都将被调用。如果不需要实现所有这些方法，那么可以扩展SimpleFileVisitor类，它包含FileVisitor接口中所有方法的默认实现。

   - FileVisitor接口的方法中，每个都返回一个FileVisitResult枚举实例。FileVisitResult枚举包含以下四个选项：

     CONTINUE：继续

     TERMINATE：终止

     SKIP_SIBLING：跳过同级

     SKIP_SUBTREE：跳过子级

## 三，AsynchronousFileChannel

1. 创建

   AsynchronousFileChannel.open(Path file, OpenOption... options);

2. 通过Future读取数据

   ```java
   @Test
   public void readAsyncFileChannel() throws IOException, InterruptedException {
       //创建AsynchronousFileChannel
       Path path = Paths.get("F:\\111.txt");
       AsynchronousFileChannel channel = AsynchronousFileChannel.open(path, StandardOpenOption.READ);
       //创建Buffer
       ByteBuffer buffer = ByteBuffer.allocate(1024);
       //读取
       Future<Integer> future = channel.read(buffer, 0);
       //判断是否完成
       while (!future.isDone()) {
           TimeUnit.SECONDS.sleep(1);
       }
       //输出数据
       buffer.flip();
       byte[] data = new byte[buffer.limit()];
       buffer.get(data);
       System.out.println(new String(data));
       buffer.clear();
   }
   ```

3. 使用CompletionHandler读取数据

   ```java
   @Test
   public void readAsyncFileChannel() throws IOException, InterruptedException {
       //创建AsynchronousFileChannel
       Path path = Paths.get("F:\\111.txt");
       AsynchronousFileChannel channel = AsynchronousFileChannel.open(path, StandardOpenOption.READ);
       //创建Buffer
       ByteBuffer buffer = ByteBuffer.allocate(1024);
       //读取
       channel.read(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
           @Override
           public void completed(Integer result, ByteBuffer attachment) {
               attachment.flip();
               byte[] data = new byte[attachment.limit()];
               attachment.get(data);
               System.out.println(new String(data));
               attachment.clear();
           }
   
           @Override
           public void failed(Throwable exc, ByteBuffer attachment) {
   
           }
       });
   }
   ```

4. 通过Future写数据

   ```java
   @Test
   public void writeAsyncFileChannel() throws IOException {
       //创建AsynchronousFileChannel
       Path path = Paths.get("F:\\111.txt");
       AsynchronousFileChannel channel = AsynchronousFileChannel.open(path, StandardOpenOption.WRITE);
       //创建Buffer
       ByteBuffer buffer = ByteBuffer.wrap("hello".getBytes(StandardCharsets.UTF_8));
       //写数据
       Future<Integer> future = channel.write(buffer, 0);
       //判断是否完成
       if (!future.isDone());
       buffer.clear();
       System.out.println("完成");
   }
   ```

5. 通过CompletionHandler写数据

   ```java
   @Test
   public void writeAsyncFileChannel() throws IOException {
       //创建AsynchronousFileChannel
       Path path = Paths.get("F:\\111.txt");
       AsynchronousFileChannel channel = AsynchronousFileChannel.open(path, StandardOpenOption.WRITE);
       //创建Buffer
       ByteBuffer buffer = ByteBuffer.wrap("吴功勋".getBytes(StandardCharsets.UTF_8));
       //写数据
       channel.write(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
           @Override
           public void completed(Integer result, ByteBuffer attachment) {
               attachment.clear();
               System.out.println("完成");
           }
   
           @Override
           public void failed(Throwable exc, ByteBuffer attachment) {
   
           }
       });
   }
   ```

## 四，Charset

1. Charset常用静态方法

   forName(String charsetName)：通过编码类型获得Charset对象

   availableCharset()：获取系统支持的所有编码方式

   defaultCharset()：获得虚拟机默认的编码方式

   isSupported(String charsetName)：判断是否支持该编码类型

2. Charset的常用普通方法

   name()：获取Charset对象的编码类型

   newEncoder()：获取编码器对象

   newDecoder()：获取解码器对象

3. 实例

   ```java
   public class CharsetDemo {
       public static void main(String[] args) throws CharacterCodingException {
           //获取Charset对象
           Charset charset = Charset.forName("UTF-8");
           //获取编码器对象
           CharsetEncoder charsetEncoder = charset.newEncoder();
           //创建缓冲区
           CharBuffer buffer = CharBuffer.wrap("hello");
           //编码
           ByteBuffer encode = charsetEncoder.encode(buffer);
           for (int i = 0; i < encode.limit(); i++) {
               System.out.println(encode.get());
           }
           //获取解码器对象
           CharsetDecoder charsetDecoder = charset.newDecoder();
           //解码
           encode.flip();
           CharBuffer decode = charsetDecoder.decode(encode);
           System.out.println(decode);
       }
   }
   ```



