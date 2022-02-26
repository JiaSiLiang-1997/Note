# 一 Stream流

说到`Stream`便容易想到`I/O Stream`，而实际上，谁规定"流"就一定是"`I/O流`"呢？在Java 8中，得益于Lambda所带来的函数式编程，引入了一个**全新的Stream概念**，用于解决已有集合类库既有的弊端。

## 1.1 引言

### 1.1.1 传统集合的多步遍历代码

几乎所有的集合（如`Collection`接口或`Map`接口等）都支持直接或间接的遍历操作。而当我们需要对集合中的元素进行操作的时候，除了必须的添加、删除、获取外，最典型的就是集合遍历。例如：

```java
import java.util.ArrayList;
import java.util.List;

public class DemoForEach {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        list.add("张三丰");
        
        for (String name : list) {
            System.out.println(name);
        }
    }
}
```

这是一段非常简单的集合遍历操作：对集合中的每一个字符串都进行打印输出操作。

### 1.1.2 循环遍历的弊端

Java 8的Lambda让我们可以更加专注于**做什么**（what），而不是**怎么做**（How），这点此前已经结合内部类进行了对比说明。现在，我们仔细体会一下上例代码，可以发现：

- `for`循环的语法就是**怎么做**；
- `for`循环的循环体才是**做什么**；

为什么使用循环？因为要进行遍历。但循环是遍历的唯一方式吗？遍历是指每一个元素逐一进行处理，**而并不是从第一个到最后一个依次处理的循环。**前者是目的，后者是方式。

试想一下，如果希望对集合中的元素进行筛选过滤：

1. 将**集合A**根据条件一过滤为**子集B**；
2. 然后再根据条件二过滤为**子集C**；

那怎么办？在Java 8之前的做法可能为：

```java
import java.util.ArrayList;
import java.util.List;

public class DemoNormalFilter {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        list.add("张三丰");
        
        List<String> zhangList = new ArrayList<>();
        for (String name : list) {
            if (name.startsWith("张")) {
                zhangList.add(name);
            }
        }
        
        List<String> shortList = new ArrayList<>();
        for (String name : zhangList) {
            if (name.length() == 3) {
                shortList.add(name);
            }
        }
        
        for (String name : shortList) {
            System.out.pritnln(name);
        }
    }
}
```

这段代码中含有三个循环，每一个作用不同：

1. 首先筛选所有姓张的人；
2. 然后筛选名字有三个字的人；
3. 最后对结果进行打印输出。

每当我们需要对集合中的元素进行操作的时候，总是需要进行循环、循环、再循环。这是理所当然的吗？**不是**。循环是做事情的方式，而不是目的。另一方面，使用线性循环就意味着只能遍历一次。如果希望再次遍历，只能再使用另一个循环从头开始。

Lambda的衍生物Stream能带来更优雅的写法。

### 1.1.3 Stream的更优写法

下面来看一下借助Java 8的Stream API，什么才叫优雅：

```java
import java.util.ArrayList;
import java.util.List;

public class DemoStreamFilter {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张无忌");
        list.add("周芷若");
        list.add("赵敏");
        list.add("张强");
        list.add("张三丰");
        
        // 1.对集合中的元素进行过滤，只要 "张" 开头的
        // 2.只要名字长度为3的
        // 3.对结果集合进行遍历
        list.stream()
            .filter(name -> name.startWith("张"))
            .filter(name -> name.length() == 3)
            .filter(name -> System.out.println(name));
    }
}
```



## 1.2 流式思想概述

**注意：忘记传统 I\O流 的固有印象！**

整体来看，流式思想类似于工厂车间的**生产流水线**。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210422210544813.png" alt="image-20210422210544813" style="zoom:67%;" />

当需要对多个元素进行操作（特别是多步操作）的时候，考虑到性能及便利性，我们应该首先拼好一个"模型"步骤方案，然后再按照方案去执行它。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210422210838090.png" alt="image-20210422210838090" style="zoom:67%;" />

