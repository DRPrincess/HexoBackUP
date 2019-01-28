

# 问题

- Android线程为什么要分 UI线程和子线程？
- Android 子线程中为什么不能更新UI？
- 为什么有些特殊情况出现？

# Java 中的线程

# 线程的实现

线程是比进程更轻量级的调度执行单位，线程的引入，可以把一个进程的资源分配和执行调度分开，各个线程既可以共享进程资源(内存地址/文件IO等)，又可以独立调度，线程是CPU调度的基本单位。

操作系统的线程实现一般分为3种

- 内核线程实现
  直接操作系统内核来完成线程切换，线程切换操纵调度器去映射到各个处理器上。需要在用户态和内核态中切换，调用代价高。
- 用户线程实现
  只要不是内核线程，就都是用户线程。操作系统把处理器资源分配到进程中，需要用户自己处理线程同步和阻塞

- 轻量级进程+用户线程实现
  线程建立到内部空间中，线程切换廉价。

目前的Java虚拟机使用的是第三种。


# Java 线程调度

线程调度是指系统为线程分配处理器使用权的过程，主要的调度方式

- 协同式调度
- 抢占式调度

目前Java 使用的是抢占式调度。Java的线程调度式有系统自动完成的，但是有提供一些关于线程分配资源的方法，例如设置线程优先级，单丝不建议使用。原因是因为 Java的线程优先级共设置了10种，优先级越高的越容易被系统选中，但是很多操作系统提供的线程优先级并不一致，例如 Windows 就只有7种。

线程的执行顺序和分配是有Java 虚拟机的线程调度器决定的。可能同一个机器和同一个程序在不同的时间，执行的顺序也是不一样的。


# Java 中线程状态

线程是独立的线程，每个Java程序都会启动一个主线程，将main()方法放在他自己执行的空间的最开始处。Java虚拟机会负责启动主线程的活动，程序员负责启动自己建立的线程。

每个线程都有自己的空间。

系统中存在大量线程时，系统会通过时间片轮转的方式调度每个线程，因此线程不能做到决定的并行，除非线程数量小于或者等于CPU的核心数。

线程的状态
- 新建
创建后还没被启动的线程状态
- 运行
这种状态的线程有可能正在执行，也有可能正在等待CPU为他分配时间

- 无限期等待
CPU不会主动分配时间，需要等待显式唤醒
    - Object.wait();
    - Thread.join();
    - LockSupport.park();

- 限期等待
CPU不会主动分配时间，但是一定时间后，系统会自动唤醒。
    - Thread.sleep(long time);
    - Object.wait(long time);
    - Thread.join(long time);
    - LockSupport.parkNanos();
    - LockSupport.parkUntil();
- 阻塞
 等待获取到一个排他锁，一般在进入代码同步区域中，线程将进入这种状态。

- 结束
 已经中止的线程状态





## Java 中内存模型

计算机的硬件存储设备 << 处理器的运算速度,所以计算机在两者之间加入出现了高速缓存，作为内存和处理器之间的缓冲

高速缓存：将运算需要使用的数据复制到缓存中，快速运算，运算结束再从缓存同步会内存中，这样处理器就不用等待缓慢的内存读写了。

Java 虚拟机也提供这么一种内存模型来屏蔽掉各种硬件和操作系统的内存访问差距，以实现各种平台上都能达到一致的内存访问效果。

Java内存的主要目标是定义程序中各个变量的访问规则，即在虚拟机中将变量存储到内存和从内存中取出变量这样的底层细节。

变量：实例字段/静态字段和构成数组对象的元素，不包含局部变量和方法参数，因为后者是线程私有的。


- 所有的变量都存储在主内存。
- 每个线程都还有自己的工作内存，工作内存中保存被该线程用到的主内存副本拷贝。
- 各个线程的工作内存只能独立访问，不能交互访问。
- 每个线程对变量的操作只能通过工作内存修改，不能直接对主线程进行操作。


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/9ce2fe4b51a09effdd4ec32775625af7.png)

程序中主要访问读写的是工作内存。


## 内存交互操作


内存和工作内存之间的具体的交互协议

