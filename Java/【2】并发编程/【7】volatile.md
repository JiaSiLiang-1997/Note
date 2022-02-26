# CPU基础知识

## 计算机组成

![image-20220207104216604](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207104216604.png)

### 存储器的层次结构

![image-20220207104240690](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207104240690.png)

![image-20220207104258645](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207104258645.png)

### 多核CPU

![image-20220207104338090](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207104338090.png)

### 超线程

超线程：一个ALU对应多个PC | Registers，所谓的四核八线程

## 缓存行

### 缓存行

缓存行64个字节是CPU同步的基本单位，缓存行隔离会比伪共享效率要高，cache line 缓存行，最小存储单元

- 缓存行越大，局部性空间效率越高，但读取时间慢
- 缓存行越小，局部性空间效率越低，但读取时间快
- 取一个折中值，目前多用：64字节

![image-20220207105350580](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207105350580.png)

### 缓存行对齐

Disruptor框架中，就使用了缓存行对齐

```java
package com.mashibing.juc.c_028_FalseSharing;

public class T02_CacheLinePadding {
    private static class Padding {
        public volatile long p1, p2, p3, p4, p5, p6, p7; //
    }

    private static class T extends Padding {
        public volatile long x = 0L;
    }

    public static T[] arr = new T[2];

    static {
        arr[0] = new T();
        arr[1] = new T();
    }

    public static void main(String[] args) throws Exception {
        Thread t1 = new Thread(()->{
            for (long i = 0; i < 1000_0000L; i++) {
                arr[0].x = i;
            }
        });

        Thread t2 = new Thread(()->{
            for (long i = 0; i < 1000_0000L; i++) {
                arr[1].x = i;
            }
        });

        final long start = System.nanoTime();
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println((System.nanoTime() - start)/100_0000);
    }
}
```



# MESI 缓存一致性协议

https://www.cnblogs.com/z00377750/p/9180644.html

![image-20220207114843875](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207114843875.png)

## 概念

**`MESI`**（`Modified Exclusive Shared Or Invalid`）(也称为伊利诺斯协议，是因为该协议由伊利诺斯州立大学提出）是一种广泛使用的支持写回策略的缓存一致性协议。

## MESI协议中的状态

`CPU`中每个缓存行（`caceh line`)使用4种状态进行标记（使用额外的两位(`bit`)表示):

**M: 被修改（Modified)**

该缓存行只被缓存在该`CPU`的缓存中，并且是被修改过的（`dirty`),即与主存中的数据不一致，该缓存行中的内存需要在未来的某个时间点（允许其它`CPU`读取请主存中相应内存之前）写回（`write back`）主存。

当被写回主存之后，该缓存行的状态会变成独享（`exclusive`)状态。

**E: 独享的（Exclusive)**

该缓存行只被缓存在该`CPU`的缓存中，它是未被修改过的（`clean`)，与主存中数据一致。该状态可以在任何时刻当有其它`CPU`读取该内存时变成共享状态（`shared`)。

同样地，当`CPU`修改该缓存行中内容时，该状态可以变成`Modified`状态。

**S: 共享的（Shared)**

该状态意味着该缓存行可能被多个`CPU`缓存，并且各个缓存中的数据与主存数据一致（`clean`)，当有一个`CPU`修改该缓存行中，其它`CPU`中该缓存行可以被作废（变成无效状态（`Invalid`））。

**I: 无效的（Invalid）**

该缓存是无效的（可能有其它`CPU`修改了该缓存行）。

## MESI状态转换图

![image-20220207120303527](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207120303527.png)

状态之间的相互转换关系也可以使用下表进行表示。

![image-20220207120326217](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207120326217.png)