这张图展示了过滤、映射、跳过、计数等多步操作，这是一种集合元素的处理方案，而方案就是一种"函数模型"。图中每一个方框都是一个"流"，调用指定的方法，可以从一个流模型转换为另一个流模型。而最右侧的数字3是最终结果。

这里`filter()`、`map()`、`skip()`都是在对函数模型进行操作，集合元素并没有真正被处理。只有当终结方法`count()`执行的时候，整个模型才会按照指定策略执行操作。而这得益于Lambda的延迟执行特性。

```tex
备注："Stream流"其实是一个集合元素的函数模型，它并不是集合，也不是数据结构，其本身并不存储任何元素（或其地址值）。
```

Stream（流）是一个来自数据源的元素队列

- 元素是特定类型的对象，形成一个队列。Java中的Stream并不会存储元素，而是按需计算。
- **数据源**流的来源。可以是集合，数组等。

和以前的`Collection`操作不同，Stream操作还有两个基础的特征：

- **Pipelining**：中间操作都会返回流对象本身。这样多个操作可以串联成一个管道，如同流式风格（fluent style）。这样做可以对操作进行优化，比如延迟执行（laziness）和短路（short-circuiting）。
- **内部迭代**：以前对集合遍历都是通过`Iterator`或者增强`for`的方式，显示的在集合外部进行迭代，这叫做外部迭代。Stream提供了内部迭代的方式，流可以直接调用遍历方法。

当使用一个流的时候，通常包括三个基本步骤：

1. 获取一个数据源（source）；
2. 数据转换；
3. 执行操作获取想要的结果。

每次转换原有Stream对象不改变，返回一个新的Stream对象（可以有多次转换），这就允许对其操作可以像链条一样排列，变成一个管道。

## 1.3 获取流

`java.util.stream.Stream<T>`是Java 8新加入的最常用的流接口。（注：并不是一个函数式接口）

获取一个流非常简单，有以下几种常用的方式：

- 所有的`Collection`集合都可以通过`stream()`默认方法获取流；
- `Stream`接口的静态方法`of`可以获取数组对应的流。

首先，`java.util.Collection`接口中加入了default方法`stream()`用来获取流，所以其所有实现类均可获取流。

代码示例：

```java
import java.util.*;
import java.util.stream.Stream;

public class DemoGetStream {
    public class void main(String[] args) {
        // 把集合转换为Stream流
        List<String> list = new ArrayList<>();
        Stream<String> stream1 = list.stream();
        
        Set<String> set = new HashSet<>();
        Stream<String> stream2 = set.stream();
        
        Map<String, String> map = new HashMap<>();
        // 获取键，存储到一个Set集合中
        Set<String> keySet = map.keySet();
        Stream<String> stream3 = keySet.stream();
        // 获取值，存储到一个Collection集合中
        Collection<String> values = map.values();
        Stream<String> stream = values.stream();
        // 获取键值对（键与值得映射关系 entrySet）
        Set<Map.Entry<String, String>> entries = map.entrySet();
        Stream<Map.Entry<String, String>> stream4 = entries.stream();
        
        // 把数组转换为Stream流
        Stream<Integer> stream6 = Stream.of(1, 2, 3, 4, 5);
        // 可变参数可以传递数组
        Integer[] arr = {1, 2, 3, 4, 5}
        Stream<Integer> stream7 = Stream.of(arr);
        
        String[] arr2 = {"a", "b", "c"};
        Stream<String> stream8 = Stream.of(arr2);
    }
}
```

```tex
备注：of()方法的参数其实是一个可变参数，所有支持数组。
```

### 

## 1.4 常用方法

![image-20210422214654725](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210422214654725.png)

流模型的操作很丰富，这里介绍一些常用的API。这些方法可以被分成两种：

- **延迟方法**：返回值类型仍然是`Stream`接口自身类型的方法，因此支持链式调用。（除了终结方法外，其余方法均为延迟方法）。
- **终结方法**：返回值类型不再是`Stream`接口自身类型的方法，因此不再支持类似`StringBuilder`那样的链式调用。本小节中，终结方法包括`count()`和`forEach()`方法。