- lock
- unlock
- read
- load
- use
- assign
- store
- write


## 并发问题


数目>=2的线程存取单一对象的数据时，可能会影响数据的损毁。

一个关于自增的例子：

```

public class TestSync implements Runnable{

    private int balance;

    @Override
    public void run() {

        for (int i = 0; i <5 ; i++) {
            increment();
            System.out.println("currentThread = "+Thread.currentThread().getName()+"  balance = "+balance);
        }
    }

    public  void  increment(){
        int i = balance;
        balance = i+1;
    }
}


```


```

public static void main(String[] args) {
       TestSync job = new TestSync();
       Thread a = new Thread(job,"A");
       Thread b = new Thread(job,"B");
       a.start();
       b.start();
}


```


运行多次是不同的结果：


例子1：

```
currentThread = B  balance = 2
currentThread = A  balance = 2
currentThread = B  balance = 3
currentThread = A  balance = 4
currentThread = A  balance = 6
currentThread = A  balance = 7
currentThread = B  balance = 5
currentThread = A  balance = 8
currentThread = B  balance = 9
currentThread = B  balance = 10

```

例子2:
```

currentThread = A  balance = 1
currentThread = A  balance = 2
currentThread = B  balance = 3
currentThread = B  balance = 5
currentThread = B  balance = 6
currentThread = B  balance = 7
currentThread = B  balance = 8
currentThread = A  balance = 4
currentThread = A  balance = 9
currentThread = A  balance = 10

```

线程越多出现的问题的概率也就越多


原因分析：

currentThread = B  balance = 2
currentThread = A  balance = 2

public  void  increment(){
    int i = balance;
    balance = i+1;
}

A进入increment() 方法，资源被抢占；
B进入increment() 方法，完成int i = balance 和 i+1 此时 i +1 = 1 但是还没有赋值;
A重新进入方法，完成动作，i+1= 2;
B重新进入方法，完成动作，i+1= 2;



## 并发的特征

- 原子性
- 可见性
- 有序性



## 如何检查并发隐患

先行发生原则

- 程序次序规则
- 管程锁定规则
- volatile 变量规则
- 线程启动规则
- 线程终止规则
- 线程中断规则
- 对象终结规则
- 传递性规则

通过各类规则判断，如果仍然没法得出确定的代码执行顺序，那这里面的操作就不是线程安全的。



## 并发下的安全问题

一旦线程进入了方法，我们必须确保在其他线程可以进入该方法之前所有的步骤都会完成。

- synchronized
 控制每次只能被单一的线程存取
 锁不是配在方法上，而是配在对象上。

- voliate
  - 限制每次读取都从主内存刷新最新的值。
  - 每次修改都必须立刻同步到主内存中
  - 不会被指令重排序优化，保证代码执行顺序和程序的顺序相同

- long/double 变量不需要专门声明为 voliate
  64位的数据读写会被分为两个32位的操作进行，为了避免读取到半个数据的情况，商用的Java虚拟机会默认 64位的数据读写是原子操作。

- 不可变变量 final 和 String 没有并发问题。


## synchronized 和  voliate 如何取舍

synchronized 会导致线程阻塞，线程从阻塞状态到运行状态会耗费资源，所以在非必要情况下，不建议使用 synchronized。


voliate 只能保证可见性，所以在符合以下两个场景的情况下，才可以使用 voliate 替代 synchronized 。

- 运算结果并不依赖变量的当前值，或者能够确保只有单一线程的线程能修改变量的值
- 变量不需要和其他的状态变量共同参与不变约束


# Android 中线程使用

主线程中处理和页面相关的事情，子线程执行耗时操作。

因为 Android 的特性，如果主线程中执行耗时操作，那么就会导致程序无法及时响应，因此耗时操作必须要放在子线程中去执行。

除了new Thread 方式， Android 中提供一些封装类来扮演线程的角色，下面是比较常用的：

- AsyncTask(底层用了线程池)

线程+Handler的方式，为了方便开发者在子线程中更新UI

- HandlerService (底层是线程)

一个有消息循环的线程，在它的内部可以使用Handler

Q:Handler 机制

- InentService (底层是线程)

