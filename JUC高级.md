# 第一章 CompletableFuture

## 一，Future接口理论知识

1. Future接口(FutureTask实现类)定义了操作异步任务执行一些方法，如获取异步任务的执行结果、取消任务的执行、判断任务是否被取消、判断任务执行是否完毕等。

2. Future的优点

   future+线程池异步多线程任务配合，能显著提高程序的执行效率

   ```java
   //3个任务，目前只有一个main来处理，耗时多少
   public static void main(String[] args) throws ExecutionException, InterruptedException {
       //创建线程池
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       long start = System.currentTimeMillis();
   
       //添加三个任务
       FutureTask<String> futureTask1 = new FutureTask<String>(() -> {
           TimeUnit.MILLISECONDS.sleep(500);
           return "task1 over";
       });
       threadPool.submit(futureTask1);
       FutureTask<String> futureTask2 = new FutureTask<String>(() -> {
           TimeUnit.MILLISECONDS.sleep(300);
           return "task1 over";
       });
       threadPool.submit(futureTask2);
       FutureTask<String> futureTask3 = new FutureTask<String>(() -> {
           TimeUnit.MILLISECONDS.sleep(200);
           return "task1 over";
       });
       threadPool.submit(futureTask3);
   
       System.out.println(futureTask1.get());
       System.out.println(futureTask2.get());
       System.out.println(futureTask3.get());
   
       long end = System.currentTimeMillis();
   
       threadPool.shutdown();
   
       System.out.println(end - start);
   }
   ```

3. Future的缺点

   - get()阻塞

     调用get()方法获取结果，如果没有计算完成容易导致程序阻塞

   - isDone()轮询

     轮询的方式会耗费无谓的CPU资源，而且也不见得能及时地得到计算结果

     如果想要异步获取结果，通常都会以轮询的方式去获取结果，尽量不要阻塞

## 二，CompletableFuture

1. CompletionStage

   - CompletionStage代表异步计算过程中的某一个阶段，一个阶段完成以后可能会触发另外一个阶段

   - 一个阶段的计算执行可以是一个Function，Consumer或者Runnable。比如：

     ```java
     stage.thenApply(x -> square(x)).thenAccept(x -> System.out.print(x)).thenRun(() -> System.out.println()
     ```

   - 一个阶段的执行可能是被单个阶段的完成触发，也可能是由多个阶段一起触发

2. CompletableFuture

   - 在Java8中，CompletableFuture提供了非常强大的Future的扩展功能，可以帮助我们简化异步编程的复杂性，并且提供了函数式编程的能力，可以通过回调的方式处理计算结果，也提供了转换和组合CompletableFuture的方法。
   - 它可能代表一个明确完成的Future,也有可能代表一个完成阶段(CompletionStage)，它支持在计算完成以后触发一些函数或执行某些动作。
   - 它实现了Future和CompletionStage接口

3. 核心的四个静态方法

   ```java
       public static void main(String[] args) throws ExecutionException, InterruptedException {
           //创建线程池
           ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
           CompletableFuture<Void> completableFuture1 = CompletableFuture.runAsync(() -> {
               System.out.println(Thread.currentThread().getName());//pool-1-thread-1
           }, threadPool);
   
           System.out.println(completableFuture1.get());//null
   
           CompletableFuture<String> completableFuture2 = CompletableFuture.supplyAsync(() -> {
               System.out.println(Thread.currentThread().getName());//pool-1-thread-2
               return "hello";
           }, threadPool);
   
           System.out.println(completableFuture2.get());//hello
   
           threadPool.shutdown();
       }
   ```

4. 减少阻塞和轮询

   ```java
   public static void main(String[] args) {
       //创建线程池
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture.supplyAsync(() -> {
           System.out.println(Thread.currentThread().getName() + "正在执行任务");
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           //返回一个10以内的随机数
           return new Random().nextInt(10);
       }, threadPool).whenComplete((result, e) -> {
           //执行完成之后
           if (e == null) {
               System.out.println("执行完成,结果为:" + result);
           }
       }).exceptionally((e) -> {
           //出现异常,直接打印异常信息
           e.printStackTrace();
           return null;
       });
   
       threadPool.shutdown();
   
       System.out.println("main线程结束");
   }
   ```

5. 链式语法

   使用@Accessors(chain = true)注解开启链式语法

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   @Accessors(chain = true)
   class Student {
       private Integer id;
       private String name;
   }
   //Student student = new Student().setId(1).setName("wgx");
   ```

## 三，案例实战

1. 不使用CompletableFuture

   ```java
   //从不同的电商网站中获取同一件商品的价格（不使用CompletableFuture）
   public class CompletableFutureMallDemo {
       static List<NetMall> netMallList = Arrays.asList(
               new NetMall("jd"),
               new NetMall("dangdang"),
               new NetMall("taobao")
       );
   
       public static List<String> getPrice(String productName) {
           return netMallList.stream().map((netMall) -> String.format(productName + " in %s price is  %.2f", netMall.getNetMallName(), netMall.calcPrice(productName))).collect(Collectors.toList());
       }
   
       public static void main(String[] args) {
           long start = System.currentTimeMillis();
           List<String> list = getPrice("MySql");
           list.stream().forEach(System.out :: println);
           long end = System.currentTimeMillis();
           System.out.println(end - start);//3112
       }
   }
   
   //模拟电商平台返回价格
   @Data
   class NetMall {
       private String netMallName;
   
       public NetMall(String netMallName) {
           this.netMallName = netMallName;
       }
   
       public double calcPrice(String productName) {
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return ThreadLocalRandom.current().nextDouble() * 2 + productName.charAt(0);
       }
   }
   ```

2. 使用CompletableFuture

   ```java
   //从不同的电商网站中获取同一件商品的价格
   public class CompletableFutureMallDemo {
       static List<NetMall> netMallList = Arrays.asList(
               new NetMall("jd"),
               new NetMall("dangdang"),
               new NetMall("taobao")
       );
   
       public static List<String> getPrice(String productName) {
           return netMallList.stream()
                   .map((netMall) -> CompletableFuture.supplyAsync(() -> String.format(productName + " in %s price is  %.2f", netMall.getNetMallName(), netMall.calcPrice(productName))))
                   .collect(Collectors.toList())
                   .stream()
                   .map((completableFuture) -> completableFuture.join())
                   .collect(Collectors.toList());
   
       }
   
       public static void main(String[] args) {
           long start = System.currentTimeMillis();
           List<String> list = getPrice("MySql");
           list.stream().forEach(System.out :: println);
           long end = System.currentTimeMillis();
           System.out.println(end - start);//1078
       }
   }
   
   //模拟电商平台返回价格
   @Data
   class NetMall {
       private String netMallName;
   
       public NetMall(String netMallName) {
           this.netMallName = netMallName;
       }
   
       public double calcPrice(String productName) {
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return ThreadLocalRandom.current().nextDouble() * 2 + productName.charAt(0);
       }
   }
   ```

## 四，CompletableFuture常用方法

1. public T getNow(T valueIfAbsent)

   没有计算完成返回valueIfAbsent，计算完成返回计算结果

2. public boolean complete(T value)

   计算完成返回false，后续的获取结果方法正常；如果没有计算完成返回true，后续的获取结果方法为value

3. thenApply（拿到返回结果，进行处理）

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return 1;
       }, threadPool).thenApply(result -> {
           //计算完成后，拿到结果，执行
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return result++;
       }).whenComplete((result, e) -> {
           if (e == null) {
               System.out.println("计算完成,结果为:" + result);
           }
       }).exceptionally(e -> {
           //出现异常，返回0
           e.printStackTrace();
           return 0;
       });
       
       threadPool.shutdown();
   }
   ```

4. handle（出现异常依旧会执行，可以自行对异常e处理）

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           //出现异常
           int i = 1 / 0;
           return 1;
       }, threadPool).handle((result, e) -> {
           //计算完成后，拿到结果和异常，可以自行对异常进行处理
           if (e == null) {
               return result++;
           } else {
               //e不为null说明出现过异常，返回0
               e.printStackTrace();
               return 0;
           }
       }).whenComplete((result, e) -> {
           if (e == null) {
               System.out.println("计算完成,结果为:" + result);
           }
       }).exceptionally(e -> {
           //出现异常，返回0
           e.printStackTrace();
           return 0;
       });
   
       threadPool.shutdown();
   }
   ```

5. thenAccept（对结果进行消费处理，无返回值）

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture completableFuture = CompletableFuture.supplyAsync(() -> {
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return 1;
       }, threadPool).thenAccept(System.out :: println);
   
       threadPool.shutdown();
   }
   ```

