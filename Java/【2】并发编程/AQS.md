# AQS框架

`AbstractQueuedSynchronizer`，简称AQS，是`java.util.concurrent`的核心，`CountDownLatch`、`FutureTask`、`Semaphore`、`ReentrantLock`等都有一个内部类是这个抽象类的子类。

**整个AQS是典型的模板模式的应用**，设计得十分精巧，对于FIFO队列的各种操作在AQS中已经实现了，**AQS的子类一般只需要重写tryAcquire(int arg)和tryRelease(int arg)两个方法即可**。

首先，我们通过下面的架构图来整体了解一下 AQS 框架：

![82077ccf14127a87b77cefd1ccf562d3253591](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/82077ccf14127a87b77cefd1ccf562d3253591.png)

- 上图中有颜色的为 Method，无颜色的为 Attribution。
- 总的来说，AQS 框架共分为五层，自上而下由浅入深，从 AQS 对外暴露的 API 到底层基础数据。
- 当有自定义同步器接入时，只需重写第一层所需要的部分方法即可，不需要关注底层具体的实现流程。当自定义同步器进行加锁或者解锁操作时，先经过第一层的 API 进入 AQS 内部方法，然后经过第二层进行锁的获取，接着对于获取锁失败的流程，进入第三层和第四层的等待队列处理，而这些处理方式均依赖于第五层的基础数据提供层。

下面我们会从整体到细节，从流程到方法逐一剖析 AQS 框架，主要分析过程如下：

![d2f7f7fffdc30d85d17b44266c3ab05323338](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/d2f7f7fffdc30d85d17b44266c3ab05323338.png)

# 原理概览

**AQS 核心思想是，如果被请求的共享资源空闲，那么就将当前请求资源的线程设置为有效的工作线程，将共享资源设置为锁定状态；如果共享资源被占用，就需要一定的阻塞等待唤醒机制来保证锁分配。**这个机制主要用的是 CLH 队列的变体实现的，将暂时获取不到锁的线程加入到队列中。

CLH：Craig、Landin and Hagersten 队列，是单向链表，AQS 中的队列是 CLH 变体的虚拟双向队列（FIFO），AQS 是通过将每条请求共享资源的线程封装成一个节点来实现锁的分配。

![7132e4cef44c26f62835b197b239147b18062](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/7132e4cef44c26f62835b197b239147b18062.png)

AQS维护了一个volatile int state（代表共享资源）和一个FIFO线程等待队列（多线程争用资源被阻塞时会进入此队列）。通过 CAS 完成对 state 值的修改。

## AQS 数据结构

先来看下 AQS 中最基本的数据结构——Node，Node 即为上面 CLH 变体队列中的节点。

```java
/** CLH Nodes */
abstract static class Node {
    volatile Node prev;       // initially attached via casTail
    volatile Node next;       // visibly nonnull when signallable
    Thread waiter;            // visibly nonnull when enqueued
    volatile int status;      // written by owner, atomic bit ops by others

    // methods for atomic operations
    final boolean casPrev(Node c, Node v) {  // for cleanQueue
        return U.weakCompareAndSetReference(this, PREV, c, v);
    }
    final boolean casNext(Node c, Node v) {  // for cleanQueue
        return U.weakCompareAndSetReference(this, NEXT, c, v);
    }
    final int getAndUnsetStatus(int v) {     // for signalling
        return U.getAndBitwiseAndInt(this, STATUS, ~v);
    }
    final void setPrevRelaxed(Node p) {      // for off-queue assignment
        U.putReference(this, PREV, p);
    }
    final void setStatusRelaxed(int s) {     // for off-queue assignment
        U.putInt(this, STATUS, s);
    }
    final void clearStatus() {               // for reducing unneeded signals
        U.putIntOpaque(this, STATUS, 0);
    }

    private static final long STATUS
        = U.objectFieldOffset(Node.class, "status");
    private static final long NEXT
        = U.objectFieldOffset(Node.class, "next");
    private static final long PREV
        = U.objectFieldOffset(Node.class, "prev");
}
```

![960271cf2b5c8a185eed23e98b72c75538637](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/960271cf2b5c8a185eed23e98b72c75538637.png)

解释一下几个方法和属性值的含义：

| **方法和属性值** | **含义**                                                     |
| ---------------- | ------------------------------------------------------------ |
| waitStatus       | 当前节点在队列中的状态                                       |
| thread           | 表示处于该节点的线程                                         |
| prev             | 前驱指针                                                     |
| predecessor      | 返回前驱节点，没有的话抛出 npe                               |
| nextWaiter       | 指向下一个处于 CONDITION 状态的节点（由于本篇文章不讲述 Condition Queue 队列，这个指针不多介绍） |
| next             | 后继指针                                                     |

waitStatus 有下面几个枚举值：

