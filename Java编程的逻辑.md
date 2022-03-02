## 第五部分 并发

### 并发基础知识
#### 理解synchronized
* synchronized用于类的实例方法、静态方法和代码块；
* 多个线程是可以执行同一个synchronized实例方法的，只要它们访问的实例对象不同；
* synchronized实例方法实际保护的是同一个对象的方法调用，确保同时只有一个线程执行；
* synchronized实例方法保护的是当前的实例对象，即this，this对象有一个锁，一个等待队列，锁只能被一个线程持有，其他试图获得同样锁的线程需要等待。
* 一般在保护变量时，需要在所有访问该变量的方法上加上synchronized；
* synchronized同步的可以是任意对象

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


