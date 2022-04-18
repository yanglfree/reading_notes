## 第五部分 并发

## 并发基础知识
### 理解synchronized
* synchronized用于类的实例方法、静态方法和代码块；
* 多个线程是可以执行同一个synchronized实例方法的，只要它们访问的实例对象不同；
* synchronized实例方法实际保护的是同一个对象的方法调用，确保同时只有一个线程执行；
* synchronized实例方法保护的是当前的实例对象，即this，this对象有一个锁，一个等待队列，锁只能被一个线程持有，其他试图获得同样锁的线程需要等待。
* 一般在保护变量时，需要在所有访问该变量的方法上加上synchronized；
* synchronized同步的可以是任意对象

#### synchronized的使用注意点
* 可重入性 通过记录锁的持有线程和持有数量实现
* 内存可见性 保证数据及时更新到主内存，也可以用volatile修饰变量
* 死锁 尽量避免还持有一个锁的同时去申请另一个锁，确实需要，所有代码按照相同的顺序去申请锁

#### 同步容器及注意事项
同步容器的使用有几个注意点：
* 复合操作 先检查后更新
* 伪同步 同步对象变了，同步使用的不是相同的锁
* 迭代 需要在同步的时候给整个容器对象加锁
* 并发容器 CopyOnWriteArrayList、ConcurrentHashMap、ConcurrentLinkedQueue、ConcurrentSkipListSet

### 线程的基本协作机制
协作的场景：
* 生产者/消费者协作模式
* 同时开始 多个线程同时开始
* 等待结束
* 异步结果
* 集合点

#### wait/notify
除了锁的等待队列，每个对象还有另一个等待队列，表示条件队列，该队列用于线程间的协作。

wait/notify只能在synchronized代码块内被调用，否则抛出异常

在synchronize方法内调用wait时，线程会释放对象锁；
在设计多线程协作时，明确协作的共享变量和条件是什么，这是协作的核心。
只有一个条件等待队列，是wait/notify的局限性


#### 线程的中断
场景：
* 在死循环的运行模式中，需要停止程序，比如生产/消费者模式
* 图像界面中，比如下载的时候，需要停止下载
* 任务超时的时候，希望中断任务
* 多个渠道买火车票，一个渠道抢到了，通知其他渠道停止

##### 取消/关闭的机制
Java中停止一个线程的主要机制是中断，中断不是强迫中止一个线程，是一种协助机制，是给线程传递一个取消的信号，**但是由线程来决定如何以及何时退出**。

##### 中断的方法
* isInterrupted
* interrupted
* interrupt

##### 线程对中断的反应
* RUNNABLE 没有执行IO操作时，设置interrupt()只会设置中断标志位，不会有其他作用
* WAITING/TIMED_WAITING  线程抛出InterruptedException异常，抛出异常后，**中断标志会被清空，而不是被设置**
* BLOCKED 设置线程的中断标志位，线程依然处于BLOCKED状态，interrupt()不能使一个等待锁的线程真正的“中断”
* NEW/TERMINATED 没有任务作用，中断标志位也不会被设置

##### 正确的取消/关闭线程
* 如果不明白线程在做什么，不能贸然的调用interrupt中断线程
* 对于以线程提供服务的模块，应该封装取消/关闭线程的操作，提供单独的取消关闭方法给调用者，不让外部调用者直接调用interrupt方法


### 并发包的基石
#### 原子变量和CAS
对于使用synchronized来对变量进行原子保证的场景来说，使用synchronized成本太高，获取锁，释放锁，可能会有的等待，切换上下文，这些都需要成本。
使用原子变量是更好的选择

Java并发包中的原子变量有以下几种类型
* AtomicBoolean
* AtomicInteger
* AtomicLong
* AtomicReference

原子变量：包含一些以原子方式实现组合操作的方法

synchronized： 悲观锁
原子变量： 乐观锁

#### ABA问题



#### 显式锁

Lock，实现类ReentrantLock
读写锁，实现类ReentrantReadWriteLock

相比于synchronized，显式锁支持以非阻塞的方式获取锁、可以响应中断，可以限时。