6. thenRun（计算完成之后执行，不需要返回结果）

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture completableFuture = CompletableFuture.supplyAsync(() -> {
           try {
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return 1;
       }, threadPool).thenRun(() -> System.out.println("计算完成"));
   
       threadPool.shutdown();
   }
   ```

7. CompletableFuture和线程池说明

   - 没有传入自定义线程池，都用默认线程池ForkJoinPool

   - 如果你执行第一个任务的时候，传入了一个自定义线程池，调用thenRun方法执行第二个任务时，则第二个任务和第一个任务是共用同一个线程池。

     调用thenRunAsync执行第二个任务时，则第一个任务使用的是你自己传入的线程池，第二个任务使用的是ForkJoin线程池

   - 有可能处理太快，系统优化切换原则，直接使用main线程处理，其它如：thenAccept和thenAcceptAsync，thenApply和thenApplyAsync等，它们之间的区别也是同理

8. 对计算速度进行选用

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture<String> playerA = CompletableFuture.supplyAsync(() -> {
           try {
               System.out.println("A is come in");
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return "player A";
       }, threadPool);
   
       CompletableFuture<String> playerB = CompletableFuture.supplyAsync(() -> {
           try {
               System.out.println("B is come in");
               TimeUnit.SECONDS.sleep(2);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return "player B";
       }, threadPool);
   
       //比较A和B的计算速度，谁先完成f是谁的结果
       CompletableFuture<String> result = playerA.applyToEither(playerB, (f) -> f + " is winner");
   
       System.out.println(result.join());
   
       threadPool.shutdown();
   }
   ```

9. 对计算结果的合并

   ```java
   public static void main(String[] args) {
       ExecutorService threadPool = Executors.newFixedThreadPool(3);
   
       CompletableFuture<Integer> completableFuture1 = CompletableFuture.supplyAsync(() -> {
           try {
               System.out.println(Thread.currentThread().getName() + "开始执行任务");
               TimeUnit.SECONDS.sleep(1);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return 1;
       }, threadPool);
   
       CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
           try {
               System.out.println(Thread.currentThread().getName() + "开始执行任务");
               TimeUnit.SECONDS.sleep(2);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           return 2;
       }, threadPool);
   
       //合并结果
       CompletableFuture<Integer> result = completableFuture1.thenCombine(completableFuture2, (a, b) -> a + b);
   
       System.out.println("合并结果为:" + result.join());
   
       threadPool.shutdown();
   }
   ```

# 第二章 多线程锁

## 一，synchronized

1. synchronized同步代码块

   实现使用的是monitorenter和monitorexit指令，一般是一个enter两个exit

2. synchronized普通同步方法

   调用指令将会检查方法的ACC_SYNCHRONIZED访问标志是否被设置。如果设置了，执行线程会将先持有monitor锁，然后再执行方法，最后在方法完成（无论是正常完成还是非正常完成）时释放monitor

3. synchronized静态同步方法

   ACC_STATIC，ACC_SYNCHRONIZED访问标志区分该方法是否静态同步方法

**什么是管程monitor**

管程（Monitors，也称为监视器）是一种程序结构，结构内的多个子程序（对象或模块）形成的多个工作线程互斥访问共享资源。这些共享资源一般是硬件设备或一群变量。对共享变量能够进行的所有操作集中在一个模块中。（把信号量及其操作原语“封装”在一个对象内部）管程实现了在一个时间点，最多只有一个线程在执行管程的某个子程序。管程提供了一种机制，管程可以看做一个软件模块，它是将共享的变量和对于这些共享变量的操作封装起来，形成一个具有一定接口的功能模块，进程可以调用管程来实现进程级别的并发控制。

## 二，公平锁和非公平锁

1. 公平锁

   是指多个线程按照申请锁的顺序来获取锁，这里类似排队买票，先来的人先买后来的人在队尾排着，这是公平的

   Lock lock = new ReentrantLock(true);//true表示公平锁，先来先得

2. 非公平锁

   是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁，在高并发环境下，有可能造成优先级翻转或者饥饿的状态（某个线程一直得不到锁）

   Lock lock = new ReentrantLock(false);//false表示非公平锁，后来的也可能先获得锁

   Lock lock=new ReentrantLock();//默认非公平锁

## 三，可重入锁（递归锁）

是指在同一个线程在外层方法获取锁的时候，再进入该线程的内层方法会自动获取锁（前提，锁对象得是同一个对象），不会因为之前己经获取过还没释放而阻塞。

如果是1个有synchronized修饰的递归调用方法，程序第2次进入被自己阻塞了岂不是天大的笑话，出现了作茧自缚。所以Java中ReentrantLock和synchronized都是可重入锁，可重入锁的一个优点是可一定程度避免死锁。

## 四，死锁与排查

1. 死锁的概念

   死锁是指两个或两个以上的线程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力干涉那它们都将无法推进下去，如果系统资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。

2. 产生死锁的主要原因

   - 系统资源不足
   - 进程运行推进的顺序不合适
   - 资源分配不当

3. 如何排查死锁

   - 使用jps -l查看进程编号

     jstack 进程编号

   - jconsole

# 第三章 LockSupport与线程中断

## 一，中断机制

一个线程不应该由其他线程来强制中断或停止而是应该由线程自己自行停止。所以，Thread.stop,Thread.suspend,Thread.resume都已经被废弃了。

1. 什么是中断机制

   在Java中没有办法立即停止一条线程，然而停止线程却显得尤为重要，如取消一个耗时操作。因此，Java提供了一种用于停止线程的协商机制一一中断，也即中断标识协商机制。

   中断只是一种协作协商机制，Java没有给中断增加任何语法，中断的过程完全需要程序员自己实现。若要中断一个线程，你需要手动调用该线程的interrupt方法，该方法也仅仅是将线程对象的中断标识设成true，接着你需要自己写代码不断地检测当前线程的标识位，如果为true，表示别的线程请求这条线程中断，此时究竞该做什么需要你自己写代码实现。

   每个线程对象中都有一个中断标识位，用于表示线程是否被中断；该标识位为true表示中断，为false表示未中断；通过调用线程对象的interrupt方法将该线程的标识位设为true可以在别的线程中调用，也可以在自己的线程中调用。

2. 中断相关的API说明

   - public void interrupt()

     仅仅是设置线程的中断状态为true，发起一个协商而不会立刻停止线程

     - 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true，仅此而已。被设置中断标志的线程将继续正常运行，不受影响。所以，interrupt()并不能真正的中断线程，需要被调用的线程自己进行配合才行。
     - 如果线程处于被阻塞状态（例如处于sleep,wait,join等状态），在别的线程中调用当前线程对象的interrupt方法，那么线程将立即退出被阻塞状态，抛出一个InterruptedException异常，==并且中断状态将被清除==。

   - public static boolean interrupted()

     判断线程是否被中断并清除当前中断状态，该方法做了两件事

     - 返回当前线程的中断状态，测试当前线程是否已被中断
     - 将当前线程的中断状态清零并重新设置为false，清除线程的中断状态

   - public boolean isInterrupted()

     判断当前线程是否被中断（通过检查中断标志位）

3. 如何停止中断运行中的线程

   - 通过一个volatile变量实现

     ```java
     static volatile boolean isStop = false;
     
     public static void main(String[] args) throws InterruptedException {
         new Thread(() -> {
             while (true) {
                 if (isStop) {
                     System.out.println("程序停止");
                     break;
                 }
                 System.out.println("程序正在运行");
             }
         }, "t1").start();
     
         TimeUnit.MILLISECONDS.sleep(10);
     
         new Thread(() -> {
             System.out.println("停止程序");
             isStop = true;
         }, "t2").start();
     }
     ```

   - 通过AtomicBoolean

     ```java
     static AtomicBoolean isStop = new AtomicBoolean(false);
     
     public static void main(String[] args) throws InterruptedException {
         new Thread(() -> {
             while (true) {
                 if (isStop.get()) {
                     System.out.println("程序停止");
                     break;
                 }
                 System.out.println("程序正在运行");
             }
         }, "t1").start();
     
         TimeUnit.MILLISECONDS.sleep(10);
     
         new Thread(() -> {
             System.out.println("停止程序");
             isStop.set(true);
         }, "t2").start();
     }
     ```

   - 通过Thread类自带的中断api方法实现

     ```java
     public static void main(String[] args) throws InterruptedException {
         Thread t1 = new Thread(() -> {
             while (true) {
                 if (Thread.currentThread().isInterrupted()) {
                     System.out.println("程序停止");
                     break;
                 }
                 System.out.println("程序正在运行");
             }
         }, "t1");
     
         t1.start();
     
         TimeUnit.MILLISECONDS.sleep(10);
     
         new Thread(() -> {
             System.out.println("停止程序");
             t1.interrupt();
         }, "t2").start();
     }
     ```

## 二，线程唤醒机制

1. 三种让线程等待和唤醒的方法

   - 使用Object中的wait()方法让线程等待，使用Object中的notify()方法唤醒线程
   - 使用JUC包中的Condition的await()方法让线程等待，使用signal()方法唤醒线程
   - LockSupport类可以阻塞当前线程以及唤醒指定被阻塞的线程

