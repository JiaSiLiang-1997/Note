# 一 Lambda表达式

## 1.1 函数式编程思想概述

在数学中，**函数**就是有输入量、输出量的一套计算方案，也就是"拿什么东西做什么事情"。相对而言，面向对象过分强调"必须通过对象的形式来做事情"，而函数式思想则尽量忽略面向对象的复杂语法——**强调做什么，而不是以什么形式做**。

面向对象思想：

​		做一件事，必须找一个能解决这个事情的对象，调用对象的方法，完成事情。

函数式编程思想：

​		只要能获取到结果，谁去做的，怎么做的都不重要，重视的是结果，不重视过程。

## 1.2 冗余的Runnable代码

### 1.2.1 传统写法

当需要启动一个线程去完成任务时，通常会通过`java.lang.Runnable`接口来定义任务内容，并使用`java.lang.Thread`类来启动该线程。代码如下：

```java
public class ThreadMain {
    public static void main(String[] args) {
        // 匿名内部类
        Runnable task = new Runnable() {
            @Override
            public void run() {
                System.out.println("多线程任务执行！");
            }
        };
        new Thread(task).start(); // 启动线程
    }
}
```

本着"一切皆对象"的思想，这种做法是无可厚非的：首先创建一个`Runnable`接口的匿名内部类对象来指定任务内容，再将其交给一个线程来启动。

### 1.2.2 代码分析

对于`Runnable`的匿名内部类用法，可以分析出几点内容：

- `Thread`类需要`Runnable`接口作为参数，其中的抽象`run()`方法是用来指定线程任务内容的核心；
- 为了指定`run()`的方法体，**不得不**需要`Runnable`接口的实现类；
- 为了省去定义一个`Runnable`接口的实现类，**不得不**使用匿名内部类；
- 必须覆盖重写抽象`run()`方法，所以方法名称、方法参数、方法返回值**不得不**再写一遍，且不能写错；
- 而实际上，**似乎只有方法体才是关键所在**。

## 1.3 编程思想转换

关注点：**做什么，而不是怎么做！**

我们真的希望创建一个匿名内部类对象吗？不，我们只是为了做这件事情而而**不得不**创建一个对象。我们真正希望做的事情是：将`run()`方法体内的代码传递给`Thread`类知晓。

**传递一段代码**——这才是我们真正的目的。而创建对象只是受限于面向对象语法而不得不采取的一种手段方式。那么有没有更加简单的办法？如果我们将关注点从"怎么做"回归到"做什么"的本质上，就会发现只要能够更好地到达目的地，过程与形式其实并不重要。

## 1.4 Lambda的写法

借助Java 8 的全新语法，上述`Runnable`接口的匿名内部类写法可以通过更简单的Lambda表达式达到等效：

```java
public class LambdaMain {
    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println("多线程任务执行！");
        	}).start();
    }
}
```

这段代码和刚才的执行效果是完全一样的，可以在1.8或更高的编译级别下通过。从代码的语义中可以看出：我们启动了一个线程，而线程任务的内容以一种更加简洁的形式被指定。

不再有"不得不创建接口对象"的束缚，不再有"抽象方法覆盖重写"的负担，就是这么简单！

## 1.5 匿名内部类的好处与弊端

一方面，匿名内部类可以帮助我们**省去实现类的定义**；另一方面，匿名内部类的语法——**确实太复杂了！**

**语义分析**：

仔细分析该代码中的语义，`Runnable`接口只有一个`run()`方法的定义；

- `public abstract void run()`；

即制定了一种做事情的方案（其实就是一个函数）：

- **无参数**：不需要任何条件即可执行该方案。
- **无返回值**：该方案不产生任何结果。
- **代码块**（方法体）：该方案的具体执行步骤。

同样的语义体现在Lambda语法中，要更加简单：

`() -> System.out.println("多线程任务执行！")`

- 前面的小括号即`run()`方法的参数（无），代表不需要任何条件；
- 中间的一个箭头代表将前面的参数传递给后面的代码；
- 后面的输出语句即业务逻辑代码。

## 1.6 Lambda标准格式

Lambda省去面向对象的条条框框，格式由**3个部分**组成：

- 一些参数；
- 一个箭头；
- 一段代码；

Lambda表达式的**标准格式**为：

```java
(参数类型 参数名称) -> { 代码语句 }
```

格式说明：

- 小括号内的语法与传统方法参数列表一致：无参数则留空；多个参数则用逗号分隔；
- `->`是新引入的语法格式，代表指向动作;
- 大括号内的语法与传统方法体要求基本一致。