- **0**：当一个 Node 被初始化的时候的默认值；
- **CANCELLED**(1)：表示当前结点已取消调度。当timeout或被中断（响应中断的情况下），会触发变更为此状态，进入该状态后的结点将不会再变化；
- **SIGNAL**(-1)：表示后继结点在等待当前结点唤醒。后继结点入队时，会将前继结点的状态更新为SIGNAL；
- **CONDITION**(-2)：表示结点等待在Condition上，当其他线程调用了Condition的signal()方法后，CONDITION状态的结点将**从等待队列转移到同步队列中**，等待获取同步锁。
- **PROPAGATE**(-3)：共享模式下，前继结点不仅会唤醒其后继结点，同时也可能会唤醒后继的后继结点。

注意：**负值表示结点处于有效等待状态，而正值表示结点已被取消。所以源码中很多地方用>0、<0来判断结点的状态是否正常**。

## 同步状态 State

在了解数据结构后，接下来了解一下 AQS 的同步状态——State。AQS 中维护了一个名为 state 的字段，意为同步状态，是由 Volatile 修饰的，用于展示当前临界资源的获锁情况。

```java
/**
* The synchronization state.
*/
private volatile int state;
```

下面提供了几个访问这个字段的方法：

| **方法名**                                                   | **描述**                |
| ------------------------------------------------------------ | ----------------------- |
| protected final int getState()                               | 获取 State 的值         |
| protected final void setState(int newState)                  | 设置 State 的值         |
| protected final boolean compareAndSetState(int expect, int update) | 使用 CAS 方式更新 State |

这几个方法都是 Final 修饰的，说明子类中无法重写它们。我们可以通过修改 State 字段表示的同步状态来实现多线程的独占模式和共享模式（加锁过程）。

## 共享模式 & 独占模式

线程两种锁的模式：

```java
// Concrete classes tagged by type
static final class ExclusiveNode extends Node { }
static final class SharedNode extends Node { }
```

| **模式**  | **含义**                       |
| --------- | ------------------------------ |
| SHARED    | 表示线程以共享的模式等待锁     |
| EXCLUSIVE | 表示线程正在以独占的方式等待锁 |

不同的自定义同步器争用共享资源的方式也不同。**自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可**，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。自定义同步器实现时主要实现以下几种方法：

- `isHeldExclusively()`：该线程是否正在独占资源。只有用到condition才需要去实现它。
- `tryAcquire(int)`：独占方式。尝试获取资源，成功则返回true，失败则返回false。
- `tryRelease(int)`：独占方式。尝试释放资源，成功则返回true，失败则返回false。
- `tryAcquireShared(int)`：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
- `tryReleaseShared(int)`：共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。

### 共享模式

Share（共享，多个线程可同时执行，如**Semaphore/CountDownLatch**）

以CountDownLatch以例，任务分为N个子线程去执行，state也初始化为N（注意N要与线程个数一致）。这N个子线程是并行执行的，每个子线程执行完后countDown()一次，state会CAS减1。等到所有子线程都执行完后(即state=0)，会unpark()主调用线程，然后主调用线程就会从await()函数返回，继续后余动作。

![3f1e1a44f5b7d77000ba4f9476189b2e32806](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/3f1e1a44f5b7d77000ba4f9476189b2e32806.png)

### 独占模式

Exclusive（独占，只有一个线程能执行，如**ReentrantLock**）

以ReentrantLock为例，state初始化为0，表示未锁定状态。A线程lock()时，会调用tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这就是可重入的概念。但要注意，获取多少次就要释放多么次，这样才能保证state是能回到零态的。

![27605d483e8935da683a93be015713f331378](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/27605d483e8935da683a93be015713f331378.png)

**一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现tryAcquire-tryRelease、tryAcquireShared-tryReleaseShared中的一种即可。但AQS也支持自定义同步器同时实现独占和共享两种方式，如ReentrantReadWriteLock。**

## 实现思路

AQS内部维护了一个CLH队列来管理锁。线程会首先尝试获取锁，如果失败就将当前线程及等待状态等信息包装成一个node节点加入到同步队列sync queue里。 接着会不断的循环尝试获取锁，条件是当前节点为head的直接后继才会尝试。如果失败就会阻塞自己直到自己被唤醒。而当持有锁的线程释放锁的时候，会唤醒队列中的后继线程。

CLH(Craig,Landin,and Hagersten)队列是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系）。AQS是将每条请求共享资源的线程封装成一个CLH锁队列的一个结点（Node）来实现锁的分配。







# 参考文章

[Java并发之AQS详解](https://www.cnblogs.com/waterystone/p/4920797.html)

[什么是AQS及其原理](https://blog.csdn.net/striveb/article/details/86761900)

[Java并发包基石-AQS详解 ](https://www.cnblogs.com/chengxiao/p/7141160.html)