2. Object类中的wait和notify实现线程等待和唤醒

   wait和notify方法必须要在同步块或者方法里面，且成对出现使用，先wait后notify才行

   ```java
   public static void main(String[] args) {
       Object object = new Object();
   
       new Thread(() -> {
           synchronized (object) {
               try {
                   System.out.println(Thread.currentThread().getName() + " come in...");
                   object.wait();
                   System.out.println(Thread.currentThread().getName() + "被唤醒");
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       }, "t1").start();
   
       new Thread(() -> {
           synchronized (object) {
               System.out.println(Thread.currentThread().getName() + " come in...");
               object.notify();
           }
       }, "t2").start();
   }
   ```

3. Condition接口中的await和signal实现线程等待和唤醒

   Condition中的线程等待和唤醒方法，需要先获取锁，一定要先await后signal

   ```java
   public static void main(String[] args) {
       ReentrantLock lock = new ReentrantLock();
   
       Condition condition = lock.newCondition();
       
       new Thread(() -> {
           try {
               lock.lock();
               System.out.println(Thread.currentThread().getName() + " come in...");
               condition.await();
               System.out.println(Thread.currentThread().getName() + "被唤醒");
               lock.unlock();
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
       }, "t1").start();
   
       new Thread(() -> {
           lock.lock();
           System.out.println(Thread.currentThread().getName() + " come in...");
           condition.signal();
           lock.unlock();
       }, "t2").start();
   }
   ```

## 三，LockSupport

1. LockSupport是什么

   LockSupport是用来创建锁和其他同步类的基本线程阻塞原语。

   LockSupport类使用了一种名为Permit（许可）的概念来做到阻塞和唤醒线程的功能，每一个线程都有一个许可，但与Semaphore不同的是，许可的累加上限是1

2. LockSupport的主要方法

   - 阻塞（park()/park(Object blocker)）

     阻塞当前线程/阻塞传入的具体线程

   - 唤醒（unpark(Thread thread)）

     唤醒处于阻塞状态的指定线程

3. ```java
   public static void main(String[] args) {
       Thread t1 = new Thread(() -> {
           System.out.println(Thread.currentThread().getName() + " come in...");
           LockSupport.park();
           System.out.println(Thread.currentThread().getName() + "被唤醒");
       }, "t1");
   
       t1.start();
   
       new Thread(() -> {
           System.out.println(Thread.currentThread().getName() + " come in...");
           LockSupport.unpark(t1);
       }, "t2").start();
   }
   ```

# 第四章 JMM

## 一，Java内存模型（Java Memory Model）

JMM(Java内存模型Java Memory Model,简称JMM)本身是一种抽象概念并不真实存在它仅仅描述的是一组约定或规范，通过这组规范定义了程序中（尤其是多线程）各个变量的读写访问方式并决定一个线程对共享变量的写入何时以及如何变成对另一个线程可见，关键技术点都是围绕多线程的原子性、可见性和有序性展开的。

## 二，JMM的三大特性

1. 可见性

   指当一个线程修改了某一个共享变量的值，其他线程是否能够立即知道该变更，JMM规定了所有的变量都存储在主内存中。

   系统主内存共享变量数据修改被写入的时机是不确定的，多线程并发下很可能出现"脏读"，所以每个线程都有自己的工作内存，线程自己的工作内存中保存了该线程使用到的变量的主内存副本拷贝，线程对变量的所有操作（读取，赋值等）都必需在线程自己的工作内存中进行，而不能够直接读写主内存中的变量。不同线程之间也无法直接访问对方工作内存中的变量，线程间变量值的传递均需要通过主内存来完成

2. 原子性

   指一个操作是不可打断的，即多线程环境下，操作不能别其他线程干扰

3. 有序性

   对于一个线程的执行代码而言，我们总是习惯性认为代码的执行总是从上到下，有序执行。但为了提升性能，编译器和处理器通常会对指令序列进行重新排序。Java规范规定JVM线程内部维持顺序化语义，即只要程序的最终结果与它顺序化执行的结果相等，那么指令的执行顺序可以与代码顺序不致，此过程叫指令的重排序。

## 三，多线程对变量的读写过程

由于JVM运行程序的实体是线程，而每个线程创建时JVM都会为其创建一个工作内存（有些地方称为栈空间），工作内存是每个线程的私有数据区域，而Java内存模型中规定所有变量都存储在主内存，主内存是共享内存区域，所有线程都可以访问，但线程对变量的操作(读取赋值等)必须在工作内存中进行，首先要将变量从主内存拷贝到的线程自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存，不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间的通信（传值）必须通过主内存来完成。

## 四，多线程先行发生原则（happens-before）

在JMM中，如果一个操作执行的结界需要对另一个操作可见性或者代码重排序，那么这两个操作之间必须存在happens-before(先行发生)原则。逻辑上的先后关系

1. happens-before总原则

   如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。

   两个操作之间存在happens-before关系，并不意味着一定要按照happens-before原则制定的顺序来执行。如果重排序之后的执行结果与按照happens-before关系来执行的结果一致，那么这种重排序并不非法。

2. happens-before8条原则

   - 次序规则

     一个线程内，按照代码顺序，写在前面的操作先行发生于写在后面的操作

   - 锁定规则

     一个unLock操作先行发生于后面（这里的“后面”是指时间上的先后）对同一个锁的lock操作

   - volatile变量规则

     对一个volatile变量的写操作先行发生于后面对这个变量的读操作，前面的写对后面的读是可见的，这里的“后面”是指时间上的先后

   - 传递规则

     如果操作A先行发生于操作B，而操作B有先行发生于操作C，则可以得出操作A先行发生于操作C

   - 线程启动规则（Tread Start Rule）

     Thread对象的start()方法先行发生于线程的每一个动作

   - 线程中断规则（Thread Interruption Rule）

     对线程interrupt()方法的调用先行发生于被中断的线程的代码检测到中断事件的发生。

     可以通过Thread.interrupted()检测到是否发生中断，也就是说要先调用interrupted()方法设置过中断标志位，才能检测到中断发送

   - 线程终止规则（Thread Termination Rule）

     线程中的所有操作都先行发生于对此线程的终止检测，可以通过isAlive()等手段检测线程是否已经终止执行

   - 对象终结规则（Finalizer Rule）
   
     一个对象的初始化完成（构造函数执行结束）先行发生于它的finalize()方法的开始

# 第五章 volatile

## 一，volatile两大特性

1. 两大特性
   - 可见性
   - 有序性
2. volatile内存语义
   - 当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值立即刷新会主内存中
   - 当读一个volatile变量时，JMM会把该线程对应的本地内存设置为无效，重新回到主内存中读取最新共享变量
   - volatile的写内存语义是直接刷新到主内存中，读的内容语义是直接从主内存中读取

## 二，内存屏障

1. 什么是内存屏障

   内存屏障（也称内存栅栏，屏障指令等，是一类同步屏障指令，是CPU或编译器在对内存随机访问的操作中的一个同步点，使得此点之前的所有读写操作都执行后才可以开始执行此点之后的操作)，避免代码重排序。内存屏障其实就是一种JVM指令，Java内存模型的重排规则会要求Java编译器在生成JVM指令时插入特定的内存屏障指令，通过这些内存屏障指令，volatile实现了Java内存模型中的可见性和有序性（禁重排），但volatile无法保证原子性。

   内存屏障之前的所有写操作都要回写到主内存

   内存屏障之后的所以读操作都能获得内存屏障之前的所有写操作的最新结果（实现了可见性）

   - 写屏障（Store Memory Barrier）：告诉处理器在写屏障之前将所有存储在缓存（store buffers）中的数据同步到主内存。也就是说当看到Store屏障指令，就必须把该指令之前所有写入指令执行完毕才能继续往下执行。
   - 读屏障（Load Memory Barrier）：处理器在读屏障之后的读操作，都在读屏障之后执行。也就是说在Load屏障指令之后就能够保证后面的读取指令一定能够读取到最新的数据。

2. 屏障分类

   ![](F:\截图\屏幕截图 2022-08-16 103710.png)

3. happens-before之变量规则

   ![](F:\截图\屏幕截图 2022-08-16 110226.png)

4. 内存屏障插入策略

   - 读屏障
     - 在每个volatile读操作的后面插入一个LoadLoad屏障，禁止处理器把上面的volatile读与下面的普通读重排序
     - 在每个volatile读操作的后面插入一个LoadStore屏障，禁止处理器把上面的volatile读与下面的普通写重排序
     - ![](F:\截图\屏幕截图 2022-08-16 110857.png)
   - 写屏障
     - 在每个volatile写操作的前面插入一个StoreStore屏障，可以保证在volatile写之前，其前面的所有普通写操作都已经刷新到主内存中
     - 在每个volatile写操作的后面插入一个StoreLoad屏障，作用是避免volatile写与后面可能有的volatile读/写操作重排序
     - ![](F:\截图\屏幕截图 2022-08-16 111915.png)

## 三，volatile特性

