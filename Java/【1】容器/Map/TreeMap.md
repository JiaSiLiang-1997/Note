# TreeMap的底层数据结构

## 源码解读

```java
 static final class Entry<K,V> implements Map.Entry<K,V> {
     K key;
     V value;
     Entry<K,V> left;
     Entry<K,V> right;
     Entry<K,V> parent;
     boolean color = BLACK;
 }
```

```java
public class TreeMap<K,V> extends AbstractMap<K,V> implements NavigableMap<K,V>, Cloneable, java.io.Serializable {
    private final Comparator<? super K> comparator;

    private transient Entry<K,V> root;

    private transient int size = 0;

    private transient int modCount = 0;

    public TreeMap() {
        comparator = null;
    }

    public TreeMap(Comparator<? super K> comparator) {
        this.comparator = comparator;
    }

    public TreeMap(Map<? extends K, ? extends V> m) {
        comparator = null;
        putAll(m);
    }

    public TreeMap(SortedMap<K, ? extends V> m) {
        comparator = m.comparator();
        try {
            buildFromSorted(m.size(), m.entrySet().iterator(), null, null);
        } catch (java.io.IOException cannotHappen) {
        } catch (ClassNotFoundException cannotHappen) {
        }
    }
}

```

# 怎样实现排序的？

SortedMap<K,V>  =>  NavigableMap<K,V> => TreeMap<K,V>

在SorterMap中定义了一个Comparator排序器，在初始化TreeMap时，可以提供该排序器的排序方式，也可以默认不提供，其底层使用红黑树实现。在插入元素的过程中，会进行元素的对比。

