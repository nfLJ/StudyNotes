# Java线程池

## 简介

> 线程池：通俗的讲，线程池就是一个管理线程的池子。

## 使用线程池的好处

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

## 线程池的创建

线程池可以通过 `ThreadPoolExector` 来创建，它有四个构造函数

![](https://gitee.com/nfLJ/Pic/raw/master/JavaSE/20201231104810.png)



```java
 public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

可以看到，其需要如下几个参数：

- **corePoolSize（必需）：** 线程池的核心线程数
- **maximumPoolSize（必需）：** 线程池的最大线程数。当活跃线程数达到该数值后，新任务将会阻塞
- **keepAliveTime（必需）：** 当线程数大于核心线程数时，多余的空闲线程存活的最长时间
- **unit（必需）：**指定 keepAliveTime 参数的时间单位
- **workQueue（必需）：**任务队列，用来储存等待执行任务的队列
- **threadFactory（可选）：** 线程工厂，用来创建线程，一般默认即可
- **handler（可选）：**  拒绝策略，当达到最大线程数时需要执行的饱和策略

下面这张图可以加深你对线程池中各个参数的相互关系的理解![线程池各个参数之间的关系](https://gitee.com/nfLJ/Pic/raw/master/JavaSE/20201231110153.png)

## 线程池的工作原理

线程池执行流程，即对应 `execute()`方法：

![](https://gitee.com/nfLJ/Pic/raw/master/JavaSE/20201231110444.png)

- 提交一个任务，线程池里存活的核心线程数小于 **corePoolSize** 时，线程池会创建一个核心线程去处理提交的任务。
- 如果线程池核心线程数已满，即线程数已经等于 **corePoolSize**，一个新提交的任务，会被放进任务队列**workQueue** 排队等待执行。
- 当线程池里面存活的线程数已经等于 **corePoolSize** 了,并且任务队列 **workQueue** 也满，判断线程数是否达到**maximumPoolSize**，即最大线程数是否已满，如果没到达，创建一个非核心线程执行提交的任务。
- 如果当前的线程数达到了 **maximumPoolSize**，还有新的任务过来的话，直接采用拒绝策略处理。

## 线程池的参数

### 任务队列（workQueue）

任务队列是基于阻塞队列实现的，即采用生产者消费者模式，在 Java 中需要实现 **BlockingQueue** 接口。但 Java 已经为我们提供了 7 种阻塞队列的实现：

1. **ArrayBlockingQueue**：一个由数组结构组成的有界阻塞队列（数组结构可配合指针实现一个环形队列）。
2. **LinkedBlockingQueue**： 一个由链表结构组成的有界阻塞队列，在未指明容量时，容量默认为**Integer.MAX_VALUE**。
3. **PriorityBlockingQueue**： 一个支持优先级排序的无界阻塞队列，对元素没有要求，可以实现**Comparable**接口也可以提供Comparator来对队列中的元素进行比较。跟时间没有任何关系，仅仅是按照优先级取任务。
4. **DelayQueue**：类似于 **PriorityBlockingQueue**，是二叉堆实现的无界优先级阻塞队列。要求元素都实现**Delayed**接口，通过执行时延从队列中提取任务，时间没到任务取不出来。
5. **SynchronousQueue**： 一个不存储元素的阻塞队列，消费者线程调用take()方法的时候就会发生阻塞，直到有一个生产者线程生产了一个元素，消费者线程就可以拿到这个元素并返回；生产者线程调用put()方法的时候也会发生阻塞，直到有一个消费者线程消费了一个元素，生产者才会返回。
6. **LinkedBlockingDeque**： 使用双向队列实现的有界双端阻塞队列。双端意味着可以像普通队列一样FIFO（先进先出），也可以像栈一样FILO（先进后出）。
7. **LinkedTransferQueue**： 它是 **ConcurrentLinkedQueue**、**LinkedBlockingQueue **和 **SynchronousQueue** 的结合体，但是把它用在 ThreadPoolExecutor 中，和 **LinkedBlockingQueue** 行为一致，但是是无界的阻塞队列。

> 注意有界队列和无界队列的区别：如果使用有界队列，当队列饱和时并超过最大线程数时就会执行拒绝策略；而如果使用无界队列，因为任务队列永远都可以添加任务，所以设置 maximumPoolSize 没有任何意义。

### 线程工厂（threadFactory）

线程工厂指定创建线程的方式，需要实现 **ThreadFactory** 接口，并实现 **newThread(Runnable r)** 方法。该参数可以不用指定，Executors 框架已经为我们实现了一个默认的线程工厂：

```java
/**
 * The default thread factory
 */
static class DefaultThreadFactory implements ThreadFactory {
    private static final AtomicInteger poolNumber = new AtomicInteger(1);
    private final ThreadGroup group;
    private final AtomicInteger threadNumber = new AtomicInteger(1);
    private final String namePrefix;

    DefaultThreadFactory() {
        SecurityManager s = System.getSecurityManager();
        group = (s != null) ? s.getThreadGroup() :
        Thread.currentThread().getThreadGroup();
        namePrefix = "pool-" +
            poolNumber.getAndIncrement() +
            "-thread-";
    }

    public Thread newThread(Runnable r) {
        Thread t = new Thread(group, r,
                              namePrefix + threadNumber.getAndIncrement(),
                              0);
        if (t.isDaemon())
            t.setDaemon(false);
        if (t.getPriority() != Thread.NORM_PRIORITY)
            t.setPriority(Thread.NORM_PRIORITY);
        return t;
    }
}
```

### 拒绝策略（handler）

当线程池的线程数达到最大线程数时，需要执行拒绝策略。拒绝策略需要实现 `RejectedExecutionHandler` 接口，并实现 `rejectedExecution(Runnable r, ThreadPoolExecutor executor)`方法。 Executors 框架已经为我们实现了4种拒绝策略：

1. **AbortPolicy（默认）**：丢弃任务并抛出 **RejectedExecutionException** 异常。
2. **CallerRunsPolicy**：由调用线程处理该任务。
3. **DiscardPolicy**：丢弃任务，但是不抛出异常。可以配合这种模式进行自定义的处理方式。
4. **DiscardOldestPolicy**：丢弃队列最早的未处理任务，然后重新尝试执行任务。

## 功能线程池

### 定长线程池（FixedThreadPool）

创建源码：

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}

public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>(),
                                  threadFactory);
}
```

- **特点**：只有核心线程，线程数量固定，执行完立即回收，任务队列为链表结构的有界队列。
- **应用场景**：控制线程最大并发数。

### 定时线程池（ScheduledThreadPool ）

创建源码：

```java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}

public static ScheduledExecutorService newScheduledThreadPool(
            int corePoolSize, 
    		ThreadFactory threadFactory) {
    return new ScheduledThreadPoolExecutor(corePoolSize, threadFactory);
}
```

- **特点**：核心线程数量固定，非核心线程数量无限，执行完闲置10ms后回收，任务队列为延时阻塞队列。
- **应用场景**：执行定时或周期性的任务。

### 可缓存线程池（CachedThreadPool）

创建源码：

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
public static ExecutorService newCachedThreadPool(ThreadFactory threadFactory) {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>(),
                                  threadFactory);
}
```

- **特点**：无核心线程，非核心线程数量无限，执行完闲置60s后回收，任务队列为不存储元素的阻塞队列。
- **应用场景**：执行大量、耗时少的任务。

### 单线程化线程池（SingleThreadExecutor）

创建源码：

```java
 public static ExecutorService newSingleThreadExecutor() {
     return new FinalizableDelegatedExecutorService
         (new ThreadPoolExecutor(1, 1,
                                 0L, TimeUnit.MILLISECONDS,
                                 new LinkedBlockingQueue<Runnable>()));
 }

