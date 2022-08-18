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

   - 在Java8中，CompletableFuture提供了非常强大的Future的扩展功能小可以帮助我们简化异步编程的复杂性，并且提供了函数式编程的能力，可以通过回调的方式处理计算结果，也提供了转换和组合CompletableFuture的方法。
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

个线程不应该由其他线程来强制中断或停止而是应该由线程自己自行停止。所以，Thread.stop,Thread.suspend,Thread.resume都已经被废弃了。

1. 什么是中断机制

   在Java中没有办法立即停止一条线程，然而停止线程却显得尤为重要，如取消一个耗时操作。因此，Java提供了一种用于停止线程的协商机制一一中断，也即中断标识协商机制。

   中断只是一种协作协商机制，Java没有给中断增加任何语法，中断的过程完全需要程序员自己实现。若要中断一个线程，你需要手动调用该线程的interrupt方法，该方法也仅仅是将线程对象的中断标识设成true，接着你需要自己写代码不断地检测当前线程的标识位，如果为true，表示别的线程请求这条线程中断，此时究竞该做什么需要你自己写代码实现。

   每个线程对象中都有一个中断标识位，用于表示线程是否被中断；该标识位为true表示中断，为false表示未中断；通过调用线程对象的interrupt方法将该线程的标识位设为true可以在别的线程中调用，也可以在自己的线程中调用。

2. 中断相关的API说明

   - public void interrupt()

     仅仅是色设置线程的中断状态为true，发起一个协商而不会立刻停止线程

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
     - 在每个volatile读操作的后面插入一个LoadStore屏障，禁止处理器把上面的volatile写与下面的普通读重排序
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

