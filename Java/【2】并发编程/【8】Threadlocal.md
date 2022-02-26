# 弱应用

![image-20220207204944584](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20220207204944584.png)

![微信图片_20220225150516](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/微信图片_20220225150516.png)

# ThreadLocal源码解读

以`set()`方法为例：

main.java

```java
public static void main(String[] args) {
    ThreadLocal<Object> objectThreadLocal = new ThreadLocal<>();
    objectThreadLocal.set(new Object());
    objectThreadLocal.remove(); // ！！！！！一定要手动remove，避免value值发生内存泄漏
}
```

```java
public void set(T value) {
    Thread t = Thread.currentThread(); // 获取当前线程
    ThreadLocalMap map = getMap(t); // 获取当前线程的ThreadLocalMap对象
    // 如果当前线程已有ThreadLocalMap，则想其中添加一个map
    if (map != null) {
        map.set(this, value); // private void set(ThreadLocal<?> key, Object value) {}
    } else {
        createMap(t, value); // 如果当前线程没有ThreadLocalMap，则new ThreadLocalMap(this, firstValue);
    }
}
```

```java
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals; // 返回Thread对象的ThreadLocalMap
}
```

```java
public class Thread implements Runnable {
    ......
    ThreadLocal.ThreadLocalMap threadLocals = null; // 每一个Thread对象中均有一个ThreadLocalMap属性
    ......
}
```

```java
private void set(ThreadLocal<?> key, Object value) {

    // We don't use a fast path as with get() because it is at
    // least as common to use set() to create new entries as
    // it is to replace existing ones, in which case, a fast
    // path would fail more often than not.

    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);

    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        if (e.refersTo(key)) {
            e.value = value;
            return;
        }

        if (e.refersTo(null)) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }

    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}
```

```java
void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

# ThreadLocal之ThreadLocalMap源码解读

ThreadLocal类中有一个内部类ThreadLocalMap，ThreadLocalMap类又有一个Map类，继承了**弱引用**。

```java
static class ThreadLocalMap {
    ......
    static class Entry extends WeakReference<ThreadLocal<?>> {
        /** The value associated with this ThreadLocal. */
        Object value;

        Entry(ThreadLocal<?> k, Object v) {
            super(k);
            value = v;
        }
    }
    ......
}
```