1. 保证可见性

   read(读取)->load(加载)->use(使用)->assign(赋值)->store(存储)->write(写入)->lock(锁定)->unlock(解锁)

   - read:作用于主内存，将变量的值从主内存传输到工作内存，主内存到工作内存

   - load:作用于工作内存，将read从主内存传输的变量值放入工作内存变量副本中，即数据加载

   - use:作用于工作内存，将工作内存变量副本的值传递给执行引擎，每当JVM遇到需要该变量的字节码指令时会执行该操作

   - assign:作用于工作内存，将从执行引擎接收到的值赋值给工作内存变量，每当JVM遇到一个给变量赋值字节码指令时会执行该操作

   - store:作用于工作内存，将赋值完毕的工作变量的值写回给主内存

   - write:作用于主内存，将store传输过来的变量值赋值给主内存中的变量

     > 由于上述6条只能保证单条指令的原子性，针对多条指令的组合性原子保证，没有大面积加锁，所以，JVM提供了另外两个原子指令

   - lock:作用于主内存，将一个变量标记为一个线程独占的状态，只是写时候加锁，就只是锁了写变量的过程。

   - unlock:作用于主内存，把一个处于锁定状态的变量释放，然后才能被其他线程占用

2. 没有原子性

   对于volatile变量具备可见性，JVM只是保证从主内存加载到线程工作内存的值是最新的，也仅是数据加载时是最新的。但是多线程环境下，"数据计算"和"数据赋值"操作可能多次出现，若数据在加载之后，若主内存volatile修饰变量发生修改之后，线程工作内存中的操作将会作废去读主内存最新值，操作出现写丢失问题。即各线程私有内存和主内存公共内存中变量不同步，进而导致数据不一致。由此可见volatile解决的是变量读时的可见性问题，但无法保证原子性，对于多线程修改主内存共享变量的场景必须使用加锁同步。

## 四，volatile使用场景

1. 单一赋值，不包含复合运算赋值（i++之类）

   volatile int a = 10;

   volatile boolean flag = false;

2. 状态标志，判断业务是否结束

3. 开销较低的读，写锁策略

4. DCL双端锁的发布

# 第六章 CAS

## 一，CAS原理简介

1. 什么是CAS

   compare and swap的缩写，中文翻译成比较并交换，实现并发算法时常用到的一种技术。

   它包含三个操作数一一内存位置、预期原值及更新值。

   执行CAS操作的时候，将内存位置的值与预期原值比较：如果相匹配，那么处理器会自动将该位置值更新为新值，如果不匹配，处理器不做任何操作，多个线程同时执行CAS操作只有一个会成功。

   **CAS有3个操作数，位置内存值V,旧的预期值A,要修改的更新值B。当且仅当旧的预期值A和内存值V相同时，将内存值V修改为B,否则什么都不做或重来当它重来重试的这种行为成为--自旋！！**

## 二，Unsafe类

1. 基本概念

   是CAS的核心类，由于Java方法无法直接访问底层系统，需要通过本地(native)方法来访问，Unsafe相当于一个后门，基于该类可以直接操作特定内存的数据。Unsafe类存在于sun.misc包中，其内部方法操作可以像C的指针一样直接操作内存，因为Java中CAS操作的执行依赖于Unsafe类的方法。

   **注意：Unsafe类中的所有方法都是native修饰的，也就是说Unsafe类中的方法都直接调用操作系统底层资源热行相应任务**

2. 变量valueOffset，表示该变量值在内存中的偏移地址，因为Unsafe就是根据内存偏移地址获取数据的

3. AtomicReference

   ```java
   public class AtomicReferenceDemo {
       public static void main(String[] args) {
           AtomicReference<Person> atomicReference = new AtomicReference<>();
           Person person1 = new Person("person1", 22);
           Person person2 = new Person("person2", 22);
   
           atomicReference.set(person1);
   
           System.out.println(atomicReference.compareAndSet(person1, person2) + "\t" + atomicReference.get());
       }
   }
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   class Person {
       private String name;
       private int age;
   }
   ```

4. 手写自旋锁

   ```java
   public class SpinLockDemo {
       AtomicReference<Thread> atomicReference = new AtomicReference<>();
   
       public void lock() {
           Thread thread = Thread.currentThread();
           //如果atomicReference为null，说明没有线程持有锁
           while (!atomicReference.compareAndSet(null, thread)) {}
       }
   
       public void unLock() {
           Thread thread = Thread.currentThread();
           //解锁，将atomicReference赋值为null
           atomicReference.compareAndSet(thread, null);
       }
   
       public static void main(String[] args) {
           SpinLockDemo spinLock = new SpinLockDemo();
           new Thread(() -> {
               spinLock.lock();
               System.out.println(Thread.currentThread().getName() + " come in...");
               try {
                   TimeUnit.SECONDS.sleep(3);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               } finally {
                   System.out.println(Thread.currentThread().getName() + " over");
                   spinLock.unLock();
               }
           }, "t1").start();
   
           new Thread(() -> {
               spinLock.lock();
               System.out.println(Thread.currentThread().getName() + " come in...");
               System.out.println(Thread.currentThread().getName() + " over");
               spinLock.unLock();
           }, "t2").start();
       }
   }
   ```

5. CAS的缺点

   - 循环时间长开销很大

   - ABA问题

     CAS算法实现一个重要前提需要取出内存中某时刻的数据并在当下时刻比较并替换，那么在这个时间差类会导致数据的变化。

     比如说一个线程1从内存位置V中取出A，这时候另一个线程2也从内存中取出A，并且线程2进行了一些操作将值变成了B，然后线程2又将V位置的数据变成A，这时候线程1进行CAS操作发现内存中仍然是A，预期OK，然后线程1操作成功。**尽管线程1的CAS操作成功，但是不代表这个过程就是没有问题的。**

6. 解决ABA问题

   ```java
   public class ABADemo {
       static AtomicStampedReference<String> stampedReference = new AtomicStampedReference<>("A", 1);
   
       public static void main(String[] args) {
           new Thread(() -> {
               int stamp = stampedReference.getStamp();
               try {
                   TimeUnit.MILLISECONDS.sleep(20);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               //修改stampedReference为B，并将stamp加1
               stampedReference.compareAndSet("A", "B", stampedReference.getStamp(), stampedReference.getStamp() + 1);
               //将stampedReference修改回A，并将stamp加1
               stampedReference.compareAndSet("B", "A", stampedReference.getStamp(), stampedReference.getStamp() + 1);
           }, "t1").start();
   
           new Thread(() -> {
               int stamp = stampedReference.getStamp();
               try {
                   TimeUnit.SECONDS.sleep(1);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               boolean b = stampedReference.compareAndSet("A", "C", stamp, stamp + 1);
               System.out.println(b + "\t" + stampedReference.getReference());
           }, "t2").start();
       }
   }
   ```

# 第七章 原子操作类

## 一，基本类型原子类

1. 常见的API

   - public final int get()：获取当前的值
   - public final int getAndSet(int newValue)：获取当前的值，并设置新的值
   - public final int getAndIncrement()：获取当前的值，并自增
   - public final int getAndDecrement()：获取当前的值，并自减
   - public final int getAndAdd(int delta)：获取当前的值，并加上预期的值
   - boolean compareAndSet(int expect, int update)：如果输入的数值等于预期值，则以原子方式将该值设置为输入值(update)

2. 案例

   ```java
   public class AtomicIntegerDemo {
       public static final int SIZE = 50;
   
       //开启50个线程，每个线程对myNumber++1000次
       public static void main(String[] args) throws InterruptedException {
           MyNumber myNumber = new MyNumber();
   
           CountDownLatch countDownLatch = new CountDownLatch(SIZE);
   
           for (int i = 0; i < SIZE; i++) {
               CompletableFuture.runAsync(() -> {
                   for (int j = 0; j < 1000; j++) {
                       myNumber.addPlusPlus();
                   }
               }).whenComplete((result, e) -> {
                   countDownLatch.countDown();
               });
           }
   
           countDownLatch.await();
   
           System.out.println(myNumber.atomicInteger.get());
       }
   }
   
   class MyNumber {
       AtomicInteger atomicInteger = new AtomicInteger();
   
       public void addPlusPlus() {
           atomicInteger.getAndIncrement();
       }
   }
   ```

## 二，数组类型原子类

## 三，引用类型原子类

1. AtomicReference

2. AtomicStampedReference

   携带版本号的引用类型原子类，可以解决ABA问题

3. AtomicMarkableReference

   简化的AtomicStampedReference，将状态戳简化为true|false

## 四，对象的属性修改原子类

1. 以一种线程安全的方式操作非线程安全对象内的某些字段

2. 更新的属性必须使用public volatile修饰符

3. 因为对象的属性修改类型原子类都是抽象类，所有每次使用都必须使用静态方法newUpdater()创建一个更新器，并且需要设置想要更新的类和属性

