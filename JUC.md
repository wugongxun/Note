# 第一章 JUC概述

## 一，什么是JUC

在Java中，线程部分是一个重点，本篇文章说的JUC也是关于线程的。JUC
就是java.util.concurrent工具包的简称。这是一个处理线程的工具包，JDK
1.5开始出现的。

## 二，线程和进程的概念

1. 进程和线程

   - 进程(Process)：是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。在当代面向线程设计的计算机结构中，进程是线程的容器。程序是指令、数据及其组织形式的描述，进程是程序的实体。是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。程序是指令、数据及其组织形式的描述，进程是程序的实体。
   - 线程(thread)：是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。

2. 线程的状态

   - NEW（新建）
   - RUNABLE（准备就绪）
   - BLOCKED（阻塞）
   - WAITING（等待）
   - TIMED_WAITING（超时等待）
   - TERMINATED（销毁）

3. wait和sleep的区别

   - sleep是Thread的静态方法，wait是Object的方法，任何对象实例都能调用。
   - sleep不会释放锁，它也不需要占用锁。wait会释放锁，但调用它的前提是当前线程占有锁（即代码要在synchronized中）。
   - 他们都可以被interrupted方法中断

4. 并发和并行

   - 串行模式

     串行表示所有任务都一一按先后顺序进行。串行意味着必须先装完一车柴才能运送这车柴，只有运送到了，才能卸下这车柴，并且只有完成了这整个三个步骤，才能进行下一个步骤。

     **串行是一次只能取得一个任务，并执行这个任务。**

   - 并行模式

     并行意味着可以同时取得多个任务，并同时去执行所取得的这些任务。并行模式相当于将长长的一条队列，划分成了多条短队列，所以并行缩短了任务队列的长度。并行的效率从代码层次上强依赖于多进程/多线程代码，从硬件角度上则依赖于多核CPU。

   - 并发

     并发(concurrent)指的是多个程序可以同时运行的现象，更细化的是多进程可以同时运行或者多指令可以同时运行。但这不是重点，在描述并发的时候也不会去扣这种字眼是否精确，==并发的重点在于它是一种现象==，==并发描述的是多进程同时运行的现象==。但实际上，对于单核心CU来说，同一时刻只能运行一个线程。所以，这里的"同时运行"表示的不是真的同一时刻有多个线程运行的现象，这是并行的概念，而是提供一种功能让用户看来多个程序同时运行起来了，但实际上这些程序中的进程不是一直霸占CPU的，而是执行一会停一会。

     要解决大并发问题，通常是将大任务分解成多个小任务，由于操作系统对进程的调度是随机的，所以切分成多个小任务后，可能会从任一小任务处执行。这可能会出现一些现象：

     - 可能出现一个小任务执行了多次，还没开始下个任务的情况。这时一般会采用队列或类似的数据结构来存放各个小任务的成果
     - 可能出现还没准备好第一步就执行第二步的可能。这时，一般采用多路复用或异步的方式，比如只有准备好产生了事件通知才执行某个任务。
     - 可以多进程/多线程的方式并行执行这些小任务。也可以单进程/单线程执行这些小任务，这时很可能要配合多路复用才能达到较高的效率

5. 管程

   是一种同步机制，保证同一时间，只有一个线程访问被保护数据或者代码

6. 用户线程和守护线程

   - 用户线程：自定义线程

   - 守护线程：比如垃圾回收

     setDaemon(true)；设置守护线程

# 第二章 Lock接口

## 一，Lock锁概述

1. 什么是Lock锁

   Lock锁实现提供了比使用同步方法和语句可以获得的更广泛的锁操作。它们允许更灵活的结构，可能具有非常不同的属性，并且可能支持多个关联的条件对象。Lock提供了比synchronized更多的功能。

2. Lock与Synchronize区别

   - Lock不是Java语言内置的，synchronized是Java语言的关键字，因此是内置特性。Lock是一个类，通过这个类可以实现同步访问；
   - Lock和synchronized有一点非常大的不同，采用synchronized不需要用户去手动释放锁，当synchronized方法或者synchronized代码块执行完之后系统会自动让线程释放对锁的占用；而Lock则必须要用户去手动释放锁，如果没有主动释放锁，就有可能出现死锁的现象
   - Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
   - 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
   - Lock可以提高多个线程进行读操作的效率。、

# 第三章 线程中的通信

