# 背景前提

在JDK 5之前Java语言是靠synchronized关键字保证同步的，这会导致有锁

锁机制存在以下问题：

- 在多线程竞争下，加锁、释放锁会导致比较多的上下文切换和调度延时，引起性能问题。
- 一个线程持有锁会导致其它所有需要此锁的线程挂起。
- 如果一个优先级高的线程等待一个优先级低的线程释放锁会导致优先级倒置，引起性能风险。

volatile是不错的机制，但是volatile不能保证原子性。因此对于同步最终还是要回到锁机制上来。

独占锁是一种**悲观锁**，synchronized就是一种独占锁，会导致其它所有需要锁的线程挂起，等待持有锁的线程释放锁。

**synchronized关键字会让没有得到锁资源的线程进入BLOCKED状态，而后在争夺到锁资源后恢复为RUNNABLE状态，这个过程中涉及到操作系统用户模式和内核模式的转换，代价比较高。**

**尽管JAVA 1.6为synchronized做了优化，增加了从偏向锁到轻量级锁再到重量级锁的过过度，但是在最终转变为重量级锁之后，性能仍然比较低。**

而另一个更加有效的锁就是**乐观锁**。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。乐观锁用到的机制就是CAS，Compare and Swap。

从思想上来说，synchronized属于悲观锁，悲观的认为程序中的并发情况严重，所以严防死守，CAS属于乐观锁，乐观地认为程序中的并发情况不那么严重，所以让线程不断去重试更新。

在java中除了上面提到的Atomic系列类，以及Lock系列类夺得底层实现，甚至在JAVA1.6以上版本，synchronized转变为重量级锁之前，也会采用CAS机制。



# CAS是什么？

在计算机科学中，比较和交换（Conmpare And Swap）是用于实现多线程同步的原子指令。 它将内存位置的内容与给定值进行比较，只有在相同的情况下，将该内存位置的内容修改为新的给定值。 这是作为单个原子操作完成的。 原子性保证新值基于最新信息计算; 如果该值在同一时间被另一个线程更新，则写入将失败。 操作结果必须说明是否进行替换; 这可以通过一个简单的布尔响应（这个变体通常称为比较和设置），或通过返回从内存位置读取的值来完成（摘自维基本科）

CAS是英文单词Compare And Swap (Compare And Exchange)/自旋/自旋锁/无锁

因为经常配合循环操作，直到完成为止，所以泛指一类操作

cas(v，a，b)，变量v，期待值a，修改值b

**CAS机制中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。**

**更新一个变量的时候，只有当变量的预期值A和内存地址V当中的实际值相同时，才会将内存地址V对应的值修改为B。**

![image-20220205173852620](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205173852620.png)

**举例**：

1. 在内存地址V当中，存储着值为10的变量。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185414783.png" alt="image-20220205185414783" style="zoom:67%;" />

2. 此时线程1想把变量的值增加1。对线程1来说，旧的预期值A=10，要修改的新值B=11。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185517555.png" alt="image-20220205185517555" style="zoom:67%;" />

3. 在线程1要提交更新之前，另一个线程2抢先一步，把内存地址V中的变量值率先更新成了11。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185548683.png" alt="image-20220205185548683" style="zoom:67%;" />

4. 线程1开始提交更新，首先进行A和地址V的实际值比较，发现A不等于V的实际值，提交失败。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185709446.png" alt="image-20220205185709446" style="zoom:67%;" />

5. 线程1重新获取内存地址V的当前值，并重新计算想要修改的值。此时对线程1来说，A=11，B=12。这个重新尝试的过程被称为自旋。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185832610.png" alt="image-20220205185832610" style="zoom:67%;" />

6. 这一次比较幸运，没有其他线程改变地址V的值。线程1进行比较，发现A和地址V的实际值是相等的。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185912126.png" alt="image-20220205185912126" style="zoom: 67%;" />

7. 线程1进行交换，把地址V的值替换为B，也就是12。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205185940823.png" alt="image-20220205185940823" style="zoom: 67%;" />



# CAS的底层实现（Unsafe）