```tex
备注：本小节之外的更多方法，请自行参考API文档。
```

### 1.4.1 逐一处理：forEach()

虽然方法名字叫`forEach()`，但是与`for`循环中的`for-each`名称不同。

```java
void forEach(Consumer<? super T> action);
```

该方法接收一个`Consumer`接口函数，会将每一个流元素交给该函数进行处理。

该方法是一个终结方法，遍历之后就不能继续调用Stream流中的其他方法。

**复习Consumer接口**

```java
java.util.function.Consumer<T> 接口是一个消费型接口
Consumer接口中包含抽象方法void accept(T t)，意为消费一个指定泛型的数据。
```

**基本使用**：

```java
import java.util.stream.Stream;

public class DemoStreamForEach {
    public static void main(String[] args) {
        Stream<String> stream = Stream.of("张无忌", "张三丰", "周芷若");
        stream.forEach(name -> System.out.println(name));
    }
}
```

### 1.4.2 过滤：filter()

可以通过`filter()`方法将一个流转换成另一个子集流。方法签名：

```java
Stream<T> filter(Predicate<? super T> predicate);
```

该接口接收一个`Predicate`函数式接口参数（可以是一个Lambda或方法引用）作为筛选条件。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210422220245586.png" alt="image-20210422220245586" style="zoom:50%;" />

**复习Prediacate接口**

此前我们已经学习过`java.util.stream.Predicate()`函数式接口，其中唯一的抽象方法为：

```java
boolean test(T t);
```

该方法将会产生一个boolean值结果，代表指定的条件是否满足。如果结果为true，那么Stream流的`filter()`方法将会留用元素；如果结果为false，那么`filter()`方法将会舍弃元素。

**基本使用**

Stream流中的`filter()`方法基本使用的代码如：

```java
import java.util.stream.Stream;

public class DemoStreamFilter {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> newStream = Stream.filter((String name) -> {return name.startWith("张");});
        // 遍历stream流
        newStream.forEach(name -> System.out.println(name));
    }
}
```

**特点**

Stream属于管道流，只能被消费（使用）**一次**；

第一个Stream流调用完毕方法，数据就会流转到下一个Stream上；

而这时第一个Stream流已经使用完毕；

所以第一个Stream流就不能再调用方法了。



### 1.4.3 映射：map()

如果需要将流中的元素映射到另一个流中，可以使用`map()`方法。方法签名：

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

该接口需要一个`Function`函数式接口参数，可以将当前流中的T类型数据转换为另一种R类型的数据。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210422223424810.png" alt="image-20210422223424810" style="zoom:80%;" />

**复习Function接口**

此前我们已经学习过`java.util.stream.Function`函数式接口，其中唯一的抽象方法为：

```java
R apply(T t);
```

这可以将一种T类型转换成R类型，而这种转换的动作，就称为"映射"。

**基本使用**

```java
public class DemoStreamMap {
    public static void main(String[] args) {
        // 获取一个String类型的Stream流
        Stream<String> stream = Stream.of("1", "2", "3", "4");
        // 使用map()方法，把字符串类型的整数，转换（映射）为Integer类型的整数
        String<Integer> stream2 = stream.map((String s) -> {
            return Integer.parseInt(s);
        });
        // 遍历stream2流
        stream.forEach(i -> System.out.println(i));
    }
}
```



### 1.4.4 统计个数：count

正如旧集合`Collection`当中的`size()`方法一样，流提供`count()`方法来数一数其中的的元素个数：

```java
long count();
```

该方法返回一个long值代表元素个数（不再像旧集合那样是`int`值）。

代码示例：

```java
import java.util.stream.Stream;

public class DemoStreamCount {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.filter(s -> {s.startWith("张")});
        System.out.println(result.count());
    }
}
```



### 1.4.5 取用前几个：limit

`limit()`方法可以对流进行截取，只取用前n个。方法签名：

```
Stream<T> limit(long maxSize);
```