1. synchronized实现线程通信

   ```java
   public class ThreadDemo {
       public static void main(String[] args) {
           Share share = new Share();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.incr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test1").start();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.decr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test2").start();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.incr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test3").start();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.decr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test4").start();
       }
   }
   
   class Share {
       private int number = 0;
   
       synchronized void incr() throws InterruptedException {
           while (number != 0) {
               this.wait();
           }
           System.out.println(Thread.currentThread().getName() + ":" + ++number);
           this.notifyAll();
       }
   
       synchronized void decr() throws InterruptedException {
           while (number != 1) {
               this.wait();
           }
           System.out.println(Thread.currentThread().getName() + ":" + --number);
           this.notifyAll();
       }
   }
   ```

2. Lock实现线程通信

   ```java
   public class ThreadDemo {
       public static void main(String[] args) {
           Share share = new Share();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.incr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test1").start();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.decr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test2").start();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.incr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test3").start();
           new Thread(() -> {
               for (int i = 0; i < 10; i++) {
                   try {
                       share.decr();
                   } catch (InterruptedException e) {
                       e.printStackTrace();
                   }
               }
           }, "test4").start();
       }
   }
   
   class Share {
       private int number = 0;
       private Lock lock = new ReentrantLock();
       private Condition condition = lock.newCondition();
   
       void incr() throws InterruptedException {
           lock.lock();
           try {
               while (number != 0) {
                   condition.await();
               }
               System.out.println(Thread.currentThread().getName() + ":" + ++number);
               condition.signalAll();
           } catch (InterruptedException e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
       }
   
       void decr() throws InterruptedException {
           lock.lock();
           try {
               while (number != 1) {
                   condition.await();
               }
               System.out.println(Thread.currentThread().getName() + ":" + --number);
               condition.signalAll();
           } catch (InterruptedException e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
       }
   }
   ```

3. 线程间的定制化通信

   ```java
   public class ThreadDemo2 {
       public static void main(String[] args) {
           ShareResource shareResource = new ShareResource();
           new Thread(() -> {
               for (int i = 0; i < 5; i++) {
                   shareResource.print5(i + 1);
               }
           }, "test1").start();
           new Thread(() -> {
               for (int i = 0; i < 5; i++) {
                   shareResource.print10(i + 1);
               }
           }, "test2").start();
           new Thread(() -> {
               for (int i = 0; i < 5; i++) {
                   shareResource.print15(i + 1);
               }
           }, "test3").start();
       }
   }
   
   class ShareResource {
       private int flag = 1;
       private Lock lock = new ReentrantLock();
       private Condition c1 = lock.newCondition();
       private Condition c2 = lock.newCondition();
       private Condition c3 = lock.newCondition();
   
       void print5(int loop) {
           lock.lock();
           try {
               while (flag != 1) {
                   c1.await();
               }
               for (int i = 0; i < 5; i++) {
                   System.out.println(Thread.currentThread().getName() + ":第" + loop + "轮");
               }
               flag = 2;
               c2.signal();
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
       }
       void print10(int loop) {
           lock.lock();
           try {
               while (flag != 2) {
                   c2.await();
               }
               for (int i = 0; i < 10; i++) {
                   System.out.println(Thread.currentThread().getName() + ":第" + loop + "轮");
               }
               flag = 3;
               c3.signal();
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
       }
       void print15(int loop) {
           lock.lock();
           try {
               while (flag != 3) {
                   c3.await();
               }
               for (int i = 0; i < 15; i++) {
                   System.out.println(Thread.currentThread().getName() + ":第" + loop + "轮");
               }
               flag = 1;
               c1.signal();
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               lock.unlock();
           }
       }
   }
   ```

# 第四章 多线程锁

1. synchronized实现同步的基础：
   - 对于普通同步方法，锁是当前实例对象。
   - 对于静态同步方法，锁是当前类的Class对象。
   - 对于同步方法块，锁是Synchonized括号里配置的对象

2. 公平锁与非公平锁

   - 非公平锁

     线程饿死，效率高

   - 公平锁

     效率相对低