public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>(),
                                threadFactory));
}
```

- **特点**：只有1个核心线程，无非核心线程，执行完立即回收，任务队列为链表结构的有界队列。
- **应用场景**：不适合并发但可能引起IO阻塞性及影响UI线程响应的操作，如数据库操作、文件操作等。

### 对比

![](https://gitee.com/nfLJ/Pic/raw/master/JavaSE/20201231114017.png)

### 小结

Executors的4个功能线程池虽然方便，但现在已经不建议使用了，而是建议直接通过使用ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

其实Executors的4个功能线程有如下弊端：

- **FixedThreadPool**和**SingleThreadExecutor**：主要问题是堆积的请求处理队列均采用**LinkedBlockingQueue**，可能会耗费非常大的内存，甚至OOM。
- **CachedThreadPool** 和 **ScheduledThreadPool**：主要问题是线程数最大数是 **Integer.MAX_VALUE**，可能会创建数量非常多的线程，甚至OOM。

## 参考

[Java多线程：彻底搞懂线程池](https://blog.csdn.net/u013541140/article/details/95225769?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522160937857016780299054020%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D&request_id=160937857016780299054020&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-95225769.first_rank_v2_pc_rank_v29&utm_term=%E7%BA%BF%E7%A8%8B%E6%B1%A0)