参数是一个`long`型，如果集合当前长度大于参数则进行截取；否则不进行操作。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210423182709093.png" alt="image-20210423182709093" style="zoom:80%;" />

代码示例：

```java
import java.util.stream.Stream;

public class DemoStreamLimit {
	public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.limit(2);
        System.out.println(result.count());
    }
}
```



### 1.4.6 跳过前几个：skip

如果希望跳过前几个元素，可以使用`skip()`方法获取一个截取之后的新流：

```java
Stream<T> skip(long n);
```

如果流的当前长度大于n，则跳过前n个；否则将会得到一个长度为0的空流。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210423183154528.png" alt="image-20210423183154528" style="zoom:80%;" />

代码示例：

```java
import java.util.stream.Stream;

public class DemoStreamSkip {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.skip(2);
        System.out.println(result.count());
    }
}
```



### 1.4.7 组合：concat

如果有两个流，希望合并成为一个流，那么可以使用`Stream`接口中的静态方法`concat()`：

```java
import java.util.stream.Stream;

public class DemoStreamConcat {
    Stream<String> streamA = Stream.of("张无忌");
    Stream<String> streamB = Stream.of("张翠山");
    Stream<String> result = Stream.concat(StreamA, StreamB);
}
```



## 1.5 练习：集合元素处理（传统方式）

**题目**：

现在有两个`ArrayList`集合存储队伍当中的多个成员姓名，要求使用传统的for循环（或增强for循环）**依次**进行以下若干操作步骤：

1. 第一个队伍只要名字为3个字的成员姓名；存储到一个新集合中。
2. 第一个队伍筛选之后只要前3个人；存储到一个新集合中。
3. 第二个队伍只要姓张的成员姓名；存储到一个新集合中。
4. 第二个队伍筛选之后不要前2个人；存储到一个新集合中。
5. 将两个队伍合并为一个队伍；存储到一个新集合中。
6. 根据姓名创建`Person`对象；存储到一个新集合中。
7. 打印整个队伍的`Person`对象信息。

**题解**：

```java
/*
	定义一个Person类
*/
public class Person {
    private String name;
    
    public Person() {
    }
    
    public Person(String name) {
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        // 1.创建队伍1
        ArrayList<String> list1 = new ArrayList<>();
        list.add("迪丽热巴");
        list.add("宋远桥");
        list.add("苏星河");
        list.add("石破天");
        list.add("石中玉");
        list.add("老子");
        list.add("庄子");
        list.add("洪七公");
        // 2.创建队伍2
        ArrayList<String> list2 = new ArrayList<>();
        list2.add("古力娜扎");
        list2.add("张无忌");
        list2.add("赵丽颖");
        list2.add("张三丰");
        list2.add("尼古拉斯赵四");
        list2.add("张天爱");
        list2.add("张二狗");
        // 3.第一个队伍只要名字为3个字的成员姓名；存储到一个新集合中。
        ArrayList<String> list3 = new ArrayList<>();
        for (String name : list1) {
            if (name.length() == 3) {
                list3.add(name);
            }
        }
        // 4.第一个队伍筛选之后只要前3个人；存储到一个新集合中。
        ArrayList<String> list4 = new ArrayList<>();
        int count = 0;
        for (String name : list3) {
            if (count < 3) {
                list4.add(name);
            }
            count += 1;
        }
        // 5.第二个队伍只要姓张的成员姓名；存储到一个新集合中。
        ArrayList<String> list5 = new ArrayList<>();
        for (String name : list2) {
            if (name.startsWith("张")) {
                list5.add(name);
            }
        }
        
        // 6.第二个队伍筛选之后不要前2个人；存储到一个新集合中。
        ArrayList<String> list6 = new ArrayList<>();
        count = 0;
        for (String name : list5) {
            if (count >= 2) {
                list6.add(name);
            }
            count += 1;
        }
        
        // 7.将两个队伍合并为一个队伍；存储到一个新集合中。
        ArrayList<String> list7 = new ArrayList<>();
        list7.addAll(list6);
        list7.addAll(list4);
        
        // 8.根据姓名创建Person对象；存储到一个新集合中。
        ArrayList<Person> person = new ArrayList<>();
        for (String name : list7) {
            person.add(new Person(name));
        }
        
        // 9.打印整个队伍的Person对象信息。
        System.out.println(person);
    }
}
```

