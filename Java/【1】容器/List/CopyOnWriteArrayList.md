# CopyOnWriteArrayList 简介

```java
public class CopyOnWriteArrayList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    
}
```

在很多应用场景中，读操作可能会远远大于写操作。由于读操作根本不会修改原有的数据，因此如果每次读取都进行加锁操作，其实是一种资源浪费。我们应该允许多个线程同时访问 List 的内部数据，毕竟读操作是线程安全的。

这和 `ReentrantReadWriteLock` 读写锁的思想非常类似，也就是 **读读共享、写写互斥、读写互斥、写读互斥**。JDK中提供了 `CopyOnWriteArrayList` 类，相比于在读写锁的思想又更进一步。为了将读取的性能发挥到极致，`CopyOnWriteArrayList` 读取是完全不用加锁的，并且更厉害的是：写入也不会阻塞读取操作，只有写入和写入之间需要进行同步等待，读操作的性能得到大幅度提升。

# CopyOnWriteArrayList 是如何做到的

`CopyOnWriteArrayList` 类的所有可变操作（add，set等等）都是通过创建底层数组的新副本来实现的。当 List 需要被修改的时候，并不直接修改原有数组对象，而是对原有数据进行一次拷贝，将修改的内容写入副本中。写完之后，再将修改完的副本替换成原来的数据，这样就可以保证写操作不会影响读操作了。

从 `CopyOnWriteArrayList` 的名字可以看出，`CopyOnWriteArrayList` 是满足 `CopyOnWrite` 的 ArrayList，所谓 `CopyOnWrite` 的意思：、就是对一块内存进行修改时，不直接在原有内存块中进行写操作，而是将内存拷贝一份，在新的内存中进行写操作，写完之后，再将原来指向的内存指针指到新的内存，原来的内存就可以被回收。

# CopyOnWriteArrayList 读取和写入源码简单分析

1. CopyOnWriteArrayList 读取操作的实现

读取操作没有任何同步控制和锁操作，理由就是内部数组 array 不会发生修改，只会被另外一个 array 替换，因此可以保证数据安全。

```java
    /** The array, accessed only via getArray/setArray. */
    private transient volatile Object[] array;

    public E get(int index) {
        return get(getArray(), index);
    }

    @SuppressWarnings("unchecked")
    private E get(Object[] a, int index) {
        return (E) a[index];
    }

    final Object[] getArray() {
        return array;
    }
```

1. CopyOnWriteArrayList 写入操作的实现

CopyOnWriteArrayList 写入操作 `add()` 方法在添加集合的时候加了锁，保证同步，避免多线程写的时候会 copy 出多个副本。

```java
    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return {@code true} (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();  // 加锁
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);  // 拷贝新数组
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();  // 释放锁
        }
    }
```