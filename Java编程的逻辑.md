## 第五部分 并发
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

synchronized： 乐观锁
原子变量： 悲观锁

#### 显式锁

Lock，实现类ReentrantLock
读写锁，实现类ReentrantReadWriteLock

相比于synchronized，显式锁支持以非阻塞的方式获取锁、可以响应中断，可以限时。
