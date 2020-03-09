# Synchronized 内置锁

- 锁重入：从互斥锁的设计上讲，当一个线程试图操作由其他线程持有对象锁的临界资源时，会进入阻塞状态。当一个线程再次请求自己持有对象锁的临界资源时，这种情况就被称为锁重入。

| 锁类型 | 实现 |
| :---- | :--- |
| 对象锁 | `monitorenter`与`monitorexit`字节码指令。 |
| 类锁   | `ACC_SYNCHRONIZED`标识符。|

# Synchronized 内置锁优化

在早期的 Java 版本中，Synchronized 内置锁属于重量级锁，依赖于操作系统 Mutex Lock 实现，开销较大。

在 Java 6 版本后，JVM 团队对 Synchronized 内置锁进行了诸多优化，性能上有了很大的提升。

# Java 对象头


> 图：Java 对象头

## 偏向锁

引入偏向锁的主要原因是：经过研究发现，在多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，因此为了减少同一线程获取锁（获锁操作较为耗时）的代价而引入偏向锁。

偏向锁的思想是：如果一个线程获得了锁，那么锁进入偏向锁模式，对象头 Mark Word 变更为偏向锁结构。当同一线程再次请求锁时，无需再做任何同步操作，直接获锁，这样便省去了大量锁申请的操作，提高了性能。对于没有锁竞争的场合，偏向锁有着很好的优化效果，因为极有可能是连续多次由同一线程申请相同的锁。

但是对于锁竞争激烈的场合，偏向锁就失效了，因为这样场合极有可能每次申请锁的线程都是不相同的，在这种场合下不应该使用偏向锁，否则会得不偿失，需要注意的是，偏向锁失败后，并不会立即膨胀为重量级锁，而是先升级为轻量级锁。

## 轻量级锁

...

## 自旋锁

轻量级锁失败后，JVM 虚拟机为了避免线程在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。

线程的阻塞与唤醒都需要操作系统由用户态切换到核心态，线程频繁阻塞与唤醒对于 CPU 而言是一件负担很重的工作，给系统并发性能带来很大压力。在很多情况下，对象锁的锁状态只会持续很短的一段时间，为了这段很短的时间频繁阻塞唤醒线程是不值得的，因此引入了自旋锁。

所谓自旋锁，就是让线程忙等待（空循环）一段时间，不会被立即挂起，看持有锁的线程是否会很快释放锁。自旋等待不能替代阻塞，虽然自旋可以避免线程切换带来的开销，但是自旋操作占用了处理器时间。如果持有锁的线程很快释放了锁，那么自旋效率就非常好，反之，自旋线程就白白浪费了处理器资源，带来了性能上的浪费。自旋时间（自旋次数）必须要有一个限度，如果线程自旋时间超过了定义的时间仍然没有获取到锁，线程应该被挂起。

自旋锁在 JDK 1.4.2 中被引入，默认关闭，但是可以使用启动参数`-XX:+UseSpinning`开启
 
在 JDK 1.6 中自旋锁默认开启，同时自旋的默认次数为`10`次，可以通过启动参数`-XX:PreBlockSpin`来调整。

JDK 1.6 还引入了自适应自旋锁，所谓自适应意味着自旋的次数不再固定，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定。某线程如果自旋获锁成功了，那么下次自旋的次数会更增加，因为 JVM 虚拟机认为既然上次自旋获锁成功了，那么这次自旋也很有可能会再次成功，所以 JVM 虚拟机就允许自旋等待次数更多。反之，如果对于某个锁很少有自旋操作能成功获锁，那么在之后请求获取这个锁的线程自旋次数会减少甚至省略掉自旋过程，以免浪费处理器资源。

如果自旋之后依然没有获取到锁，也就只能升级为重量级锁了。








<!-- EOF -->