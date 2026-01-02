# 内部-Java ParallelStream 踩坑记录


## 一、踩坑经历
在使用JDK8中的stream大家肯定不陌生，而且在处理集合（list、map）的时候用起来很爽，更爽的是可以通过 ParallelStream 进行并行操作，充分压榨cpu的性能。同时也可以简化多线程代码的书写，用起来是真的香。但是很多时候印证了一句话，当你没有深刻理解一个框架的时候，用的时候很爽。踩坑的时候就更爽。具体的代码简化如下：
```java
    //分批查询信息
    List<Long> activityIds = activityIds.stream().distinct().collect(Collectors.toList());
    List<List<Long>> idPartitions = ListUtils.partition(activityIds, 10);
    AtomicLong atomicLong = new AtomicLong(System.currentTimeMillis());
    partition.parallelStream().forEach(ids -> {
        AtomicLong start = new AtomicLong(System.currentTimeMillis());
        //查询二方接口
        //hsf invoke
        service.invoke(...);
        traceLog("query size" + activityIds, start);
    });
```
上述代码中实际的流程是，活动id批次请求二方接口，然后获取数据进行拼装。看似没有什么问题，但在一段时间内，频频出现接口超时的问题，各种排查，开始以为二方接口的时间问题。

后来通过各种定位，发现主要是在idPartitions.size大于200的时候，会出现超时问题，主要原因是由于 `这种 parallelStream 是整个java进程共用一个 ForkJoinPool`, 换句话说项目中很多 parallelStream 的地方会共用这个线程池，包括gc线程。所以一旦代码中用的地方多了。在同时运行的时候，自然会出现超时等待/超时问题。所以有必要对相关知识做一个整体的梳理，避免后续继续犯错。



## 二、预备知识1：意义关于并行和并发的概念

首先,在多线程并发编程中，有两个绕不开的术语并行和并发。很多同学可能对两者的区别不是很清楚，这里简单解释一下。

- `并行` 指两个事件在同一个时间点同时发生，多核CPU提供了硬件上在同一个时间点同时执行两个指令的可能性。
- `并发` 指两个事件在同一个时间段一起发生，硬件上单核cpu如果在同时运行多个任务（事件），就会在不同的时间点执行。不同任务的指令，但是在同一个时间点只能执行一个任务的一条指令。

综上所述，并行+并发可以更好的压榨cpu的性能。


##  三、预备知识2：线程池概念

线程池引出:

随着服务器硬件cpu核数越来越多，利用多线程技术以压榨cpu的性能，更加高效的运行程序。所以，多线程技术运用的越来越广泛。但是由于线程的创建和销毁都涉及到系统调用。比较消耗系统资源。所以就引入了线程池技术。避免频繁的线程创建和销毁。

javase提供了一个工具，可以为我们创建一个线程池,其本质就是new一个 ThreadPoolExecutor 对象。进入源码可以看到，就是new 各种 ThreadPoolExecutor, 且很多时候 Executor 提供的方法会有很多局限和不可控因素。

所以一般来说，我们可以自己来创建线程池。

线程池原理：
```java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler){...}
```

- corePoolSize ：线程池的核心线程数，说白了就是即便是线程池里没有任何任务，也会有 corePoolSize 个线程在候着等待任务；
- maximumPoolSize ：最大线程数，不管你提交多少任务。线程池里最多工作线程数就是 maximumPoolSize；
- keepAliveTime ：线程的存活时间。当线程池里的线程数大于 corePoolSize 时，如果等了keepAliveTime 时长还没有任务可执行，则线程退出。
- unit ：这个用来指定 keepAliveTime 的单位。比如秒 TimeUnit.SECONDES.
- workQueue : 一个阻塞队列，提交的任务将会被放到这个队列里。
- threadFactory ：线程工厂，用来创建线程，主要是为了给线程起名字，默认工厂的线程名字：pool-1-thread-3.
- handler : 拒绝策略，当线程池里，线程被耗尽。且队列也满了的时候会调用。

 具体的执行流程图如下：

{{< figure src="/image/tech/java/ThreadPoolExecutor.png" title="" >}}

{{< figure src="/image/tech/java/ThreadPoolExecutor_2.png" title="" >}}

## 四、预备知识3 JDK自带的forkjoin线程池

可以通过 parallelStream 的源码发现 parallel Stream 底层是将任务进行了切分，最终将任物传递给了jdk8自带的 `"全局"`ForkJoinPool 线程池。在 Fork-Join 中。比如一个拥有4个线程的ForkJoinPool线程池，有一个任务队列，一个大的任务切分出的子任务会提交到线程池的任务队列中，4个线程从任务队列中获取任务执行。哪个线程执行的任务快，哪个线程执行的任务就多。 只有队列中没有任务线程是空闲的。这就是`工作窃取`。

可以通过下图更好的理解，这种分而治之的思想。

{{< figure src="/image/tech/java/forkJoin.png" title="" >}}

关键问题:

> 如果直接使用 parallelStream().foreach()，会默认使用全局的 ForkJoinPool，而这样就会导致当前程序很多地方共用同一个线程池，包括GC相关操作在内。所以一旦任务队列中满了之后，就会出现阻塞的情况，导致整个程序的只要当前使用 ForkJoinPool 地方都会出现问题！


## 五、如何合理的使用parallelStream

结合上述的问题，需要注意几点：

- `parallelStream()中 foreach() 操作尽量保证是线程安全的`

  很多人在用惯了流式处理之后，很多for循环，都会直接使用流式foreach()，实际上这样不一定是合理的。如果只是简单的for循环，确实没有必要使用流式处理，因为流底层，封装了很多流式处理的复杂逻辑。从性能上来讲不占优。

- `parallelStream()中 foreach() 不要直接使用默认的线程池`
```java
    ForkJoinPool customerPool = new ForkJoinPool(n);
    customerPool.submit(() -> {
        lists.parallelStream().forEach(ids -> {
        ...
        });
    }).get();
```
- `parallelStream() 使用的时候尽量避免耗时操作`
 
   如果遇到耗时的操作。或者大量IO的操作，或者有线程sleep的操作，一定要避免使用并行流。


## 参考
[https://juejin.cn/post/6996586542932164645](https://juejin.cn/post/6996586542932164645)