## 1.6 练习：集合元素处理（Stream流方式）

**题目**：

将上一题当中的传统for循环写法更换为Stream流式处理方式。两个集合的初始内容不变，`Person`类的定义也不变。

**解答**：

等效的Stream流式处理代码：

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

public class Test {
    public static void main(String[] args) {
        // 1.创建队伍1
        ArrayList<String> list1 = new ArrayList<>();
        list.add("迪丽热巴");
        list.add("宋远桥");
        list.add("苏星河");
        list.add("石破天");
        list.add("石中玉");
        list.add("老子");
        list.add("庄子");
        list.add("洪七公");
        // 2.创建队伍2
        ArrayList<String> list2 = new ArrayList<>();
        list2.add("古力娜扎");
        list2.add("张无忌");
        list2.add("赵丽颖");
        list2.add("张三丰");
        list2.add("尼古拉斯赵四");
        list2.add("张天爱");
        list2.add("张二狗");
        
        Stream<String> stream1 = list1.stream();
        Stream<String> stream2 = list2.stream();

        Stream<String> stream3 = stream1.filter(name -> name.length()==3).limit(3);
        Stream<String> stream4 = stream2.filter(name -> name.startsWith("张")).skip(2);
        Stream<String> stream = Stream.concat(stream3, stream4);

        ArrayList<Person> person = new ArrayList<>();
        stream.forEach(name -> {
            person.add(new Person(name));
        });

        System.out.println(person);
    }
}
```



# 二 方法引用

在使用Lambda表达式的时候，我们实际上传递进去的代码就是一种解决方案：拿什么参数做什么操作。那么考虑一种情况：如果我们在Lambda中所指定的操作方案，已经有地方存在相同，那是否还有必要再写重复的逻辑？

## 2.1 冗余的Lambda场景

来看一个简单的函数式接口以应用Lambda表达式：

```java
@FunctionalInterface
public interface Printable {
    void print(String str);
}
```

在`Printable`接口当中唯一的抽象方法`print`接收一个字符串参数，目的就是为了打印显示它。

那么通过Lambda来使用它的代码很简单：

```java
public class DemoPrintSimple {
    private static void printString(Printable data) {
        data.print("Hello，World!");
    }
    
    public static void main(String[] args) {
        printString(s -> System.out.println(s));
        // 优化之后
        printString(System.out::println);
    }
    /*
    	分析：
    		Lambda表达式的目的，打印参数传递的字符串
    		把参数s，传递给了System.out对象，调用out对象中的方法prtintln对字符串进行了输出
    		注意：
    			1.System.out对象是已经存在的。
    			2.println方法也是已经存在的。
    		所以可以使用方法引用来优化Lambda表达式
    */
    
}
```

  其中`printString()`方法只管调用`Printable`接口的`print()`方法，而不管`print()`方法的具体实现逻辑会将字符串打印到什么地方去。而`main()`方法通过Lambda表达式指定了函数式接口`Printable`的具体操作方案为：**拿到String（类型可推导，所以可省略）数据后，在控制台中输出它。**

## 2.2 问题分析

这段代码的问题在于，对字符串进行控制台打印输出的操作方案，明明已经有了现成的实现，那就是`System.out`对象中的`println(String)`方法。既然Lambda希望做的事情就是调用`println(String)`方法，那何必自己手动调用呢？

## 2.3 用方法引用改进代码

能否省去Lambda的语法格式（尽管它已经相当简洁）呢？只要"引用"过去就好了：

```java
public class DemoPrintRef {
    private static void printString(Printable data) {
        data.print("Hello，World!");
    }
    