## 1.7 练习：使用Lambda标准格式（无参无返回）

**题目**：

给定一个厨子`Cook`接口，内含唯一的抽象方法`makeFood()`，且无参数，无返回值。如下：

```java
public interface Cook {
    void makeFood();
}
```

在下面的代码中，请使用Lambda的**标准格式**调用`invokeCook()`方法，打印输出"吃饭啦！"字样：

```java
public class InvokeCookMain {
    public static void main(String[] args) {
        // TODO 请在此使用Lambda【标准格式】调用invokeCook方法
        
        // 调用invokeCook方法，参数是Cook接口，传递Cook接口的匿名内部类对象
        // 传统方式
        invokeCook(new Cook() {
            @Override
            public void makeFood() {
                System.out.println("吃饭啦！");
            }
        });
        
        // Lambda表达式，简化匿名内部类的书写
         invokeCook(() -> {
            System.out.println("吃饭啦！");
         });
    }
    
    private static void invokeCook(Cook cook) {
        cook.makeFood();
    }
}
```

## 1.8 练习：使用Lambda标准格式（有参无返回）

```tex
需求：
	使用数组存储多个Person对象；
	对数组中的Person对象使用Arrays的sort()方法通过年龄进行升序排序。
```

下面举例演示`java.util.Comparator<T>`接口的使用场景代码，其中的抽象方法定义为：

- `public abstract int compara(T o1, T o2);`

当需要对一个对象数组进行排序时，`Arrays.sort()`方法需要一个`Comparator`接口实例来指定排序的规则。假设有一个`Person`类，含有`String name`和`int age`两个成员变量。

```java
public class Person {
    private String name;
    private int age;
    
    // 省去构造器、toString()与Getter/Setter方法
}
```

**传统写法**：

```java
public class ArraysMain {
    public static void main(String[] args) {
        // 使用数组存储多个Person对象
        Person[] arr = {
            new Person("迪丽热巴", 18);
            new Person("古力娜扎", 28);
            new Person("马尔扎哈", 38);
        };
        
        // 对数组中的Person对象使用Arrays的sort()方法通过年龄进行升序（前-后）排序
        Arrays.sort(arr, new Comparator<Persong>() {
            @Override
            public int compare(Person o1, Person o2) {
                return o1.getAge() - o2.getAge();
            }
        })
    }
}
```

**Lambda标准格式**：

```java
public class ArraysMain {
    public static void main(String[] args) {
        // 使用数组存储多个Person对象
        Person[] arr = {
            new Person("迪丽热巴", 18);
            new Person("古力娜扎", 28);
            new Person("马尔扎哈", 38);
        };
        
        // 对数组中的Person对象使用Arrays的sort()方法通过年龄进行升序（前-后）排序
        Arrays.sort(arr, (Person o1, Person o2) -> {
            return o1.getAge() - o2.getAge();
        });
    }
}
```

## 1.9 练习：使用Lambda标准格式（有参有返回）

**题目**：

给定一个计算器`Calculate`接口，内含抽象方法`calc()`可以将两个int数字相加得到和值：

```java
public interface Calculate {
    int calc(int a, int b);
}
```

在下面的代码中，请使用Lambda的**标准格式**调用`invokeCalc()`方法，完成120和130的相加计算：

```java
public class InvokeCalc {
    public static void main(String[] args) {
        // 传统方法
        invokeCalc(10, 20, new Calculator() {
            @Override
            public int calc(int a, int b) {
                return a + b;
            }
        });
        
        // Lambda标准格式
        invokeCalc(10, 20, (int a, int b) -> {
            return a + b;
        });
    }
    public static void invokeCalc(int a, int b, Calculator calculator) {
        int result = calculator.calc(a, b);
        System.out.println("结果是：" + result);
    }
}
```

## 1.10 Lambda省略格式

**可推导即可省略**

Lambda强调的是"做什么"而不是"怎么做"，所以凡是可以根据上下文推导得知的信息，都可以省略。例如上述还可以使用Lambda的省略写法：

```java
public static void main(String[] args) {
    invokeCalc(120, 130, (a, b) -> a + b);
}
```

**省略规则**

在Lambda标准格式的基础上，可以使用省略写法的规则为：

1. 小括号内参数的类型可以省略；
2. 如果小括号内**有且仅有一个参数**，则小括号可以省略；
3. 如果大括号内****