一个服务，系统对他进行了封装，可以更方便的执行后台任务，如果只是后台线程，如果进程中没有活动的四大组件，那么线程的优先级就很低，很容易被系统杀死，如果是后台服务的话，不容易被系统杀死，从而可以尽量保活。

Q：为什么后台服务比后台线程高？

# 关于线程池

线程池的好处：

- 重用线程池中的线程，避免因为线程的创建和销毁所带来的性能开销
- 能有效控制线程中的最大并发数，避免大量线程之间因为互相抢占系统资源而导致的阻塞现象
- 能够对线程进行简单的管理，并提供定时执行以及指定间隔循环执行等功能。

线程池的概念来源于 Java 中的 Executor。

```
public interface Executor {
    void execute(Runnable command);
}

```

ThreadPoolExecutor 是线程次的真正实现，它的构造方法提供了一系列参数来配置线程次

```

public ThreadPoolExecutor(int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime,
                            TimeUnit unit,
                            BlockingQueue<Runnable> workQueue,
                            ThreadFactory threadFactory,
                            RejectedExecutionHandler handler) {

  }

```

- corePoolSize
  线程池的核心线程数，
  默认情况下，核心线程会在线程次中一直存活。
  allowCoreThreadTimeOut 属性设置为 true 的时候，核心线程超时之后也会被停止。
- maximumPoolSize
  线程次中所能容纳的最大线程数 （包括核心线程+非核心线程），当线程数目达到这个最大数，后续的新任务就会被阻塞
- keepAliveTime
  非核心线程闲置时的超时时长，超过这个时长，非核心线程就会被回收。
- unit
  指定 keepAliveTime 参数的时间单位，纳秒/微秒/毫秒/秒/分钟/小时/天等
- workQueue
  线程池中的任务队列
- threadFactory
  线程工厂，为线程池提供创建新线程的功能
- handler
  因为线程任务队列已满或者无法成功执行任务，这个时候会通过这个参数来通知调用者。



# 线程池的分类

Android 中最常见的四类具有不同功能特性的线程池

- FixedThreadPool
  线程数量固定的线程池。只有核心线程，没有超时机制，核心线程会一直存在

  使用场景：执行需要快速响应外界的请求


  ```

  public static ExecutorService newFixedThreadPool(int nThreads) {
         return new ThreadPoolExecutor(nThreads, nThreads,
                                       0L, TimeUnit.MILLISECONDS,
                                       new LinkedBlockingQueue<Runnable>());
     }

  ```

  ```

  ExecutorService fixedThreadPool = Executors.newFixedThreadPool(4);
  fixedThreadPool.execute(mRunnable);

  ```

- CachedThreadPool
  线程数量不固定，最大数是Integer.MAX_VALUE。只有非核心线程。超时机制 60S

  使用场景：执行大量耗时较少的任务


  ```

  public static ExecutorService newCachedThreadPool() {
      return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                    60L, TimeUnit.SECONDS,
                                    new SynchronousQueue<Runnable>());
  }

  ```

  ```

  ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
  cachedThreadPool.execute(mRunnable);

  ```


- ScheduledThreadPool

  核心线程数量固定，非核心线程数没有限制，没有超时机制，非核心线程只要空闲，会立即回收。

  使用场景：执行定时任务和具有固定周期的重复任务

  ```

  public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
         return new ScheduledThreadPoolExecutor(corePoolSize);
     }

  ```

  ```


    ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(4);
    scheduledThreadPool.execute(mRunnable);
    scheduledThreadPool.schedule(mRunnable,4000, TimeUnit.MILLISECONDS);

    //以固定延迟（时间）来执行线程任务，它实际上是不管线程任务的执行时间的，每次都要把任务执行完成后再延迟固定时间后再执行下一次。
    scheduledThreadPool.scheduleWithFixedDelay(mRunnable,1000,500, TimeUnit.MILLISECONDS);

    //以固定频率来执行线程任务，可能设定的固定时间不足以完成线程任务，但是它不管，达到设定的延迟时间了就要执行下一次了。
    scheduledThreadPool.scheduleAtFixedRate(mRunnable,1000,500, TimeUnit.MILLISECONDS);


  ```