3. 可重入锁

   - synchronized实现

     ```java
     public class ThreadDemo3 {
         public static void main(String[] args) {
             Object o = new Object();
             new Thread(() -> {
                 synchronized(o) {
                     System.out.println(Thread.currentThread().getName() + "外层");
                     synchronized(o) {
                         System.out.println(Thread.currentThread().getName() + "中层");
                         synchronized(o) {
                             System.out.println(Thread.currentThread().getName() + "内层");
                         }
                     }
                 }
             }, "test1").start();
         }
     }
     ```

   - 使用Lock实现

     ```java
     public class ThreadDemo3 {
         public static void main(String[] args) {
             ReentrantLock lock = new ReentrantLock();
             new Thread(() -> {
                 try {
                     lock.lock();
                     System.out.println(Thread.currentThread().getName() + "外层");
                     try {
                         lock.lock();
                         System.out.println(Thread.currentThread().getName() + "内层");
                     } catch (Exception e) {
                         e.printStackTrace();
                     } finally {
                         lock.unlock();
                     }
                 } catch (Exception e) {
                     e.printStackTrace();
                 } finally {
                     lock.unlock();
                 }
             }, "test1").start();
         }
     }
     ```

4. 死锁

   - 两个或两个以上的进程在执行过程中，因为争夺资源而造成一种互相等待的现象，如果没有外力干涉，他们无法执行下去

   - ```java
     public class DeadLock {
         public static void main(String[] args) {
             Object a = new Object();
             Object b = new Object();
             new Thread(() -> {
                 synchronized(a) {
                     System.out.println(Thread.currentThread().getName() + "获取到锁a");
                     try {
                         TimeUnit.SECONDS.sleep(1);
                     } catch (InterruptedException e) {
                         e.printStackTrace();
                     }
                     synchronized(b) {
                         System.out.println(Thread.currentThread().getName() + "获取到锁b");
                     }
                 }
             }, "testA").start();
             new Thread(() -> {
                 synchronized(b) {
                     System.out.println(Thread.currentThread().getName() + "获取到锁b");
                     try {
                         TimeUnit.SECONDS.sleep(1);
                     } catch (InterruptedException e) {
                         e.printStackTrace();
                     }
                     synchronized(a) {
                         System.out.println(Thread.currentThread().getName() + "获取到锁a");
                     }
                 }
             }, "testB").start();
         }
     }
     ```

   - 验证是否为死锁

     jps -l

     jstack 进程号

     ![](F:\截图\屏幕截图 2022-06-24 205935.png)

# 第五章 Callable接口

1. Callable接口特点

   - 为了实现Runnable，需要实现不返回任何内容的run())方法，而对于Callable，需要实现在完成时返回结果的call()方法。
   - call()方法可以引发异常，而run()则不能。
   - 为实现Callable而必须重写call方法

2. 创建线程

   ```java
   public class ThreadDemo {
       public static void main(String[] args) throws ExecutionException, InterruptedException {
           FutureTask<Integer> futureTask = new FutureTask<>(() -> {
               System.out.println(Thread.currentThread().getName() + "进入Callable");
               return 1024;
           });
           new Thread(futureTask, "test1").start();
           while (!futureTask.isDone()) {
               System.out.println("请稍等...");
           }
           System.out.println(futureTask.get());
       }
   }
   ```

# 第六章 辅助类

## 一，CountDownLatch（减少计数）

CountDownLatch类可以设置一个计数器，然后通过countDown方法来进行减1的操作，使用await方法等待计数器不大于0，然后继续执行await方法之后的语句。

- CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞
- 其它线程调用countDown方法会将计数器减1（调用countDown方法的线程不会阻塞)
- 当计数器的值变为0时，因await方法阻塞的线程会被唤醒，继续执行

```java
public class CountDownLatchDemo {
    //模拟6个同学依次离开教室，然后锁门
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "号同学离开教室");
                countDownLatch.countDown();
            }, String.valueOf(i + 1)).start();
        }
        countDownLatch.await();
        System.out.println("锁门");
    }
}
```

## 二，CyclicBarrier（循环栅栏）

![](F:\截图\屏幕截图 2022-06-26 163607.png)

```java
public class CyclicBarrierDemo {
    //集齐七龙珠召唤神龙
    public static final Integer NUMBER = 7;
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUMBER, () -> {
            System.out.println("集齐七龙珠召唤神龙");
        });
        for (int i = 0; i < 7; i++) {
            new Thread(() -> {
                try {
                    System.out.println("收集到" + Thread.currentThread().getName() + "号龙珠");
                    cyclicBarrier.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i + 1)).start();
        }
    }
}
```

