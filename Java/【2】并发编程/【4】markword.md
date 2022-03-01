# markword

**==关于对象内存布局，详细参见JVM部分==**

**Hotspot 虚拟机的对象头包括两部分信息**，**第一部分用于存储对象自身的运行时数据**（哈希码、GC 分代年龄、锁状态标志等等），**另一部分是类型指针**，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是那个类的实例。

对象头：存储对象自身的运行时数据：==**Mark Word**==（在32bit和64bit虚拟机上长度分别为32bit和64bit），包含如下信息：

- 对象hashCode

- 对象GC分代年龄

- 锁状态标志（轻量级锁、重量级锁）

- 线程持有的锁（轻量级锁、重量级锁）

- 偏向锁相关：偏向锁、自旋锁、轻量级锁以及其他的一些锁优化策略是JDK1.6加入的，这些优化使得Synchronized的性能与ReentrantLock的性能持平，**在Synchronized可以满足要求的情况下，优先使用Synchronized**，除非是使用一些ReentrantLock独有的功能，例如指定时间等待等。

```java
public class Test {
    public static void main(String[] args) {
        Object o = new Object();
        System.out.println(ClassLayout.parseInstance(o).toPrintable());
        synchronized (o) {
            System.out.println(ClassLayout.parseInstance(o).toPrintable());
        }
    }
}
```

![image-20220205225310137](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220205225310137.png)

**注意**：

- 锁相关的信息是记录在markword（对象头）中；





# 工具：JOL = Java Object Layout

```xml
<dependencies>
    <dependency>
        <groupId>org.openjdk.jol</groupId>
        <artifactId>jol-core</artifactId>
        <version>0.9</version>
    </dependency>
</dependencies>
```