- SingleThreadExecutor

  核心线程数为1，没有超时机制。

  使用场景：所有的任务都在同一个线程中按顺序执行，不需要处理线程同步问题。

  ```

  public static ExecutorService newSingleThreadExecutor() {
     return new FinalizableDelegatedExecutorService
         (new ThreadPoolExecutor(1, 1,
                                 0L, TimeUnit.MILLISECONDS,
                                 new LinkedBlockingQueue<Runnable>()));
 }

  ```

  ```

  ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
  singleThreadExecutor.execute(mRunnable);

  ```


# Android 的线程模型

  Android 中的线程可以分为两类：

  - UI线程

  当应用启动，系统会创建一个主线程（main thread）。这个主线程负责向UI组件分发事件（包括绘制事件），也是在这个主线程里，你的应用和Android的UI组件发生交互，所以main thread也叫UI thread也即UI线程。

  - 子线程

  一些比较耗时的工作比如访问网络或者数据库查询，都会阻塞UI线程，导致事件停止分发（包括绘制事件）。对于用户来说，应用看起来像是卡住了，更坏的情况是，如果UI线程blocked的时间太长（大约超过5秒），用户就会看到ANR（application not responding）的对话框。

  这种情况下要用到子线程。


 Android为单线程模型，因为 Android 的 UI 操作不是线程安全。子线程要做的就是

  - 不要阻塞UI线程。
  - 不要在 UI 线程之外访问Android UI toolkit（主要是这两个包中的组件：android.widget and android.view）。


# Android 的子线程真的不能访问UI吗？

请看一个简单的小例子：

```
public class ThreadActivity extends Activity {


    TextView tvThread;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_thread);

        tvThread = findViewById(R.id.tvThread);

        new Thread(new Runnable() {
            @Override
            public void run() {
                tvThread.setText("hello thread");
            }
        }).start();

    }


}

```

子线程中访问并修改了 tvThread UI控件，但是并没有失败，而且还修改成功了。


现在我们先让子线程休眠 1S 再更新UI：

```

new Thread(new Runnable() {
    @Override
    public void run() {
        try{
            Thread.sleep(1000);
        }catch (Exception e){
        }
        tvThread.setText("hello thread");
    }
}).start();

```

还是熟悉的配方，还是熟悉的味道，它崩溃了：

```

android.view.ViewRootImpl$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.

```


- Android是怎么做到 子线程不能访问 UI 的？

ViewRootImpl:

```
void checkThread() {
       if (mThread != Thread.currentThread()) {
           throw new CalledFromWrongThreadException(
                   "Only the original thread that created a view hierarchy can touch its views.");
       }
   }
```

分析源码可以的得出 ViewRootImpl 的创建是在onResume方法之后，

在第一次尝试onCreate方法中创建子线程并访问UI成功，在那个时刻，ViewRootImpl还没有来得及创建，无法检测当前线程是否是UI线程，所以程序没有崩溃。

而之后修改了程序，让线程休眠了100毫秒后再更新UI，程序就崩了。很明显在这100毫秒内ViewRootImpl已经完成了创建，并能执行checkThread方法检查当前访问并更新UI的线程是不是UI线程。


所以“子线程不能访问 UI” 这相当于从另一个角度给Android的UI访问加上象征意义上的锁，来保证同步。

为什么不直接加锁的原因是：

每一次访问UI，Android都会重新绘制 View，如果加锁会降低效率，导致页面反应迟钝。



- 如果子线程想访问 UI 怎么办？

   伟大的 Handler

   - Activity.runOnUiThread(Runnable)
   - View.post(Runnable)
   - View.postDelayed(Runnable, long)
   - 也可以直接创建 Handler


# 总结

1. Java 的线程调度是抢占式调度，所以要在多线程情况下，注意并发保护。
2. 为什么说加锁会降低效率
3. synchronized 会导致线程阻塞，线程从阻塞状态到运行状态会耗费资源，所以保证原子操作的情况下，可以考虑用 voliate 替代
4. Android 子线程中，请避免更新 UI ，不要钻系统的空子。