4. 案例（AtomicIntegerFieldUpdater）

   ```java
   public class AtomicIntegerUpdaterDemo {
       public static void main(String[] args) throws InterruptedException {
           Bank bank = new Bank();
           CountDownLatch countDownLatch = new CountDownLatch(10);
           for (int i = 0; i < 10; i++) {
               CompletableFuture.runAsync(() -> {
                   for (int j = 0; j < 1000; j++) bank.transMoney();
               }).whenComplete((result, e) -> countDownLatch.countDown());
           }
   
           countDownLatch.await();
   
           System.out.println(bank.balance);
       }
   }
   class Bank {
       private String bankName = "ICBC";
       public volatile int balance = 0;
   
       AtomicIntegerFieldUpdater<Bank> fieldUpdater = AtomicIntegerFieldUpdater.newUpdater(Bank.class, "balance");
   
       public void transMoney() {
           fieldUpdater.getAndIncrement(this);
       }
   }
   ```

5. 案例（AtomicReferenceFieldUpdater）

   ```java
   public class AtomicReferenceFieldUpdaterDemo {
       public static void main(String[] args) {
           MyVar myVar = new MyVar();
           for (int i = 0; i < 5; i++) {
               new Thread(() -> {
                   try {
                       myVar.init();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }, String.valueOf(i)).start();
           }
       }
   }
   class MyVar {
       //判断是否已经初始化
       public volatile Boolean isInit = Boolean.FALSE;
   
       private AtomicReferenceFieldUpdater<MyVar, Boolean> referenceFieldUpdater = AtomicReferenceFieldUpdater.newUpdater(MyVar.class, Boolean.class, "isInit");
   
       public void init() throws InterruptedException {
           if (referenceFieldUpdater.compareAndSet(this, Boolean.FALSE, Boolean.TRUE)) {
               System.out.println("开始初始化");
               TimeUnit.SECONDS.sleep(3);
               System.out.println("初始化完成");
           } else {
               System.out.println(Thread.currentThread().getName() + "初始化失败，已经初始化");
           }
       }
   }
   ```

## 五，原子操作增强类

1. 常用API

   - void add(long x)：将当前的value加x
   - void increment()：将当前的value加1
   - void decrement()：将当前的value减1
   - long sum()：返回当前值，在没有并发更新value的情况下，sum会返回一个精确值，在存在并发的情况下，sum不保证返回精确值
   - void reset()：将value重置为0，可用于替换重新new一个LongAdder，但此方法只可以在没有并发更新的情况下使用
   - long sumThenReset()：获取当前的value，并将value重置为0

2. 简单介绍

   - LongAdder只能用来计算加法，且从零开始计算

   - LongAccumulator提供了自定义的函数操作

   - ```java
     public static void main(String[] args) {
         LongAdder longAdder = new LongAdder();
         longAdder.increment();
         longAdder.increment();
         longAdder.increment();
     
         System.out.println(longAdder.sum());
     
         LongAccumulator longAccumulator = new LongAccumulator((left, right) -> left + right, 0);
         longAccumulator.accumulate(1);
         longAccumulator.accumulate(2);
         longAccumulator.accumulate(3);
     
         System.out.println(longAccumulator.get());
     }
     ```

3. 简单对比

   ```java
   public class AccumulatorCompareDemo {
       public static final int THREADS = 50;
       public static final int _100W = 100 * 10000;
   
       public static void main(String[] args) throws InterruptedException {
           MyResource myResource = new MyResource();
           CountDownLatch countDownLatch = new CountDownLatch(THREADS);
           long start = System.currentTimeMillis();
   
           for (int i = 0; i < THREADS; i++) {
               CompletableFuture.runAsync(() -> {
                   for (int j = 0; j < _100W; j++) {
                       //myResource.incrementBySynchronized();//1273
                       //myResource.incrementByAtomicInteger();//931
                       //myResource.incrementByAtomicIntegerFieldUpdater();//1492
                       //myResource.incrementByLongAdder();//115
                       myResource.incrementByLongAccumulator();//126
                   }
               }).whenComplete((result, e) -> countDownLatch.countDown());
           }
   
           countDownLatch.await();
   
           long end = System.currentTimeMillis();
   
           System.out.println(myResource.longAccumulator.get());
   
           System.out.println(end - start);
       }
   }
   class MyResource {
       public volatile int number = 0;
       public synchronized void incrementBySynchronized() {
           number++;
       }
   
       AtomicInteger atomicInteger = new AtomicInteger(0);
       public void incrementByAtomicInteger() {
           atomicInteger.incrementAndGet();
       }
   
       AtomicIntegerFieldUpdater<MyResource> fieldUpdater = AtomicIntegerFieldUpdater.newUpdater(MyResource.class, "number");
       public void incrementByAtomicIntegerFieldUpdater() {
           fieldUpdater.incrementAndGet(this);
       }
   
       LongAdder longAdder = new LongAdder();
       public void incrementByLongAdder() {
           longAdder.increment();
       }
   
       LongAccumulator longAccumulator = new LongAccumulator((left, right) -> left + right,0);
       public void incrementByLongAccumulator() {
           longAccumulator.accumulate(1);
       }
   }
   ```

4. Striped64类中的一些变量的或者方法的定义

   - base:类似于AtomicLong中全局的value值。在没有竞争情况下数据直接累加到base上，或者cells扩容时，也需要将数据写入到base上
   - collide:表示扩容意向，false一定不会扩容，true可能会扩容。
   - ceIlsBusy:初始化ceIls或者扩容ceIls需要获取锁，0：表示无锁状态1：表示其他线程已经持有了锁
   - casCellsBusy():通过CAS操作修改cellsBusy的值，CAS成功代表获取锁，返回true
   - NCPU:当前计算机CPU数量，CeIl数组扩容时会使用到
   - getProbe():获取当前线程的hash值
   - advanceProbe():重置当前线程的hash值

5. LongAdder原理分析

   LongAdder的基本思路就是分散热点，将value值分散到一个Cell数组中，不同线程会命中到数组的不同槽中，各个线程只对自己槽中的那个值进行CAS操作，这样热点就被分散了，冲突的概率就小很多。如果要获取真正的long值，只要将各个槽中的变量值累加返回。

   sum()会将所有Cell数组中的value和base累加作为返回值，核心的思想就是将之前AtomicLong一个value的更新压力分散到多个value中去，从而降级更新热点。

6. 源码分析

   ```java
   //add(long x)
   public void add(long x) {
       Cell[] as; long b, v; int m; Cell a;
       if ((as = cells) != null || !casBase(b = base, b + x)) {
           boolean uncontended = true;
           if (as == null || (m = as.length - 1) < 0 ||
               (a = as[getProbe() & m]) == null ||
               !(uncontended = a.cas(v = a.value, v + x)))
               longAccumulate(x, null, uncontended);
       }
   }
   ```

   - 如果Cells表为空，尝试用CAS更新base字段，成功则退出
   - 如果Cells表为空，CAS更新base字段失败，出现竞争，uncontended为true,调用longAccumulate;
   - 如果Cells表非空，但当前线程映射的槽为空，uncontended为true,调用longAccumulate;
   - 如果Cells表非空，且前线程映射的槽非空，CAS更新Cell的值，成功则返回，否则，uncontended设为false，调用longAccumulate。

# 第八章 ThreadLocal

## 一，简介

1. 简介

   ThreadLocal提供线程局部变量。这些变量与正常的变量不同，因为每一个线程在访问ThreadLoca实例的时候（通过其get或set方法）都有自己的、独立初始化的变量副本。ThreadLocal实例通常是类中的私有静态字段，使用它的目的是希望将状态（例如，用户ID或事务ID）与线程关联起来。

2. 能干嘛

   实现每一个线程都有自己专属的本地变量副本（自己用自己的变量不麻烦别人，不和其他人共享，人人有份，人各一份）主要解决了让每个线程绑定自己的值，通过使用get()和set()方法，获取默认值或将其值更改为当前线程存的副本的值从而避免了线程安全问题。

3. api

   - T get()：返回当前线程的此线程局部变量副本中的值
   - T initialValue()：返回此线程局部变量的当前线程的“初始值”
   - void remove()：删除此线程局部变量的当前线程值
   - void set(T value)：将此线程局部变量的当前线程副本设置为指定值
   - static ThreadLocal withInitial(Supplier supplier)：创建一个线程局部变量

4. 案例

   ```java
   public class ThreadLocalDemo {
       public static void main(String[] args) throws InterruptedException {
           MyNumber myNumber = new MyNumber();
           CountDownLatch countDownLatch = new CountDownLatch(5);
   
           for (int i = 0; i < 5; i++) {
               CompletableFuture.runAsync(() -> {
                   int anInt = new Random().nextInt(10);
                   for (int j = 0; j < anInt; j++) myNumber.increment();
                   System.out.println(Thread.currentThread().getName() + ":" + myNumber.number.get());
               }).whenComplete((result, e) -> {
                   countDownLatch.countDown();
                   //使用完成之后必须remove()
                   myNumber.number.remove();
               });
           }
   
           countDownLatch.await();
       }
   }
   class MyNumber {
       //创建ThreadLocal
       ThreadLocal<Integer> number = ThreadLocal.withInitial(() -> 0);
   
       public void increment() {
           number.set(number.get() + 1);
       }
   }
   ```