我们看一下AtomicInteger当中常用的自增方法incrementAndGet：

```java
public final int incrementAndGet() {
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```

```java
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, v + delta));
    return v;
}
```

```java
private volatile int value;
public native int getIntVolatile(Object o, long offset);
```

这段代码是一个无限循环，也就是CAS的自旋，循环体中做了三件事：

1. 获取当前值
2.  当前值+1，计算出目标值
3. 进行CAS操作，如果成功则跳出循环，如果失败则重复上述步骤

这里需要注意的重点是get方法，这个方法的作用是获取变量的当前值。

如何保证获取的当前值是内存中的最新值？很简单，**用volatile关键字来保证**（保证线程间的可见性）。

我们接下来看一下`compareAndSwapInt`方法的实现：

```java
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;

static {
    try {
        valueOffset = unsafe.objectFieldOffset
            (AtomicInteger.class.getDeclaredField("value"));
    } catch (Exception ex) { throw new Error(ex); }
}

private volatile int value;


public final native boolean compareAndSwapInt(Object o, long offset,
                                                  int expected,
                                                  int x);
```

compareAndSet方法的实现很简单，只有一行代码。这里涉及到两个重要的对象，一个是unsafe，一个是valueOffset。

什么是**unsafe**呢？Java语言不像C，C++那样可以直接访问底层操作系统，但是JVM为我们提供了一个后门，这个后门就是unsafe。unsafe为我们提供了硬件级别的原子操作。

至于valueOffset对象，是通过`unsafe.objectFiledOffset`方法得到，所代表的是AtomicInteger对象value成员变量在内存中的偏移量。我们可以简单的把valueOffset理解为value变量的内存地址。

我们上面说过，CAS机制中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。

而unsafe的compareAndSwapInt方法的参数包括了这三个基本元素：valueOffset参数代表了V，expect参数代表了A，update参数代表了B。

正是unsafe的compareAndSwapInt方法保证了Compare和Swap操作之间的原子性操作。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205204802235.png" alt="image-20220205204802235" style="zoom:80%;" />

CAS指令，在硬件级别有一条指令支持`cmpxchg`，`LOCK_IF_MP`表示如果有多个CPU，前面再加`Lock`指令。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205205058043.png" alt="image-20220205205058043" style="zoom:80%;" />

最终实现：

cmpxchg = cas修改变量值

```c++
lock cmpxchg // 指令
```

硬件：

lock指令在执行后面指令的时候锁定一个北桥信号（不采用锁总线的方式）

**注意**：

**cmpxchg不是一个原子操作，需要lock指令加锁**，synchronized和volatile底层均使用要lock指令完成。

**lock指令**表示，当执行这条指令时，其他CPU不能对其修改。



# 原子操作类

所谓原子操作类，指的是`java.util.concurrent.atomic`包下，一系列以Atomic开头的包装类。如AtomicBoolean，AtomicUInteger，AtomicLong。它们分别用于Boolean，Integer，Long类型的原子性操作。

并且在某些情况下，使用AtomicInteger之后，代码的性能会比synchronized更好。

而**Atomic操作类**的底层正是用到了“**CAS机制**”。





# CAS存在什么问题？

-  **CPU开销过大**

  循环时间长开销大。自旋CAS如果长时间不成功，会给CPU带来非常大的执行开销。在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很到的压力。

  如果JVM能支持处理器提供的pause指令那么效率会有一定的提升，pause指令有两个作用，第一它可以延迟流水线执行指令（de-pipeline）,使CPU不会消耗过多的执行资源，延迟的时间取决于具体实现的版本，在一些处理器上延迟时间是零。第二它可以避免在退出循环的时候因内存顺序冲突（memory order violation）而引起CPU流水线被清空（CPU pipeline flush），从而提高CPU的执行效率。

- **不能保证代码块的原子性**

  CAS机制所保证的知识一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用synchronized了。

  只能保证一个共享变量的原子操作。当对一个共享变量执行操作时，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候就可以用锁，或者有一个取巧的办法，就是把多个共享变量合并成一个共享变量来操作。比如有两个共享变量i＝2,j=a，合并一下ij=2a，然后用CAS来操作ij。**从Java1.5开始JDK提供了AtomicReference类来保证引用对象之间的原子性，你可以把多个变量放在一个对象里来进行CAS操作。**