![img](https://images2018.cnblogs.com/blog/1014100/201806/1014100-20180613225133544-1986197420.png)

## 操作

在一个典型系统中，可能会有几个缓存（在多核系统中，每个核心都会有自己的缓存）共享主存总线，每个相应的`CPU`会发出读写请求，而缓存的目的是为了减少`CPU`读写共享主存的次数。

一个缓存除在`Invalid`状态外都可以满足cpu的读请求，一个`Invalid`的缓存行必须从主存中读取（变成`S`或者 `E`状态）来满足该`CPU`的读请求。

一个写请求只有在该缓存行是M或者E状态时才能被执行，如果缓存行处于`S`状态，必须先将其它缓存中该缓存行变成`Invalid`状态（也既是不允许不同`CPU`同时修改同一缓存行，即使修改该缓存行中不同位置的数据也不允许）。该操作经常作用广播的方式来完成，例如：`RequestFor Ownership` (`RFO`)。

缓存可以随时将一个非M状态的缓存行作废，或者变成`Invalid`状态，而一个`M`状态的缓存行必须先被写回主存。

一个处于`M`状态的缓存行必须时刻监听所有试图读该缓存行相对就主存的操作，这种操作必须在缓存将该缓存行写回主存并将状态变成S状态之前被延迟执行。

一个处于S状态的缓存行也必须监听其它缓存使该缓存行无效或者独享该缓存行的请求，并将该缓存行变成无效（`Invalid`）。

一个处于E状态的缓存行也必须监听其它缓存读主存中该缓存行的操作，一旦有这种操作，该缓存行需要变成`S`状态。

对于`M`和`E`状态而言总是精确的，他们在和该缓存行的真正状态是一致的。而`S`状态可能是非一致的，如果一个缓存将处于`S`状态的缓存行作废了，而另一个缓存实际上可能已经

独享了该缓存行，但是该缓存却不会将该缓存行升迁为`E`状态，这是因为其它缓存不会广播他们作废掉该缓存行的通知，同样由于缓存并没有保存该缓存行的`copy`的数量，因此（即使有这种通知）也没有办法确定自己是否已经独享了该缓存行。

从上面的意义看来E状态是一种投机性的优化：如果一个`CPU`想修改一个处于`S`状态的缓存行，总线事务需要将所有该缓存行的`copy`变成`Invalid`状态，而修改`E`状态的缓存不需要使用总线事务。



**注意**：

缓存一致性协议多有种，不同的CPU可能使用不同的协议：MSI、MESI、Synapse、Firefly、Dragon

## 系统底层如何实现数据一致性

1. MESI如果能解决，就使用MESI；
2. 如果不能，就锁总线；





# volatile的用途

- 线程可见性；
- 防止指令重排序；



## 线程可见性



```java
package com.mashibing.testvolatile;

public class T01_ThreadVisibility {
    private static volatile boolean flag = true;

    public static void main(String[] args) throws InterruptedException {
        new Thread(()-> {
            while (flag) {
                //do sth
            }
            System.out.println("end");
        }, "server").start();


        Thread.sleep(1000);

        flag = false;
    }
}
```







## 防止指令重排序

### 指令重排序

https://www.cnblogs.com/liushaodong/p/4777308.html

![image-20220207120940530](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207120940530.png)

```java
package com.mashibing.jvm.c3_jmm;

public class T04_Disorder {
    private static int x = 0, y = 0;
    private static int a = 0, b =0;

    public static void main(String[] args) throws InterruptedException {
        int i = 0;
        for(;;) {
            i++;
            x = 0; y = 0;
            a = 0; b = 0;
            Thread one = new Thread(new Runnable() {
                public void run() {
                    //由于线程one先启动，下面这句话让它等一等线程two. 读着可根据自己电脑的实际性能适当调整等待时间.
                    //shortWait(100000);
                    a = 1;
                    x = b;
                }
            });

            Thread other = new Thread(new Runnable() {
                public void run() {
                    b = 1;
                    y = a;
                }
            });
            one.start();other.start();
            one.join();other.join();
            String result = "第" + i + "次 (" + x + "," + y + "）";
            if(x == 0 && y == 0) {
                System.err.println(result);
                break;
            } else {
                //System.out.println(result);
            }
        }
    }


    public static void shortWait(long interval){
        long start = System.nanoTime();
        long end;
        do{
            end = System.nanoTime();
        }while(start + interval >= end);
    }
}
```



### 什么是内存屏障

https://blog.csdn.net/truelove12358/article/details/106496259

**内存屏障正是通过阻止屏障两边的指令重排序来避免编译器和硬件的不正确优化而提出的一种解决办法**。

**volatile语义中的内存屏障**：

- 在每个volatile写操作前插入StoreStore屏障，在写操作后插入StoreLoad屏障；
- 在每个volatile读操作前插入LoadLoad屏障，在读操作后插入LoadStore屏障；

volatile的内存屏障策略非常严格保守，保证了线程可见性。



### 系统底层如何保证有些性

1. **内存屏障** sfence mefence lfence等系统原语；

   在hotpsot中实现时，仍然使用的是`lock指令`，因为sfence mefence lfence这类原语不具备移植性，有的CPU支持，有的CPU不支持。

2. 锁总线；（hotspot实现）



# 底层实现

### volatile如何解决指令重排序

#### 源码层（java）

volatile i（源码）

#### 编译器层 — 字节码层（class）

ACC_VOLATILE（字节码中，只是多了个标记）

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207173457963.png" alt="image-20220207173457963" style="zoom:50%;" />

#### JVM层

JVM的内存屏障（Memory Barrier）可以被分为以下几种类型：

![image-20220207172839465](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207172839465.png)

JVM层面具体实现：

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207173309478.png" alt="image-20220207173309478" style="zoom:50%;" />

#### 具体实现（hotspot实现 — 锁总线）

bytecodeinterpreter.cpp

```c++
int field_offset = cache->f2_as_index();
          if (cache->is_volatile()) {
            if (support_IRIW_for_not_multiple_copy_atomic_cpu) {
              OrderAccess::fence();
            }
```

orderaccess_linux_x86.inline.hpp

```c++
inline void OrderAccess::fence() {
  if (os::is_MP()) {
    // always use locked addl since mfence is sometimes expensive
#ifdef AMD64
    __asm__ volatile ("lock; addl $0,0(%%rsp)" : : : "cc", "memory");
#else
    __asm__ volatile ("lock; addl $0,0(%%esp)" : : : "cc", "memory");
#endif
  }
}
```





# 经典问题

关于`Object o = new Object()`

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207163357515.png" alt="image-20220207163357515" style="zoom:67%;" />