5. Thread，ThreadLocal，ThreadLocalMap的关系

   ThreadLocalMap就是一个保存ThreadLocal对象的map（其实就是以ThreadLocal为key），不过是经过了两层包装的ThreadLocal对象

   JVM内部维护了一个线程版的Map<ThreadLocal, Value>（通过ThreadLocal对象的set方法，把ThreadLocal对象自己当做key放进ThreadLocalMap中），每个线程要用到这个T的时候，用当前的线程去Map里面获取，通过这样让每个线程都拥有了自己独立的变量

# 第九章 Java对象内存布局和对象头

1. markword分布图（64位）

   ![](F:\截图\屏幕截图 2022-09-07 112115.png)

2. JOL

   - 引入依赖

     ```xml
     <dependency>
         <groupId>org.openjdk.jol</groupId>
         <artifactId>jol-core</artifactId>
         <version>0.9</version>
     </dependency>
     ```

   - 测试

     ```java
     public static void main(String[] args) {
         Object o = new Object();
         System.out.println(ClassLayout.parseInstance(o).toPrintable());
     }
     ```

   - 结果

     ![](F:\截图\屏幕截图 2022-09-07 113317.png)

# 第十章 Synchronized与锁升级

## 一，偏向锁

1. 单线程竞争

   当线程A第一次竞争到锁时，通过操作修改Mark Word中的偏向线程ID、偏向模式。如果不存在其他线程竞争，那么持有偏向锁的线程将永远不需要进行同步。

2. 主要作用

   当一段同步代码一直被同一个线程多次访问，由于只有一个线程那么该线程在后续访问时便会自动获得锁

3. 启动偏向锁

   实际上偏向锁在JDK1.6之后是认开启的，但是启动时间有延迟，所以需要添加参数-XX:BiasedLockingStartupDelay=0,让其在程序肩动时立刻后动

   - 开启偏向锁

      -XX:+UseBiasedLocking	-XX:BiasedLockingStartupDelay=0

   - 关闭偏向锁

      -XX:-UseBiasedLocking

4. 偏向锁的撤销

   偏向锁使用一种等到竞争出现才释放锁的机制，只有当其他线程竞争锁时，持有偏向锁的原来线程才会被撤销，撤销需要等待全局安全点（该时间点上没有字节码正在执行)，同时检查持有偏向锁的线程是否还在执行：

   - 第一个线程正在执行synchronized方法（处于同步块），它还没有执行完，其它线程来抢夺，该偏向锁会被取消掉并出现锁升级。此时轻量级锁由原持有偏向锁的线程持有，继续执行其同步代码，而正在竞争的线程会进入自旋等待获得该轻量级锁。
   - 第一个线程执行完成synchronized方法（退出同步块），则将对象头设置成无锁状态并撤销偏向锁，重新偏向。

## 二，轻量级锁

1. 主要作用

   有线程来参与锁的竞争，但是获取锁的冲突时间极短

2. 轻量锁的获取

   假如线程A己经拿到锁，这时线程B又来抢该对象的锁，由于该对象的锁己经被线程A拿到，当前该锁己是偏向锁了。而线程B在争抢时发现对象头Mark Word中的线程ID不是线程B自己的线程ID而是线程A，那线程B就会进行CAS操作希望能获得锁。此时线程B操作中有两种情况：

   如果锁获取成功，直接替换Mark Word中的线程ID为B自己的ID(A→B),重新偏向于其他线程（即将偏向锁交给其他线程，相当于当前线程"被"释放了锁)，该锁会保持偏向锁状态，A线程结束，B线程上位；

   如果锁获取失数，则偏向锁升级为轻量级锁（设置偏向锁标识为0并设置锁标志位为00），此时轻量级锁由原持有偏向锁的线程持有，继续执行其同步代码，而正在竞争的线程B会进入自旋等待获得该轻量级锁。

3. 轻量锁的加锁

   JVM会为每个线程在当前线程的栈帧中创建用于存储锁记录的空间，官方成为Displaced Mark Word。若一个线程获得锁时发现是轻量级锁，会把锁的Mark Word复制到自己的Displaced Mark Word里面。然后线程尝试用CAS将锁的Mark Word替换为指向锁记录的指针。如果成功，当前线程获得锁，如果失败，表示Mark Word已经被替换成了其他线程的锁记录，说明在与其它线程竞争锁，当前线程就尝试使用自旋来获取锁。

   自旋CAS:不断尝试去获取锁，能不升级就不升级，尽量不要阻塞

4. 轻量锁的释放

   在释放锁时，当前线程会使用CAS操作将Displaced Mark Word的内容复制回锁的Mark Word里面。如果没有发生竞争，那么这个复制的操作会成功。如果有其他线程因为自旋多次导致轻量级锁升级成了重量级锁，那么CAS操作会失败，此时会释放锁并唤醒被阻塞的线程。

5. 自旋次数和程度

   - 在java6之前：默认情况下自选次数是10次，可以使用-XX:PreBlockSpin=10来修改，或者自旋的线程数超过了CPU核数的一半
   - 在java6之后：自适应自旋锁，线程如果自旋成功了，那下次自旋的最大次数会增加，因为JVM认为既然上次成功了，那么这一次也很大概率会成功。反之如果很少会自旋成功，那么下次会减少自旋的次数甚至不自旋，避免CPU空转。

## 三，重量级锁

1. 重量级锁原理

   Java中synchronized的重量级锁，是基于进入和退出Monitor对象实现的。在编译时会将同步块的开始位置插入monitor enter指令，在结束位置插入monitor exit指令。

   当线程执行到monitor enter指令时，会尝试获取对象所对应的Monitor所有权，如果获取到了，即获取到了锁，会在Monitor的owner中存放当前线程的id，这样它将处于锁定状态，除非退出同步块，否则其他线程无法获取到这个Monitor。

## 四，总结

1. 锁的指向
   - 偏向锁：Mark Word存储的是偏向的线程ID
   - 轻量锁：Mark Word存储的是指向线程中Lock Record的指针
   - 重量锁：Mark Word存储的是指向堆中的monitor对象的指针

2. 锁升级之后，hashcode去哪里了

   - ==在无锁状态下==，Mark Word中可以存储对象的identity hash code值。当对象的hashCode()方法第一次被调用时，JVM会生成对应的identity hash code值并将该值存储到Mark Word中。

   - ==对于偏向锁===，在线程获取偏向锁时，会用Thread ID和epoch值覆盖identity hash code所在的位置。如果一个对象的hashCode()方法已经被调用过一次之后，这个对象不能被设置偏向锁。因为如果可以的话，那Mark Word中的identity hash code必然会被偏向线程lD给覆盖，这就会造成同一个对象前后两次调用hashCode()方法得到的结果不一致。

   - ==升级为轻量级锁时==，JVM会在当前线程的栈帧中创建一个锁记录Lock Record空间，用于存储锁对象的Mark Word拷贝，该拷贝中可以包含identity hash code，所以轻量级锁可以和identity hash code共存，哈希码和GC年龄自然保存在此，释放锁后会将这些信息写回到对象头。

   - ==升级为重量级锁后==，Mark Word保存的重量级锁指针，代表重量级锁的ObjectMonitor类里有字段记录非加锁状态下的Mark Word，锁释放后也会将信息写回到对象头。

3. 各种锁的优缺点

   ![](F:\截图\屏幕截图 2022-09-07 174954.png)

4. synchronized锁升级过程总结

   先自旋，不行再阻塞。实际上是把之前的悲观锁（重量级锁）变成在一定条件下使用偏向锁以及使用轻量级（自旋锁CAS）的形式

   synchronized在修饰方法和代码块在字节码上实现方式有很大差异，但是内部实现还是基于对象头的Mark Word来实现的。JDK1.6之前synchronized使用的是重量级锁，JDK1.6之后进行了优化，拥有了无锁->偏向锁->轻量级锁->重量级锁的升级过程，而不是无论什么情况都使用重量级锁。

5. 锁消除

   从JIT角度看相当于无视它，synchronized(lock)不存在了，这个锁对象并没有被共用扩散到其它线程使用，极端的说就是根本没有这个锁对象的底层机器码，消除了锁的使用

   ```java
   public static void main(String[] args) {
       for (int i = 0; i < 10; i++) {
           new Thread(() -> {
               Object lock = new Object();
               synchronized (lock) {
                   System.out.println(Thread.currentThread().getName());
               }
           }, String.valueOf(i)).start();
       }
   }
   ```