## 三，Semaphore（信号灯）

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        //6辆汽车，抢占3个车位
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "号车抢到了车位");
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                    System.out.println(Thread.currentThread().getName() + "号车离开了车位");
                }
            }, String.valueOf(i + 1)).start();
        }
    }
}
```

# 第七章 读写锁

1. 案例

   ```java
   public class ReadWriteLockDemo {
       public static void main(String[] args) {
           Cache cache = new Cache();
           for (int i = 0; i < 5; i++) {
               final int num = i;
               new Thread(() -> {
                   cache.put(num + "", num);
               }, String.valueOf(i + 1)).start();
           }
           for (int i = 0; i < 5; i++) {
               final int num = i;
               new Thread(() -> {
                   cache.get(num + "");
               }, String.valueOf(i + 1)).start();
           }
       }
   }
   class Cache {
       private volatile Map<String, Object> map = new HashMap<>();
   
       private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
   
       //存数据
       public void put(String key, Object value) {
           try {
               readWriteLock.writeLock().lock();
               System.out.println(Thread.currentThread().getName() + "正在写数据");
               TimeUnit.MICROSECONDS.sleep(300);
               map.put(key, value);
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               System.out.println(Thread.currentThread().getName() + "写完了");
               readWriteLock.writeLock().unlock();
           }
       }
   
       //读数据
       public Object get(String key) {
           Object result = null;
           try {
               readWriteLock.readLock().lock();
               System.out.println(Thread.currentThread().getName() + "正在读数据");
               TimeUnit.MICROSECONDS.sleep(300);
               result = map.get(key);
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               System.out.println(Thread.currentThread().getName() + "读完了");
               readWriteLock.readLock().unlock();
           }
           return result;
       }
   }
   ```

2. 读写锁的降级

   ```Java
   public class ReadWriteLockDemo2 {
       public static void main(String[] args) {
           ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
           ReentrantReadWriteLock.ReadLock readLock = rwLock.readLock();//读锁
           ReentrantReadWriteLock.WriteLock writeLock = rwLock.writeLock();//写锁
           new Thread(() -> {
               try {
                   writeLock.lock();//获取写锁
                   readLock.lock();//获取读锁
                   System.out.println(Thread.currentThread().getName() + ":正在写入");
                   writeLock.unlock();//释放写锁
                   TimeUnit.SECONDS.sleep(3);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               } finally {
                   System.out.println(Thread.currentThread().getName() + ":写入完成");
                   writeLock.unlock();//释放读锁
               }
           }, "write").start();
           new Thread(() -> {
               try {
                   readLock.lock();
                   System.out.println(Thread.currentThread().getName() + ":正在读取");
               } catch (Exception e) {
                   e.printStackTrace();
               } finally {
                   System.out.println(Thread.currentThread().getName() + ":读取完成");
                   readLock.unlock();
               }
           }, "read").start();
       }
   }
   ```

# 第八章 阻塞队列

1. 概述

   Concurrent包中，BlockingQueue很好的解决了多线程中，如何高效安全“传输”数据的问题。通过这些高效并且线程安全的队列类，为我们快速搭建高质量的多线程程序带来极大的便利。本文详细介绍了BlockingQueue家庭中的所有成员，包括他们各自的功能以及常见使用场景。

   “阻塞队列，顾名思义，首先它是一个队列，通过一个共享的队列，可以使得数据由队列的一端输入，从另外一端输出；

   当队列是空的，从队列中获取元素的操作将会被阻塞，当队列是满的，从队列中添加元素的操作将会被阻塞。

   试图从空的队列中获取元素的线程将会被阻塞，直到其他线程往空的队列插入新的元素试图向已满的队列中添加新元素的线程将会被阻塞，直到其他线程从队列中移除一个或多个元素或者完全清空，使队列变得空闲起来并后续新增

2. 常见的BlockingQueue

   - ArrayBlockingQueue

     基于数组的阻塞队列实现，在ArrayBlocking Queue内部，维护了一个定长数组，以便缓存队列中的数据对象，这是一个常用的阻塞队列，除了一个定长数组外，ArrayBlockingQueue内部还保存着两个整形变量，分别标识着队列的头部和尾部在数组中的位置。

   - LinkedBlockingQueue

     基于链表的阻塞队列，同ArrayListBlockingQueue类似，其内部也维持着个数据缓冲队列（该队列由一个链表构成），当生产者往队列中放入一个数据时，队列会从生产者手中获取数据，并缓存在队列内部，而生产者立即返回：只有当队列缓冲区达到最大值缓存容量时(LinkedBlockingQueue可以通过构造函数指定该值)，才会阻塞生产者队列，直到消费者从队列中消费掉一份数据，生产者线程会被唤醒，反之对于消费者这端的处理也基于同样的原理。而LinkedBlockingQueue之所以能够高效的处理并发数据，还因为其对于生产者端和消费者端分别采用了独立的锁来控制数据同步，这也意味着在高并发的情况下生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能。

   - DelayQueue

     DelayQueue中的元素只有当其指定的延迟时间到了，才能够从队列中获取到该元素。DelayQueue是一个没有大小限制的队列，因此往队列中插入数据的操作（生产者）永远不会被阻塞，而只有获取数据的操作（消费者）才会被阻塞。

   - PriorityBlockingQueue

     基于优先级的阻塞队列（优先级的判断通过构造函数传入的Compator对象来决定)，但需要注意的是PriorityBlockingQueue并不会阻塞数据生产者，而只会在没有可消费的数据时，阻塞数据的消费者。·因此使用的时候要特别注意，生产者生产数据的速度绝对不能快于消费者消费数据的速度，否则时间一长，会最终耗尽所有的可用堆内存空间。在实现PriorityBlockingQuleue时，内部控制线程同步的锁采用的是公平锁。

   - SynchronousQueue

     一种无缓冲的等待队列，类似于无中介的直接交易，有点像原始社会中的生产者和消费者，生产者拿着产品去集市销售给产品的最终消费者，而消费者必须亲自去集市找到所要商品的直接生产者，如果一方没有找到合适的目标，那么对不起，大家都在集市等待。相对于有缓冲的BlockingQueue来说，少了一个中间经销商的环节（缓冲区），如果有经销商，生产者直接把产品批发给经销商，而无需在意经销商最终会将这些产品卖给那些消费者，由于经销商可以库存一部分商品，因此相对于直接交易模式，总体来说采用中间经销商的模式会吞吐量高一些（可以批量买卖），但另一方面，又因为经销商的引入，使得产品从生产者到消费者中间增加了额外的交易环节，单个产品的及时响应性能可能会降低

   - LinkedTransferQueue

     LinkedTransferQueue是一个由链表结构组成的无界阻塞TransferQueue队列。相对于其他阻塞队列，LinkedTransferQueue多了tryTransfer和transfer方法。LinkedTransferQueue采用一种预占模式。意思就是消费者线程取元素时，如果队列不为空，则直接取走数据，若队列为空，那就生成一个节点（节点元素为null）入队，然后消费者线程被等待在这个节点上，后面生产者线程入队时发现有一个元素为null的节点，生产者线程就不入队了，直接就将元素填充到该节点，并唤醒该节点等待的线程，被唤醒的消费者线程取走元素，从调用的方法返回。

3. 核心方法

   | 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时                 |
   | -------- | --------- | -------- | ------ | -------------------- |
   | 插入     | add(e)    | offer(e) | put(e) | offer(e, time, unit) |
   | 排除     | remove()  | poll()   | take() | poll(time, unit)     |
   | 检查     | element() | peek()   | 不可用 | 不可用               |

   ![](F:\截图\屏幕截图 2022-06-28 171600.png)

# 第九章 线程池

1. 简介

   线程池（thread pool)：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。

2. 线程池的优势

   线程池的工作只要控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其他线程执行完毕，在从队列中取出任务来执行

   - 降低资源消耗：通过重复利用已创建的线程降低线程创建和销毁造成的消耗
   - 提高响应速度：当任务到达时，任务可以不需要等待线程创建就能立即执行
   - 提高线程的可管理性：线程是稀缺资源，如果无限制的创建不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控
   - java中的线程池是通过Executor框架实现的，该框架中使用到了Executor，Executors，ExecutorService，ThreadPoolExecutor这几个类

3. 线程池的使用

   ```java
   public class ThreadPoolDemo {
       public static void main(String[] args) {
           //ExecutorService threadPool = Executors.newFixedThreadPool(5);//一池多线程
           //ExecutorService threadPool = Executors.newSingleThreadExecutor();//一池一线程
           ExecutorService threadPool = Executors.newCachedThreadPool();//可扩容线程池
           for (int i = 0; i < 10; i++) {
               threadPool.execute(() -> {
                   System.out.println(Thread.currentThread().getName());
               });
           }
           threadPool.shutdown();
       }
   }
   ```

4. 