    public static void main(String[] args) {
        printString(System.out::println);
    }
}
```

请注意其中的双冒号`::`写法，这被称为"**方法引用**"，而双冒号是一种新的语法。

## 2.4 方法引用符

双冒号`::`为引用运算符，而它所在的表达式被称为**方法引用**。如果Lambda要表达的函数方案已经存在于某个方法的实现中，那么则可以通过双冒号来引用该方法作为Lambda的替代者。

### 2.4.1 语义分析

例如上例中，`System.out`对象中有一个重载的`println(String)`方法恰好就是我们所需要的。那么对于`printString()`方法的函数式接口参数，对比下面两种写法，完全等效：

- Lambda表达式写法：`s -> System.out.pritln(s);`
- 方法引用写法：`System.out::println`

第一种语义是指：拿到参数之后经Lambda之手，继而传递给`System.out.println()`方法去处理。

第二种等效写法的语义是指：直接让`System.out`中的`println()`方法来取代Lambda。两种写法的执行效果完全一样，而第二种方法引用的写法复用了已有方案，更加简介。

注：Lambda中 传递的参数 一定是方法引用中 的那个方法可以接收的类型，否则会抛出异常。

### 2.4.2 推导与省略

如果使用Lambda，那么根据"**可推导就是可省略**"的原则，无需指定参数类型，也无需指定的重载形式**————**它们都将被自动推导，而如果使用方法引用，也是同样可以根据上下文进行推导。

函数式接口时Lambda的基础，而方法引用是Lambda的孪生兄弟。

## 2.5 通过对象名引用成员方法

这是最常见的一种用法，与上例相同。如果一个类中已经存在了一个成员方法：

```java
public class MethodRefObject {
    public void printUpperCase(String str) {
        System.out.println(str.toUpperCase());
    }
}
```

函数式接口仍然定义为：

```java
@FunctionalInterface
public interface Printable {
    void print(String str);
}
```

那么当需要使用这个`printUpperCase()`成员方法来替代`Printable`接口的Lambda的时候，已经具有了`MethodRefObject`类的对象实例，则可以通过对象名引用成员方法，代码为：

```java
/*
	通过对象名引用成员方法
	使用前提是对象名是已经存在的，成员方法也是已经存在的
	就可以使用对象名来引用成员方法
*/
public class Test {
    // 定义一个方法，方法的参数传递Printable接口
    public static void printString(Printable p) {
        p.print("Hello");
    }

    public static viod main(String[] args) {
        MethodRefObject obj = new MethodRefObject();
        /*
    		方式一：使用Lambda表达式
    	*/
        printString(s -> obj.printUpperCase(s));
        
        /*
        	方式二：使用方法引用
        */
        printString(obj::printUpperCase)
    }
}
```

## 2.6 通过类名称引用静态方法

由于在`java.lang.Math`类中已经存在了静态方法`abc()`，所以当我们需要通过Lambda来调用该方法时，有两种写法。首先是函数式接口：

```java
@FunctionalInterface
public interface Calcable {
    int calc(int num);
}
```

第一种写法是使用Lambda表达式：

```java
public class DemoLambda {
    private static void method(int num, Calcable lambda) {
        System.out.println(lambda.calc(num));
    }
    public static void main(String[] args) {
        method(10, n -> Math.abs(n));
    }
}
```

但是使用方法引用的更好写法是：

```java
public class DemoMethodRef {
    private static void method(int num, Calcable lambda) {
        System.out.println(lambda.calc(num));
    }
    public static void main(String[] agrs) {
        method(10, Math::abs);
    }
}
```

## 2.7 通过super引用成员方法

如果存在继承关系，当Lambda中需要出现`super`调用时，也可以使用方法引用进行替代。首先是函数式接口：

```java
@FunctionalInterface
public interface Greetable {
    void greet();
}
```

然后是父类`Human`的内容：

```java
public class Human {
    public void sayHello() {
        System.out.pritnln("Hello!");
    }
}
```

最后是子类`Man`的内容，其中使用了Lambda的写法：

```java
public class Man extends Human {
    @Override
    public void sayHello() {
        System.out.println("大家好，我是Man!");
    }
    