6. 锁粗化

   假如方法中首尾相接，前后相邻的都是同一个锁对象，JIT编译器就会把这几个synchronized块合并成一个大块，加粗加大范围，一次申请锁使用即可，避免次次的申请和释放锁，是开了性能

   ```java
   public static void main(String[] args) {
       Object lock = new Object();
       synchronized (lock) {
           System.out.println(1);
       }
       synchronized (lock) {
           System.out.println(2);
       }
       synchronized (lock) {
           System.out.println(3);
       }
       synchronized (lock) {
           System.out.println(4);
       }
   }
   ```

# 第十一章 AQS

## 一，AQS基本概念

1. AbstractQueuedSynchronizer

   是用来实现锁或者其它同步器组件的公共基础部分的抽象实现，是重量级基础框架及整个JUC体系的基石，主要用于解决锁分配给"谁"的问题

   整体就是一个抽象的FIFO队列来完成资源获取线程的排队工作，并通过一个int类变量表示持有锁的状态

2. 解释说明

   如果共享资源被占用，就需要一定的阻塞等待唤醒机制来保证锁分配。这个机制主要用的是CLH队列的变体实现的，将暂时获取不到锁的线程加入到队列中，这个队列就是AQS同步队列的抽象表现。它将要请求共享资源的线程及自身的等待状态封装成队列的结点对象(Node)，通过CAS、自旋以及LockSupport.park()的方式，维护state变量的状态，使并发达到同步的效果。

   ![](F:\截图\屏幕截图 2022-09-08 095631.png)

## 二，AQS内部的体系架构

1. AQS本身

   ```java
   /**
    * The synchronization state.
    */
   private volatile int state;
   ```

   state变量+CLH双端队列

2. Node节点

   - 模式
     - SHARED：表示线程以共享的模式等待锁
     - EXCLUSIVE：表示线程正在以独占的方式等待锁
   - 方法和属性值
     - waitStatus：当前节点在队列中的状态
     - thread：表示处于该节点的线程
     - prev：前驱指针
     - predecessor：返回前驱节点，没有的话抛出npe
     - nextWater：指向下一个处于CONDITION状态的节点
     - next：后继指针
   - 枚举
     - 0：当一个Node被初始化的时候的默认值
     - CANCELLED：为1，表示线程获取锁的请求已经取消了
     - CONDITION：为-2，表示节点在等待队列中，节点线程等待唤醒
     - PROPAGATE：为-3，当前线程处于在SHARED情况下，该字段才会使用
     - SIGNAL：为-1，表示线程已经准备好了，等待资源释放

## 三，源码分析

1. lock()

   对比公平锁和非公平锁的tryAcquire()方法的实现代码，其实差别就在于非公平锁获取锁时比公平锁中少了一个判断!hasQueuedPredecessors()

   hasQueuedPredecessors()中判断了是否需要排队，导致公平锁非公平锁的差异如下：

   - 公平锁：公平锁讲究先来先到，线程在获取锁时，如果这个锁的等待队列中已经有线程在等待，那么当前线程就会进入等待队列中；
   - 非公平锁：不管是否有等待队列，如果可以获取锁，则立刻占有锁对象。也就是说队列的第一个排队线程苏醒后，不一定就是排头的这个线程获得锁，它还是需要参加竞争锁（存在线程竞争的情况下），后来的线程可能不讲武德插队夺锁了。

   ```java
   //FairSync
   final void lock() {
       acquire(1);
   }
   
   //NonfairSync
   final void lock() {
       if (compareAndSetState(0, 1))
           setExclusiveOwnerThread(Thread.currentThread());
       else
           acquire(1);
   }
   ```

2. acquire();

   ```java
   public final void acquire(int arg) {
       if (!tryAcquire(arg) &&
           acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
           selfInterrupt();
   }
   ```

   首先进入tryAcquire(arg)方法

   ```java
   final boolean nonfairTryAcquire(int acquires) {
       final Thread current = Thread.currentThread();
       //获取状态值
       int c = getState();
       //state为0，CAS修改state，尝试获取锁
       if (c == 0) {
           if (compareAndSetState(0, acquires)) {
               setExclusiveOwnerThread(current);
               return true;
           }
       }
       //判断当前持有锁的线程是不是当前进入的线程
       else if (current == getExclusiveOwnerThread()) {
           int nextc = c + acquires;
           if (nextc < 0) // overflow
               throw new Error("Maximum lock count exceeded");
           setState(nextc);
           return true;
       }
       //state不为0，有线程占用锁，直接返回false
       return false;
   }
   ```

   如果已经有线程占用锁，tryAcquire(arg)方法返回false，取反之后为true，进入到addWaiter(Node.EXCLUSIVE)方法

   ```java
   private Node addWaiter(Node mode) {
       Node node = new Node(Thread.currentThread(), mode);
       // Try the fast path of enq; backup to full enq on failure
       //将尾结点(tail)赋给pred
       Node pred = tail;
       //尾结点不为null，说明已经有节点在队列中
       if (pred != null) {
           //将该节点的prev指针指向尾结点(pred)
           node.prev = pred;
           //CAS修改尾结点为当前进入的节点
           if (compareAndSetTail(pred, node)) {
               //将上一次的尾结点的next指针指向为本次进入的节点
               pred.next = node;
               return node;
           }
       }
       //尾结点为null执行enq(node);
       enq(node);
       return node;
   }
   ```

   如果尾结点为null，队列为空，需要初始化，执行enq(node)方法

   ```java
   private Node enq(final Node node) {
       for (;;) {
           Node t = tail;
           //尾结点为null，需要初始化
           if (t == null) { // Must initialize
               //使用CAS设置头结点，这个节点为一个虚拟节点，不是任何一个用户线程
               if (compareAndSetHead(new Node()))
                   tail = head;
           //尾结点不为null，将当前进入的节点放到最后即可，同上
           } else {
               node.prev = t;
               if (compareAndSetTail(t, node)) {
                   t.next = node;
                   return t;
               }
           }
       }
   }
   ```

   节点入队成功之后就会进入acquireQueued(addWaiter(Node.EXCLUSIVE), arg)方法

   ```java
   final boolean acquireQueued(final Node node, int arg) {
       boolean failed = true;
       try {
           boolean interrupted = false;
           for (;;) {
               //获取当前节点的前一个节点
               final Node p = node.predecessor();
               //判断前置节点是否为头结点(即虚拟节点)，如果是调用tryAcquire(arg)尝试获取锁
               if (p == head && tryAcquire(arg)) {
                   setHead(node);
                   p.next = null; // help GC
                   failed = false;
                   return interrupted;
               }
               //获取锁失败进入先shouldParkAfterFailedAcquire(p, node)
               if (shouldParkAfterFailedAcquire(p, node) &&
                   parkAndCheckInterrupt())
                   interrupted = true;
           }
       } finally {
           if (failed)
               cancelAcquire(node);
       }
   }
   ```

   当当前节点的前置节点为头结点（即虚拟节点）时，尝试获取锁，如果获取失败进入到shouldParkAfterFailedAcquire(p, node)方法

   ```java
   private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
       //获取前置节点的waitStatus
       int ws = pred.waitStatus;
       //判断前置节点waitStatus是否为Node.SIGNAL(-1)
       if (ws == Node.SIGNAL)
           return true;
       //判断前置节点waitStatus是否大于0，大于0说明是CANCELLED状态，忽略该状态的节点，重新连接队列
       if (ws > 0) {
           do {
               node.prev = pred = pred.prev;
           } while (pred.waitStatus > 0);
           pred.next = node;
       } else {
           //创建节点的waitStatus默认为0，所以第一次进入会调用compareAndSetWaitStatus(pred, ws, Node.SIGNAL)，CAS设置前置节点的waitStatus为Node.SIGNAL(-1)
           compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
       }
       return false;
   }
   ```

   第一次进入会调用compareAndSetWaitStatus(pred, ws, Node.SIGNAL)，CAS设置前置节点的waitStatus为Node.SIGNAL(-1)，返回false，再一次进入循环如果前置节点不为头结点或者依旧尝试抢锁失败，会再次进入到shouldParkAfterFailedAcquire(p, node)方法。第二次进入时前置节点的waitStatus已经被修改为-1，因此直接返回了true。shouldParkAfterFailedAcquire(p, node)方法返回true之后，会进入到parkAndCheckInterrupt()方法。

   ```java
   调用tryRelease(arg)，尝试释放锁//parkAndCheckInterrupt()方法直接调用LockSupport.park(this)，阻塞当前线程
   private final boolean parkAndCheckInterrupt() {
       LockSupport.park(this);
       return Thread.interrupted();
   }
   ```

