# 哈希表

- 核心是基于哈希值的桶和链表
- 0(1)的平均查找、插入、删除时间
- 致命缺陷是哈希值的碰撞(collision)

# Java7 HashMap

- 经典的哈希表实现:数组+链表
- 重要知识点
  - 初始容量
  - 负载因子
  - 哈希算法
  - 扩容
    - 低效
    - 线程不安全

## 源码解读

面，我们来看一下Java的HashMap的源代码。

Put一个Key,Value对到Hash表中：

```java
public V put(K key, V value) {
    if (table == EMPTY_TABLE) {
        inflateTable(threshold);
    }
    if (key == null)
        return putForNullKey(value);
    int hash = hash(key);
    int i = indexFor(hash, table.length);
    // 如果已存在相同key，则直接替换其value
    for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }
    // 如果不存在该key,创建一个新的哈希桶（Entry）
    modCount++;
    addEntry(hash, key, value, i);
    return null;
}
```

检查容量是否超标

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
    // 先检查容量是否达到阈值，如果达到阈值，则进行resize()
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = indexFor(hash, table.length);
    }
	
    createEntry(hash, key, value, bucketIndex);
}
```

新建一个更大尺寸的hash表，然后把数据从老的Hash表中迁移到新的Hash表中。

```java
void resize(int newCapacity) {
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    if (oldCapacity == MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return;
    }
    Entry[] newTable = new Entry[newCapacity];
    // 同过transfer()，把旧表迁移到新表上
    transfer(newTable, initHashSeedAsNeeded(newCapacity));
    table = newTable;
    threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
}
```

迁移的源代码，注意（！！！！！！）处：

```java
void transfer(Entry[] newTable)
{
    Entry[] src = table;
    int newCapacity = newTable.length;
    //下面这段代码的意思是：
    //  从OldTable里摘一个元素出来，然后放到NewTable中
    for (int j = 0; j < src.length; j++) {
        Entry<K,V> e = src[j];
        if (e != null) {
            src[j] = null;
            do {
                Entry<K,V> next = e.next; // ！！！！！！
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i]; // ！！！！！！
                newTable[i] = e; // ！！！！！！
                e = next; // ！！！！！！
            } while (e != null);
        }
    }
} 
```

好了，这个代码算是比较正常的。而且没有什么问题。

## Java7 HashMap存在的问题

- [非常容易碰到的死锁](https://coolshell.cn/articles/9606.html)

- 潜在的安全隐患

  - CVE - 2011- 4858

  - [Tomcat邮件组的讨论](https://lists.apache.org/thread/p8r2zblg42vvzvyhtjgpzlwwp4vv4tbl)

    ```
    可以通过精心构造的恶意请求引发DoS（链表性能退化分析）
    例如：
    youwebsite.com?a=1&b=2&c=3&d=4|
    问题：
    由于String中的hash计算公开，可以设计一组hash值相同的字符串，作为请求参数名，则Tomcat中HashMap结构会退化成链表。
    "Aa"，"BB"，"C#"
    ```

    注意：在1.8版本后，oracle提供了一个解决方案

    ![image-20220204234402684](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220204234402684.png)

首先需要强调一点，`HashMap`的线程不安全体现在会造成死循环、数据丢失、数据覆盖这些问题。其中死循环和数据丢失是在JDK1.7中出现的问题，在JDK1.8中已经得到解决，然而1.8中仍会有数据覆盖这样的问题。

### 扩容引发的线程不安全

`HashMap`的线程不安全主要是发生在扩容函数中，即根源是在**transfer函数**中，JDK1.7中`HashMap`的`transfer`函数如下：

![image-20220228120611509](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220228120611509.png)

这段代码是`HashMap`的扩容操作，重新定位每个桶的下标，并采用头插法将元素迁移到新数组中。头插法会将链表的顺序翻转，这也是形成死循环的关键点。理解了头插法后再继续往下看是如何造成死循环以及数据丢失的。

**扩容造成死循环和数据丢失的分析过程**：

假设现在有两个线程A、B同时对下面这个`HashMap`进行扩容操作：

![1553942282](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553942282.jpeg)

正常扩容后的结果是下面这样的：

![1553942426](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553942426.jpeg)

但是当线程A执行到上面`transfer`函数的第11行代码时，CPU时间片耗尽，线程A被挂起。即如下图中位置所示：

![image-20220228121019914](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220228121019914.png)

此时线程A中：e=3、next=7、e.next=null

![1553943048](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553943048.jpeg)

当线程A的时间片耗尽后，CPU开始执行线程B，并在线程B中成功的完成了数据迁移

![1553943250](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553943250.jpeg)

重点来了，根据[Java](https://so.csdn.net/so/search?q=Java&spm=1001.2101.3001.7020)内存模式可知，线程B执行完数据迁移后，此时主内存中`newTable`和`table`都是最新的，也就是说：7.next=3、3.next=null。

随后线程A获得CPU时间片继续执行`newTable[i] = e`，将3放入新数组对应的位置，执行完此轮循环后线程A的情况如下：

![1553943714](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553943714.jpeg)

接着继续执行下一轮循环，此时e=7，从主内存中读取e.next时发现主内存中7.next=3，于是乎next=3，并将7采用头插法的方式放入新数组中，并继续执行完此轮循环，结果如下：

![1553944363](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553944363.jpeg)

执行下一次循环可以发现，next=e.next=null，所以此轮循环将会是最后一轮循环。接下来当执行完e.next=newTable[i]即3.next=7后，3和7之间就相互连接了，当执行完newTable[i]=e后，3被头插法重新插入到链表中，执行结果如下图所示：

![1553944998](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/1553944998.jpeg)

面说了此时e.next=null即next=null，当执行完e=null后，将不会进行下一轮循环。到此线程A、B的扩容操作完成，很明显当线程A执行完后，HashMap中出现了==环形结构==，当在以后对该HashMap进行操作时会出现死循环。

并且从上图可以发现，元素5在扩容期间被莫名的丢失了，这就发生了==数据丢失==的问题。

# Java8 HashMap的改进

- 数组+链表/红黑树

- 扩容时插入顺序的改进

- 函数方法

  - forEach

  - compute系列

- Map的新API

  - merge !

  - replace

## 源码解读

以`put()`方法为例，讲解HashMap的扩容过程

首先执行put()方法，会计算该key的hash值，并内部调用putVal()方法。

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // hash表为空或表长为0时，执行resize()
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 如果当前key并不在hash表中，直接添加进hash表
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        // p表示该hash值在hash表中链表（树根节点）的元素，如果p的key和要插入key，则e记下p
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 插入，为树节点时
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 插入，为链表时
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

## JDK1.8中的线程不安全

根据上面JDK1.7出现的问题，在JDK1.8中已经得到了很好的解决，如果你去阅读1.8的源码会发现找不到transfer函数，因为JDK1.8直接在resize函数中完成了数据迁移。另外说一句，JDK1.8在进行元素插入时使用的是尾插法。

为什么说JDK1.8会出现数据覆盖的情况喃，我们来看一下下面这段JDK1.8中的putVal()中第6行操作代码：

其中第六行代码是判断是否出现hash碰撞，假设两个线程A、B都在进行put操作，并且hash函数计算出的插入下标是相同的，当线程A执行完第六行代码后由于时间片耗尽导致被挂起，而线程B得到时间片后在该下标处插入了元素，完成了正常的插入，然后线程A获得时间片，由于之前已经进行了hash碰撞的判断，所有此时不会再进行判断，而是直接进行插入，这就导致了线程B插入的数据被线程A覆盖了，从而线程不安全。

除此之前，还有就是代码的第38行处有个++size，我们这样想，还是线程A、B，这两个线程同时进行put操作时，假设当前HashMap的zise大小为10，当线程A执行到第38行代码时，从主内存中获得size的值为10后准备进行+1操作，但是由于时间片耗尽只好让出CPU，线程B快乐的拿到CPU还是从主内存中拿到size的值10进行+1操作，完成了put操作并将size=11写回主内存，然后线程A再次拿到CPU并继续执行(此时size的值仍为10)，当执行完put操作后，还是将size=11写回内存，此时，线程A、B都执行了一次put操作，但是size的值只增加了1，所有说还是由于数据覆盖又导致了线程不安全。


## fail-fast机制

我们知道 java.util.HashMap 不是线程安全的，因此如果在使用迭代器的过程中有其他线程修改了map，那么将抛出ConcurrentModificationException，这就是所谓fail-fast策略。这一策略在源码中的实现是通过 modCount 域，modCount 顾名思义就是修改次数，对HashMap 内容的修改都将增加这个值，那么==在迭代器初始化过程中会将这个值赋给迭代器的 expectedModCount。在迭代过程中，判断modCount 跟 expectedModCount 是否相等，如果不相等就表示已经有其他线程修改了 Map。==

# 总结（Java7 到 Java8 做了哪些改变，为什么?）

- jdk1.7底层采用entry数组+链表的数据结构，而1.8采用node数组+链表/红黑树的数据结构。

- jdk1.7的HashMap插入新值时使用头插法，1.8使用尾插法。（rehash）

  使用头插法比较快，但在多线程扩容时会引起倒序和闭环的问题。所以1.8就采用了尾插法。

- 扩容后新表中的索引位置计算方式不同，**jdk1.7扩容时是将旧表元素的所有数据重新进行哈希计算，即hashCode & (length-1)**。而**1.8中扩容时只需将hashCode和老数组长度做与运算判断是0还是1，是0的话索引不变，是1的话索引变为老索引位置+老数组长度。**



# 经典面试题

## HashMap先不考虑[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，手写一个底层数据结构，存储key value

**数组 + 链表结构：**

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    transient Node<K,V>[] table;
}  
```

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }
```

**数组 + 红黑树结构：**

```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V> {
    static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
	}
}
```

```java
 static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
     TreeNode<K,V> parent;  // red-black tree links
     TreeNode<K,V> left;
     TreeNode<K,V> right;
     TreeNode<K,V> prev;    // needed to unlink next upon deletion
     boolean red;
     
     TreeNode(int hash, K key, V val, Node<K,V> next) {
         super(hash, key, val, next);
     }

 }  
```

## 为什么负载因子是0.75？

![image-20220204183529528](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220204183529528.png)

## 为什么主数组的长度必须为2^n^

![image-20220204184426581](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220204184426581.png)

![image-20220204225604154](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220204225604154.png)

## HashMap为什么是线程不安全的

jdk1.7，在扩容中，数据迁移transfer()，采用头插法，多线程情况下，会产生环形结构和数据丢失；

jdk1.8，resize()中，多线程插入情况下，会产生数据覆盖或size++只操作一次的情况；

## HashMap的put()的过程

![621c73330e3e745d894dd88c (1)](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/621c73330e3e745d894dd88c (1).png)

## HashMap的resize()扩容过程

![621c829f1efad40767365be7](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/621c829f1efad40767365be7.png)

## 迁移时，为什么要使用尾插法？

在jdk1.7 中，扩容采用头插法，但头插法会改变节点的排列，因此，在多线程的场景下，原先按顺序排列的链表，就可能出现首尾相连的问题。在jdk1.8之后，就采用尾插法，因为尾插法不会影响原有的顺序，也就解决了节点相连成环的问题。