    // 定义方法method，参数传递Greetable接口
    public void method(Greetable g) {
        g.greet();
    }
    
    public void show() {
        // 调用method方法，方法的参数Greetable是一个函数式接口，所以可以传递Lambda
        method(() -> {
            // 创建父类Human对象
            Human human = new Human();
            // 调用父类的sayHello()方法
            human.sayHello();
        });
        
        /*
        	因为有字符类关系，所以存在一个关键字super，代表父类，所以我们可以直接使用super调用父类的成员方法。
        */
        method(() -> {super.sayHello()});
        
        /*
        	使用super引用类的成员方法
        	super是已经存在的
        	父类的成员方法sayHello也是已经存在的
        	所以我们可以直接使用super引用父类的成员方法
        */
        method(super::sayHello);
    }
}
```

## 2.8 通过this引用成员方法

`this`代表当前对象，如果需要引用的方法就是当前类中的成员方法，那么可以使用**`this::成员方法`**的格式来使用方法引用。首先是简单的函数式接口：

```java
@FunctionalInterface
public interface Richable {
    void buy();
}
```

下面是一个丈夫`Husband`类：

```java
public class Husband {
    public void buyHouse() {
        System.out.println("买房子");
    }
    private void marry(Richable lambda) {
        lambda.buy();
    }
    public void beHappy() {
        marry(() -> this.buyHouse());
    }
}
```

开心方法`beHappy()`调用了结婚方法`marry()`，后者的参数为函数式接口`Richable`，所以需要一个Lambda表达式。但是如果这个Lambda表达式的内容已经在本类当中存在了，则可以对`Husband`丈夫类进行修改：

```java
public class Husband {
    public void buyHouse() {
        System.out.println("买房子");
    }
    private void marry(Richable lambda) {
        lambda.buy();
    }
    // 使用 方法引用 进行Lambda表达式优化
    public void beHappy() {
        marry(() -> this::buyHouse);
    }
}
```

## 2.9 类的构造器引用

由于构造器的名称与类名称完全一样，并不固定。所以构造器引用使用`类名称::new`的格式表示。首先是一个简单的`Person`类：

```java
public class Person {
    private String name;
    public Person(String name) {
        
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

然后是用来创建`Person`对象的函数式接口：

```java
@FunctionalInterface
public interface PersonBuilder {
    // 定义一个方法，根据传递的姓名，创建Person对象返回
    Person builderPerson(String name);
}
```

测试类：

```java
public class Test {
    // 定义一个方法，参数传递姓名和PersonBuilder接口，方法中通过姓名创建Person对象
    public void printName(String name, PersonBuilder pb) {
        Person person = pb.builderPerson(name);
        System.out.println(person.getName());
    }
    
    public static void main(String[] args) {
        printName("张三", s -> {
            return Person(s);
        });
        
        /*
        	使用 方法引用 优化Lambda表达式
        	构造方法 new Person(String name)已知
        	创建对象已知 new
        	就可以使用Person引用new创建对象
        */
        printName("张三", Person::new);
    }
}
```

## 2.10 数组的构造器引用

数组也是`Object`的子类对象，所以同样具有构造器，只是语法稍有不同。如果对应到Lambda的使用场景中时，需要一个函数式接口：

```java
@FunctionalInterface
public interface ArrayBuilder {
    int[] buildArray(int length);
}
```

在应用该接口的时候，可以通过Lambda表达式：

```java
public class DemoArrayInitRef {
    private static int[] initArray(int length, ArrayBuilder builder) {
        return builder.buildArray(length);
    }
    
    public static void main(String[] args) {
        int[] array = initArray(10, length -> new int[length]);
    }
}
```

但是更好的写法是使用数组的构造器引用：

```java
public class DemoArrayInitRef {
    private static int[] initArray(int length, ArrayBuilder builder) {
        return builder.buildArray(length);
    }
    
    public static void main(String[] args) {
        int[] array = initArray(10, int[]::new);
    }
}
```

