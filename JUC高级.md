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