3. unlock()

   ```java
   public void unlock() {
       sync.release(1);
   }
   ```

   首先进入sync.release(1);方法

   ```java
   public final boolean release(int arg) {
       //调用tryRelease(arg)，尝试释放锁
       if (tryRelease(arg)) {
           Node h = head;
           if (h != null && h.waitStatus != 0)
               unparkSuccessor(h);
           return true;
       }
       return false;
   }
   ```

   调用tryRelease(arg)，尝试释放锁

   ```java
   protected final boolean tryRelease(int releases) {
       //因为之前锁一直被占用所有state为1，c = 1 - 1为0
       int c = getState() - releases;
       //判断当前线程不是持有锁的线程
       if (Thread.currentThread() != getExclusiveOwnerThread())
           throw new IllegalMonitorStateException();
       boolean free = false;
       //c等于0，调用setExclusiveOwnerThread(null);方法将持有锁的线程设置为null，将free设置为true
       if (c == 0) {
           free = true;
           setExclusiveOwnerThread(null);
       }
       //设置state为0，表示没有线程持有锁
       setState(c);
       //成功释放锁，返回true
       return free;
   }
   ```

   如果tryRelease(arg)方法成功释放锁，返回true，回到sync.release(1)方法中继续执行

   ```java
   public final boolean release(int arg) {
       if (tryRelease(arg)) {
           Node h = head;
           //如果头结点不为null，且头结点waitStatus不为0，则进入unparkSuccessor(h);
           if (h != null && h.waitStatus != 0)
               unparkSuccessor(h);
           return true;
       }
       return false;
   }
   ```

   因为在lock过程中的shouldParkAfterFailedAcquire(p, node)方法中将前置节点的waitStatus设置为-1，因此头结点的waitStatus为-1不为0，进入unparkSuccessor(h);

   ```java
   private void unparkSuccessor(Node node) {
       //头结点的waitStatus为-1
       int ws = node.waitStatus;
       if (ws < 0)
           //CAS设置头结点的waitStatus为0
           compareAndSetWaitStatus(node, ws, 0);
       //获取头结点的下一个节点，即第一个真实的用户线程的节点
       Node s = node.next;
       if (s == null || s.waitStatus > 0) {
           s = null;
           for (Node t = tail; t != null && t != node; t = t.prev)
               if (t.waitStatus <= 0)
                   s = t;
       }
       //s不为null，唤醒s节点上的线程
       if (s != null)
           LockSupport.unpark(s.thread);
   }
   ```

   第一个真实的用户线程的节点被唤醒，继续执行代码，回到acquireQueued(final Node node, int arg)方法中的循环体继续执行

   ```java
   final boolean acquireQueued(final Node node, int arg) {
       boolean failed = true;
       try {
           boolean interrupted = false;
           for (;;) {
               final Node p = node.predecessor();
               //再次尝试获取锁
               if (p == head && tryAcquire(arg)) {
                   setHead(node);
                   p.next = null; // help GC
                   failed = false;
                   return interrupted;
               }
               if (shouldParkAfterFailedAcquire(p, node) &&
                   parkAndCheckInterrupt())
                   interrupted = true;
           }
       } finally {
           if (failed)
               cancelAcquire(node);
       }
   }
   ```

   进入tryAcquire(arg)方法，再次尝试获取锁

   ```java
   protected final boolean tryAcquire(int acquires) {
       return nonfairTryAcquire(acquires);
   }
   ```

   ```java
   final boolean nonfairTryAcquire(int acquires) {
       final Thread current = Thread.currentThread();
       //因为已经释放锁了，state为0
       int c = getState();
       if (c == 0) {
           //CAS设置state为1
           if (compareAndSetState(0, acquires)) {
               //设置当前线程为持有锁的线程
               setExclusiveOwnerThread(current);
               return true;
           }
       }
       else if (current == getExclusiveOwnerThread()) {
           int nextc = c + acquires;
           if (nextc < 0) // overflow
               throw new Error("Maximum lock count exceeded");
           setState(nextc);
           return true;
       }
       return false;
   }
   ```

   如果是非公平锁有可能会有新的线程先抢到锁，所以tryAcquire(arg)方法可能返回的是false，如果当前线程抢到了锁，则返回true，返回true之后继续执行如下代码

   ```java
   if (p == head && tryAcquire(arg)) {
       setHead(node);
       //设置头节点的next为null
       p.next = null; // help GC
       failed = false;
       return interrupted;
   }
   
   
   private void setHead(Node node) {
       //设置当前节点为头结点
       head = node;
       //设置当前节点的线程为null
       node.thread = null;
       //设置当前节点的prev为null
       node.prev = null;
   }
   ```

   此时已经没有任何指针指向了头节点，帮助GC。并且因为当前节点被设置为了null，所有当前节点成为了新的的虚拟节点，而且是头结点

4. cancelAcquire(node)

   ```java
   //node为需要取消的节点
   private void cancelAcquire(Node node) {
       //判断node是否为null
       if (node == null)
           return;
   	//将node的线程设置为null
       node.thread = null;
   
       // 获取node的前置节点
       Node pred = node.prev;
       //循环判断前置节点的waitStatus是否大于0(即是否为取消状态)，如果前置节点也为取消状态，则获取前置节点的前置节点赋给pred继续判断，直到遇到不为取消状态的节点
       while (pred.waitStatus > 0)
           node.prev = pred = pred.prev;
   
       // 获取pred的next
       Node predNext = pred.next;
   
       // 设置当前节点的waitStatus为Node.CANCELLED(1)
       node.waitStatus = Node.CANCELLED;
   
       // 如果当前节点为尾结点，直接CAS移除自己
       if (node == tail && compareAndSetTail(node, pred)) {
           compareAndSetNext(pred, predNext, null);
       } else {
           int ws;
           //判断pred的waitStatus是否为Node.SIGNAL，如果不为Node.SIGNAL且小于0则设置为Node.SIGNAL，设置成功之后同时pred不为head，pred的线程不为null，进入if
           if (pred != head &&
               ((ws = pred.waitStatus) == Node.SIGNAL ||
                (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) &&
               pred.thread != null) {
               //获取当前node的next
               Node next = node.next;
               //如果next不为null，且不为取消状态
               if (next != null && next.waitStatus <= 0)
                   //CAS设置pred的next指针指向当前node的next
                   compareAndSetNext(pred, predNext, next);
           } else {
               unparkSuccessor(node);
           }
   		//设置断开当前node的next指针，帮助GC回收当前node
           node.next = node; // help GC
       }
   }
   ```

# 第十二章 读写锁

## 一，简介

1. 读写锁的定义：一个资源能够被多个读线程访问，或者被一个写线程访问，但是不能同时存在读写线程

2. 锁降级

   ReentrantReadWriteLock锁降级：将写入锁降级为读锁，锁的严苛程度变强叫做升级，反之叫做降级。

   - 如果同一个线程持有了写锁，在没有释放写锁的情况下，它还可以继续获得读锁。这就是写锁的降级，降级成为了读锁。
   - 规则惯例，先获取写锁，然后获取读锁，再释放写锁的次序。
   - 如果释放了写锁，那么就完全转换为读锁。

# 第十三章 StampLock

## 一，StampLock的三种访问模式

1. Reading(读模式悲观)：功能和ReentrantReadWriteLock的读锁类似
2. Writing(写模式)：功能和ReentrantResdWriteLockl的写锁类似
3. Optimistic reading(乐观读模式)：无锁机制，类似于数据库中的乐观锁，支持读写并发，很乐观认为读取时没人修改，假如被修改再实现升级为悲观读模式

## 二，案例

```java
public class StampLockDemo {
    public static void main(String[] args) {
        MyNumber myNumber = new MyNumber();
        new Thread(() -> {
            myNumber.optimisticRead();
        }).start();
        new Thread(() -> {
            myNumber.write(1);
        }).start();
    }
}
class MyNumber {
    int number = 0;

    StampedLock stampedLock = new StampedLock();

    public void write(int n) {
        long stamp = stampedLock.writeLock();
        System.out.println("正在写入");
        try {
            number += n;
        } finally {
            System.out.println("写入完成,number为" + number);
            stampedLock.unlockWrite(stamp);
        }
    }

    //悲观读
    public void read() {
        long stamp = stampedLock.readLock();
        System.out.println("正在读取");
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            System.out.println("读取完成,number为" + number);
            stampedLock.unlockRead(stamp);
        }
    }

    //乐观读
    public void optimisticRead() {
        long stamp = stampedLock.tryOptimisticRead();
        System.out.println("正在读取");
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            //判断是否有线程修改number
            if (stampedLock.validate(stamp)) {
                //没有线程修改，读取成功
                System.out.println("读取完成,number为" + number);
            } else {
                //有线程修改，读取失败，升级为悲观锁
                stamp = stampedLock.readLock();
                System.out.println("读取失败，升级为悲观锁");
                try {
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    System.out.println("读取完成,number为" + number);
                    stampedLock.unlockRead(stamp);
                }
            }
        }
    }
}
```

**StampedLock的缺点**

- StampedLock不支持重入
- StampedLock的悲观读锁和写锁都不支持条件变量(Condition),这个也需要注意。
- 使用StampedLock一定不要调用中断操作，即不要调用interrupt()方法