-  **ABA问题**

  因为CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。ABA问题的解决思路就是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A－B－A 就会变成1A-2B－3A。



# ABA问题是什么？

**什么是ABA问题？**

**当一个值从A变成B，又更新回A，普通CAS机制会误判通过检测。**

1. 假设内存中有一个值为A的变量，存储在地址V中。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205190849432.png" alt="image-20220205190849432" style="zoom:67%;" />

2. 此时有三个线程想使用CAS的方式更新这个变量的值，每个线程的执行时间有略微偏差。线程1和线程2已经获取当前值，线程3还未获取当前值。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205190958206.png" alt="image-20220205190958206" style="zoom:67%;" />

4. 接下来，线程1先一步执行成功，把当前值成功从A更新为B；同时线程2因为某种原因被阻塞住，没有做更新操作；线程3在线程1更新之后，获取了当前值B。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191122647.png" alt="image-20220205191122647" style="zoom:67%;" />

5. 在之后，线程2仍然处于阻塞状态，线程3继续执行，成功把当前值从B更新成了A。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191147883.png" alt="image-20220205191147883" style="zoom:67%;" />

6. 最后，线程2终于恢复了运行状态，由于阻塞之前已经获得了“当前值A”，并且经过compare检测，内存地址V中的实际值也是A，所以成功把变量值A更新成了B。

   <img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191223204.png" alt="image-20220205191223204" style="zoom:67%;" />

**实际案例**：

我们假设一个提款机的例子。假设有一个遵循CAS原理的提款机，小灰有100元存款，要用这个提款机来提款50元。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191643828.png" alt="image-20220205191643828" style="zoom:50%;" />

由于提款机硬件出了点问题，小灰的提款操作被同时提交了两次，开启了两个线程，两个线程都是获取当前值100元，要更新成50元。

理想情况下，应该一个线程更新成功，一个线程更新失败，小灰的存款值被扣一次。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191702669.png" alt="image-20220205191702669" style="zoom:50%;" />

线程1首先执行成功，把余额从100改成50.线程2因为某种原因阻塞。这时，小灰的妈妈刚好给小灰汇款50元。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191732221.png" alt="image-20220205191732221" style="zoom:50%;" />

线程2仍然是阻塞状态，线程3执行成功，把余额从50改成了100。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191759007.png" alt="image-20220205191759007" style="zoom:50%;" />

线程2恢复运行，由于阻塞之前获得了“当前值”100，并且经过compare检测，此时存款实际值也是100，所以会成功把变量值100更新成50。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191824977.png" alt="image-20220205191824977" style="zoom:50%;" />

原本线程2应当提交失败，小灰的正确余额应该保持100元，结果由于ABA问题提交成功了。



# ABA问题的解决方案

怎么解决呢？加个版本号就可以了。

真正要做到严谨的CAS机制，我们在compare阶段不仅要比较期望值A和地址V中的实际值，还要比较变量的版本号是否一致。

我们仍然以刚才的例子来说明，假设地址V中存储着变量值A，当前版本号是01。

线程1获取了当前值A和版本号01，想要更新为B，但是被阻塞了。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205191953740.png" alt="image-20220205191953740" style="zoom:67%;" />

这时候，内存地址V中变量发生了多次改变，版本号提升为03，但是变量值仍然是A。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205192017402.png" alt="image-20220205192017402" style="zoom:67%;" />

随后线程1恢复运行，进行compare操作。经过比较，线程1所获得的值和地址的实际值都是A，但是版本号不相等，所以这一次更新失败。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205192039434.png" alt="image-20220205192039434" style="zoom:67%;" />

**在Java中，AtomicStampedReference类就实现了用版本号作比较额CAS机制。**





# 参考文章

[什么是CAS机制？](https://blog.csdn.net/qq_32998153/article/details/79529704)

[Java中CAS原理详解](https://www.cnblogs.com/barrywxx/p/8487444.html)

