# 一 Scanner类

Scanner类的功能：可以实现键盘输入数据，到程序当中。



引用类型的一般使用步骤：

​	1. 导包

```java
import 包路径.包名
```

​	如果需要使用的目标类，和当前类位于同一个包下，则可以省略导包语句不写。

​	注意：只有java.lang包下的内容不需要导包，其他的包都需要import。



​	2. 创建

```java
类名称 对象名 = new 类名称();
```

​	

​	3. 使用

```java
对象名.成员方法名();
```



代码示例：

```java
// 1.导包
import java.lang.Scanner;

public class Example {
    public static void main(String[] args) {
        // 2.创建
        Scanner sc = new Scanner(System.in); // 注：System.in 代表从键盘输入
        
        // 3.使用
        // 获取键盘输入的一个int数字：
        int num = sc.nextInt();
        System.out.println("输入的int数字是：", + num);
        
        // 获取键盘输入的一个字符串：
        String str = sc.next();
        System.out.println("输入的字符串是：", + str);
    }
}
```



注意：其实从键盘输入的都是字符串，只是 nextInt() 将输入的字符串进行了整数转换。



Scanner练习题一：

​		题目：键盘输入两个int数字，并且求出和值。

代码如下：

```java
public class Sum {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.println("请输入第一个数字：");
        int a = sc.nextInt();
        System.out.println("请输入第二个数字：");
        int b = sc.nextInt();
        
        int result = a + b;
        System.out.println("结果是：" + result);
    }
}
```



Scanner练习题二：

​		题目：键盘输入三个int数字，然后求出其中的最大值。

代码如下：

```java
public class SumThree {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.println("请输入第一个数字：");
        int a = sc.nextInt();
        System.out.println("请输入第二个数字：");
        int b = sc.nextInt();
        System.out.println("请输入第三个数字：");
        int c = sc.nextInt();
        
        // 求出三个数中的最大值，方法很多种。
        d = a > b ? a : b;
        maxValue = d > c ? d : c;
        System.out.println("最大值是：" + maxValue);
    }
}
```



# 二 Random类

Random类用来生成随机数字，使用三步骤：

1. 导包

```java
import java.lang.Random
```

2. 创建

```java
Random r = new Random(); // 小括号当中留空即可
```

3. 使用

```java
// 获取一个随机的int数字（范围是int所有范围，有）
int num = r.nextInt();
// 获取一个随机的int数字（参数代表了范围，左闭右开区间）：
int num = r.nextInt(3);
```

代码示例：

```java
public class Example1 {
	public static void main(String[] args) {
        Random r = new Random();
        
        int num = r.nextInt();
        System.out.println("随机数是" + num);
    }
}


public class Example2 {
    public static void main(String[] args) {
        Random r = new Random();
        
        for(int i = 0; i < 100; i++) {
            int num = r.nextInt(10);
            System.out.println("随机数是：" + num);
        }
    }
}
```



练习题一：

题目要求：根据int变量n的值，来获取随机数字，范围是[1, n]，可以取到1也可以取到n。

思路：

1. 定义一个int变量n，随意赋值
2. 要使用Random：三个步骤，导包、创建、使用
3. 如果要写10，那么就是0~9，然而想要的是1~10，可以发现：整体+1即可。
4. 打印随机数字

```java
public class Example {
    public static void main(String[] args) {
		Random r = new Random();
        
        // 本来是0~9的随机数，整体+1就变成了1~10的随机数。
        int num = r.nextInt(10) + 1;
        System.out.println("结果是：" + num);
    }
}
```



练习题二:

题目要求：用代码模拟猜数字的小游戏。

思路

1. 首先需要产生一个随机数字，并且一旦产生不再变化。用Random的nextInt()方法。

2. 需要键盘输入，所以用到了Scanner。

3. 获取键盘输入的数字，用Scanner当中的nextInt()方法。

4. 已经得到了两个数字，判断（if）一下：

   ​		如果太大了，提示太大，并且重试。

   ​		如果太小了，提示太小，并且重试。

   ​		如果猜中了，游戏结束。

5. 如果重试就是再来一次，循环次数不确定，用while(true)。

```java
public class Example {
    public static void main(String[] args) {
        // 产生一个随机数字 1~100
        Random r = new Random();
        int randomNum = r.nextInt(99) + 1;
        
        
        // 开始猜数字
        while (True) {
            // 提示输入一个数字
            System.out.println("请输入一个整数：");           
            inputNum = new Scanner(System.in).nextInt();
            
            // 判断输入的数字是否正确
            if (inputNum == randomNum) {
                System.out.println("猜中答案，游戏结束！");
                break;
            }
            else if (inputNum > randomNum) {
                System.out.println("输入答案太大了，请重新输入！");
            }
            else {
                System.out.println("输入答案太小了，请重新输入！")
            }
        }       
    }
}
```



# 三 Object类

## 3.1 概述

`java.lang.Object`类是Java语言中的根类，即所有类的父类。它中描述的所有方法子类都可以使用。在对象实例化的时候，最终找到的父类就是`Object`。

如果一个类没有特别指定父类，那么默认则继承自`Object`类。例如：

```java
public class MyClass /*extends Object*/ {
    
}
```

主要方法：

```java
public String toString(); // 返回该对象的字符串表示
public boolean equals(Object obj); // 指示其他某个对象是否与此对象“相等”。
```

## 3.2 toString方法

- `public String toString()`：返回该对象的字符串表示。

`toString()`方法返回该对象的字符串表示，其实该字符串内容就是对象的类型+@+内存地址值。

代码示例：

```java
public class Person {
    private String name;
    private int age;
    public Person() {
        
    }
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    /*
    	 直接打印对象的地址值没有意义，需要重写Object类中的toString方法。
    	 打印对象的属性(name, age)，需要重写toString方法
    */
    @Override
    public String toString() {
        return "Person{name=+" + name + "+，age=" + age + "}"
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        /*
        	Person类默认继承了Object类，所以可以使用Object类中的toString()方法
        */
        Person p = new Person("张三", 20);
        System.out.println(p.toString()); // 输出：所在包.类名@地址值(十六进制)
        // 直接打印对象的名字，其实就是调用对象的toString()方法
        System.out.println(p); // 输出：同上
        // 重写toString()方法后
        System.out.pritnln(p); // 输出：Person{name=张三，age=20}  
    }
}
```

看一个类是否重写了toString，直接打印这个类的对象即可，如果没有重写toString方法，那么打印的是对象的地址值。

## 3.3 equals方法

- `public boolean equals(Object obj)`：指示其他某个对象是否与此对象"相等"。

调用成员方法`equals()`并指定参数为另一个对象，则可以判断这两个对象是否是相同的。这里的"相同"有默认和自定义两种方法。

**默认地址比较**

如果没有覆盖重写`equals()`方法，那么`Object类`中默认进行`==`运算符的对象地址比较，只要不是同一个对象，结果必然为`false`。

**对象内容比较**

如果希望进行对象的内容比较，即所有或指定的部分成员变量就判定两个对象相同，则可以覆盖重写`equals()`方法。

代码示例：

```java
import java.util.Objects;

public class Person {
    private String name;
    private int age;
    public Person() {
        
    }
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName() {
        this.name = name; 
    }
    public int getAge() {
        return age;
    }
    public void setAge() {
        
    }
    /*
    	Object类的equals方法，默认比较的是两个对象的地址值，没有意义。
    	所以我们要重写equals方法，比较两个对象的属性(name, age)
    	存在的问题：
    		隐含着一个多态，多态的弊端：无法使用子类特有的内容（属性和方法）
    		Object obj = p2 = new Person("李四", 22)
    	解决：
    		可以使用向下转型（强制转型），把obj的类型从Object强制转换成Person
    */
    @Override
    public boolean equals(Object obj) {
        // 增加一个判断，防止传递的参数是this本身
        if (obj == this) return true;
        // 增加一个判断，防止传递的参数为null
        if (obj == null) return false;
        // 增加一个判断，防止类型转换时报错，ClassCastException
        if (obj instanceof Person) {
            // 先使用向下转型，把obj转换成Person
            Person p = (Person)obj;
            // 比较两个对象的属性，一个对象是this(方法调用者)，另一个对象是obj(参数)。
            if (this.name == obj.name && this.age == obj.age) return true;
            else return false;
        }
        // 不是Person类型，直接返回false。
        return false;
    }
    /*
    	getClass() != obj.getClass() 使用反射技术，判断obj是否是Person类型
    	等价于：obj instanceof Person
    */
}
```

```java
public class Main {
    public static void main(String[] args) {
        /*
        	equals()源码：
        		public boolean equals(Object obj) {
        			return (this == obj);
        		}
        		参数：Object obj 可以传递任意的对象
        		== 比较运算符，返回的是一个boolean，不是true就是false。
        		基本数据类型：比较的值
        		引用数据类型：比较的是两个对象的地址值
        		this是谁？哪个对象调用的方法，方法中的this就是哪个对象。
        		obj是谁？传递的参数是哪个对象，方法中的obj就是哪个对象。
        */
        Person p1 = new Person("张三", 20);
        Person p2 = new Person("张三", 20);
        // 不覆盖重写equals()方法
        System.out.println(p1.equals(p2)); // 输出：false
        // 覆盖重写equals()方法
        System.out.println(p1.equals(p2)); // 输出：true
        
    }
}
```

注意事项：

```java
public class Main {
    public static void main(String[] args) {
        // String s1 = "abc";
        String s1 = null;
        String s2 = "abc";
        boolean b1 = s1.equals(s2); // NullPointerException null是不能调用方法的。
        
        /*
        	Object类的equals()方法：对两个对象进行比较，防止空指针异常。
        	public static boolean equals(Object a, Object b) {
        		return (a == b) || (a != null && a.equals(b));
        	}
        */
        boolean b2 = Object.equals(s1, s2);
        System.out.println(b2); // 输出：false
    }
}
```



# 四 日期时间类

## 4.1 Date类

**概述**

`java.util.Date`类表示特定的瞬间，精确到毫秒（千分之一秒）。

毫秒值的作用：可以把日期转换为毫秒进行计算，计算完毕再把毫秒转换为日期。

继续直阅Date类的描述，发现Date拥有多个构造函数，只是部分已经过时，但是其中有未过时的构造函数可以把毫秒值转成日期对象。

- `public Date()`：分配`Date`对象并初始化此对象，以表示分配它的时间（精确到毫秒）。
- `public Date(long Data)`：分配`Date`对象并初始化此对象，以表示自从标准基准时间（称为"历元（`epoch`）"，即1970年1月1日00:00:00 GMT）以来的指定毫秒数。

注意：由于我们处于东八区，所以我们的基准时间为1970年1月1日8时0分0秒。

代码示例：

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(System.currentTimeMillis()); // 获取当前系统到1970年1月1日00:00:00经历了多少毫秒
    }
}
```

**常用方法**

```java
public class Main {
    public static void main(String[] args) {

    }
    public static void show01() {
        /*
        	Date类的空参构造方法
        	Date()获取当前系统的日期和时间
        */
        Date date = new Date(); 
        System.out.println(date); // 输出：星期 月 日 hh:mm:ss CST 年
    }
    public static void show02() {
        /*
        	Date类的带参构造方法
        	Date(long date)：传递毫秒值，把毫秒值转换为Date日期
        */
        Date date = new Date(0L);
        System.out.println(date); // 输出： Thu Jan 01 08:00:00 CST 1970
    }
    public static void show03() {
        /*
        	long getTime()：把日期转换为毫秒值，相当于System.currentTimeMillis()
        	返回自 1970 年 1 月 1 日 00:00:00 GMT 以来 此Date对象表示的毫秒数
        */
        Date date = new Date();
        long time = date.getTime();
    }
}
```



## 4.2 DateFormat类&SimpleDateFormat类

` java.lang.DateFormat` 是日期/时间格式化子类的抽象类，我们通过这个类可以完成日期和文本之间的转换，也就是可以在`Date`对象与`String`对象之间进行来回转换。

- **格式化**：按照指定的格式，从`Date`对象转换为`String`对象。
- **解析**：按照指定的格式，从`String`对象转换为`Date`对象。

### 4.2.1 构造方法

由于`DateFormat`为抽象类，不能直接使用，所以需要常用的子类`java.text.SimpleDateFormat`。这个类需要一个模式（格式）来指定格式化或解析的标准。构造方法为：

- `public SimpleDateFormat(String pattern)`：用给定的模式和默认语言环境的日期格式符号构造`SimpleDateFormat`。

参数`pattern`是一个字符串，代表日期时间的自定义格式。

### 4.2.2 格式规则

常用的格式规则为：

| 字母 | 日期或时间元素           |       表示        |                 示例                  |
| :--: | :----------------------- | :---------------: | :-----------------------------------: |
|  G   | Era 标志符               |       Text        |                  AD                   |
|  y   | 年                       |       Year        |               1996；96                |
|  M   | 年中的月份               |       Month       |              July；Jul;               |
|  w   | 年中的周数               |      Number       |                  27                   |
|  W   | 月份中的周数             |      Number       |                   2                   |
|  D   | 年中的天数               |      Number       |                  189                  |
|  d   | 月份中的天数             |      Number       |                  10                   |
|  F   | 月份中的星期             |      Number       |                   2                   |
|  E   | 星期中的天数             |       Text        |             Tuesday；Tue              |
|  a   | Am/pm 标记               |       Text        |                  PM                   |
|  H   | 一天中的小时数（0-23）   |      Number       |                   0                   |
|  k   | 一天中的小时数（1-24）   |      Number       |                  24                   |
|  K   | am/pm 中的小时数（0-11） |      Number       |                   0                   |
|  h   | am/pm 中的小时数（1-12） |      Number       |                  12                   |
|  m   | 小时中的分钟数           |      Number       |                  30                   |
|  s   | 分钟中的秒数             |      Number       |                  55                   |
|  S   | 毫秒数                   |      Number       |                  978                  |
|  z   | 时区                     | General time zone | Pacific Standard Time；PST；GMT-08:00 |
|  Z   | 时区                     | RFC 822 time zone |                 -800                  |

代码示例：

```java
/*
	java.text.DateFormat：是日期/时间格式化子类的抽象类
	作用：
		格式化（日期—>文本）、解析（文本—>日期）
	成员方法：
		String format(Date date)：按照指定的模式，把Date日期，格式化为符合模式的字符串
		Date parse(String source)：把符合模式的字符串，解析为Date日期
	DateFormat类是一个抽象类，无法直接创建对象使用，可以使用DateFormat类的子类
	构造方法：
		SimpleDateFormat(String pattern)：用给定的模式和默认语言环境的日期格式符号构造 SimpleDateFormat。
		参数：
			String pattern：传递指定的模式
		模式：区分大小写(y、Y、m、M、......)
			写对应的模式，会把模式替换为对应的日期和时间
			"yyyy-MM-dd HH:mm:ss"
		注意：
			模式中的字母不能更改，连接模式的符号可以改变。
			例如："yyyy年MM月dd日 HH时mm分ss秒"
*/
public class Main {
    public static void main(String[] args) {
        
    }
    public static void show01() {
        /*
        	使用DateFormat类中的方法format，把日期格式化为文本
        	使用步骤：
				1.创建SimpleDateFormat对象，构造方法中传递指定的模式
				2.调用SimpleDateFormat对象中的方法format()，按照构造方法中指定的模式，把Date日期格式化为符合模式的字符串（文本）。
        */
        // 1.创建对象
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
        // 2.调用方法
        Date date = new Date();
        String d = sdf.format(date);
        System.out.pritnln(d); // 输出：xxxx年xx月xx日 xx时xx分xx秒  
    }
    public static void show02() throws ParseException {
        /*
        	使用DateFormat类中的方法parse，把文本解析为日期
        	使用步骤：
        		1.创建SimpleDateFormat对象，构造方法中传递指定的模式
        		2.调用SimpleDateFormat对象中的方法parse()，把符合构造方法中的模式的字符串，解析为Date日期。
        	注意事项：
        		parse()声明了一个异常：ParseException，如果字符串和构造方法的模式不一样，那么程序就会抛出此异常。 
        		调用了一个抛出异常的方法，就必须处理这个异常。
        			1.throws继续抛出这个异常。
        			2.要么try catch自己处理这个异常。
        */
        // 1.创建对象
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
        // 2.调用对象
        String timeStr = "yyyy年MM月dd日 HH时mm分ss秒";
        Date date = sdf.parse(timeStr);
        System.out.pritnln(d); // 输出：Sun Aug 08 00:00:00 CST 0000 
    }
}
```



# 五 Calendar

## 5.1 概念

`java.util.Calendar`是日历类，在`Date`后出现，替换掉了许多`Date`的方法。该类将所有可能用到的时间信息封装为静态成员变量，方便获取。日历类就是方便获取各个时间属性的。

## 5.2 获取方式

`Calendar`为抽象类，由于语言敏感性，`Calendar`类在创建对象时并非直接创建，而是通过静态方法创建，返回子类对象，如下：

`Calendar`静态方法：

- `public static Calendar getInstance()`：使用默认时区和语言环境获得一个日历。

代码示例：

```java
public class Main {
    public static void main(String[] args) {
        Calendar c = Calendar.getInstance(); // 多态
        System.out.println(c);
    }
}
```

## 5.3 常用方法

根据`Calendar`类的API文档，常用方法有：

- `public int get(int field)`：返回给定日历字段的值。
- `public void set(int field, int value)`：将给定的日历字段设置为给定值。
- `public abstract void add(int field, int amount)`：根据日历的规则，为给定的日历字段添加或减去指定的时间量。
- `public Date getTime()` ：返回一个表示此`Calendar`时间值（从历元到现在的毫秒偏移量）的Date对象。

`Calendar`类中提供很多成员变量，代表给定的日历字段：

| 字段值           | 含义         |
| ---------------- | ------------ |
| YEAR = 1         | 年           |
| MONTH = 2        | 月           |
| DATE = 5         | 月中的某一天 |
| DAY_OF_MONTH = 5 | 月中的某一天 |
| HOUR = 10        | 时           |
| MINUTE = 12      | 分           |
| SECOND = 13      | 秒           |

代码示例：

```java
public class Main {
    public static void main(String[] args) {
    	show01();
        show02();
        show03();
        show04();
    }
    public static void show01() {
        /*
        	public int get(int field); 返回给定日历字段的值
        	参数：传递指定的日历字段(YEAR、MONTH、......)
        	返回值：日历字段代表的具体值
        */
        // 1.获取对象
        Calendar c = Calendar.getInstance();
        // 2.调用get()方法
        int year = c.get(Calendar.YEAR);
        System.out.println(year); // 输出：2021
        int month = c.get(Calendar.MONTH);
        System.out.println(month); // 输出：3（西方的月份：0-11，东方：1-12）
        int date = c.get(Calendar.DAY_OF_MONTH);
        System.out.println(day); // 输出：5
    }
    public static void show02() {
        /*
        	public void set(int field, int value); 将给定的日历字段设置为给定值
        	参数：
        		int field: 传递指定的日历字段(YEAR、MONTH、......)
        		int value: 给指定字段设置的值
        	注意：这个方法可以重载！！！
        */
        // 1.获取对象
        Calendar c = Calendar.getInstance();
        // 2.调用set()方法
        c.set(Calendar, YEAR, 9999);
        // 3.调用get()方法
        int year = c.get(Calendar.YEAR);
        System.out.println(year); // 输出：9999
    }
    public static void show03() {
        /*
        	public abstract void add(int field, int amount); 根据日历的规则，为给定的日历字段添加或减去指定的时间量。
        	参数：
        		int field: 传递指定的日历字段(YEAR、MONTH、......)
        		int amout: 增加/减少的值
        */
        // 1.获取对象
        Calendar c = Calendar.getInstance();
        // 2.调用add()方法
        c.add(Calendar, YEAR, 2); // 把年份增加2年
        // 3.调用get()方法
        int year = c.get(Calendar.YEAR);
        System.out.println("输出"); // 输出：2023
    }
    public static void show04() {
        /*
        	public Date getTime(); 返回一个表示此Calendar时间值（从历元到现在的毫秒偏移量）的Date对象。
			意义：把日历对象抓换为日期对象
        */
        // 1.获取对象
        Calendar c = Calendar.getInstance();
        // 2.调用getTime()方法，将日历对象转换为日期对象。
        Date date = c.getTime();
        System.out.println(date); // 输出：Sun Aug 08 16:49:37 CST 2021
    }
}
```



# 六 System类

`java.lang.System`类中提供了大量的静态方法，可以获取与系统相关的信息或系统级操作，在`System`类的API文档中，常用的方法有：

- `public static long currentTimeMillis()`：返回以毫秒为单位的当前时间。
- `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`：将数组中指定的数据拷贝到另一个数组中。

## 6.1 currentTimeMillis方法

实际上，currentTimeMills方法就是获取当前系统时间与1970年01月01日00:00点之间的毫秒差值。

代码示例：

```java
import java.util.Date;

public class SystemDemo {
    public static void main(String[] args) {
        // 获取当前时间毫秒值
        System.out.println(System.currentTimeMillis()); // 输出：1516098531144
    }
}
```

## 6.2 arraycopy方法

代码示例：

```java
/*
	public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length); 将数组中指定的数据拷贝到另一个数组中。
	参数：
		str: 源数组
		srcPos: 源数组中的起始位置
		dest: 目标数组
		dest: 目标数组中的起始位置
		length: 要复制的数组元素的数量
*/
public class SystemDemo {
    public static void main(String[] args) {
        // 定义源数组
        int src = {1, 2, 3, 4, 5};
        // 定义目标数组
        int dest = {6, 7, 8, 9, 10};
        // 使用arraycopy()方法
        System.arraycopy(src, 0, dest, 0, 3);
        System.out.println(dest); //输出：{1, 2, 3, 9, 10}
    }
}
```



# 七 StringBuilder类

## 7.1 字符串拼接问题

由于`String`类的对象内容不可改变，所以每当进行字符串拼接时，总是会在内存中创建一个新的对象。例如：

```java
public class StringDemo {
    public static void main(String[] args) {
        String s = "Hello";
        s += "World";
        System.out.println(s);
    }
}
```

在API中对`String`类有这样的描述：字符串是常量，他们的值在创建后不能被更改。

注意：**字符串底层就是一个被`final`修饰的数组！！！**

根据这句话分析我们的代码，其实总共产生了三个字符串，即`Hello`、`World`和`HelloWorld`。引用变量s首先指向`Hello`对象，最终指向拼接出来的新字符串对象，即`HelloWord`。

## 7.2 StringBuilder概述

`java.lang.StringBuilder`类：字符串缓冲区，可以提高字符串的效率。

<center><a href="https://imgtu.com/i/cM1tit"><img src="https://z3.ax1x.com/2021/04/05/cM1tit.png" alt="cM1tit.png" border="0" /></a></center>

## 7.3 构造方法

根据`StringBuilder`的API文档，常用的构造方法有2个：

- `public StringBuilder`：构造一个空的`StringBuild`容器，其初始容量为16个字符。

- `public StringBuilder(String str)`：构造一个`StringBuilder`容器，并将字符串添加进去。

代码示例：

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        // 使用无参构造
        StringBuilder sb1 = new StringBuilder();
        System.out.println(sb1); // (空白)
        // 使用带参构造
        StringBuilder sb2 = new StringBuilder("Dilraba");
        System.out.println(sb2); // 输出：Dilraba
    }
}
```

## 7.4 常用方法

`StringBuilder`常用的方法有2个：

- `public StringBuilder append(...)`：添加任意类型数据的字符串形式，并返回当前对象自身。
- `public String toString()`：将当前`StringBuilder`对象转换为`String`对象。

### 7.4.1 append方法

`append()`方法具有多种重载形式，可以接收任意类型的参数。任何数据作为参数都会将对应的字符串内容添加到`StringBuilder`中。

代码示例：

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        // 1.创建StringBuilder对象
        StringBuilder sb1 = new StringBuilder();
        // 2.调用append()方法
        StringBuilder sb2 = sb1.append("abc");
        System.out.println(sb1); // 输出：abc
        System.out.println(sb2); // 输出：abc  相当于把sb1的地址值复制给了sb2
        System.out.println(sb1 == sb2); // 输出：true（比较的是地址值）
        // append()方法返回的是this，调用方法的对象，append()方法无需接收返回值。
        
        /*
        	链式编程：方法返回值是一个对象，可以继续调用方法。
        */
        System.out.println("ab".toUpperCase().toLowerCase.toUpperCase());
    }
}
```

### 7.4.2 toString方法

`StringBuilder`和`String`可以相互转换。

- `String`   ===>   `StringBuilder`：可以使用`StringBuilder`的构造方法。
- `StringBuilder`   ===>   `String`：可以使用`StringBuilder`中的`toString()`方法。

代码示例：

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        // String ===> StringBuilder
        String s1 = "Dilraba";
        StringBuilder s2 = new StringBuilder(s1);
        
        // StringBuilder ===> String
        StringBuilder s3 = new StringBuilder("Dilraba");
        String s4 = s3.toString();
    }
}
```



# 八 包装类

## 8.1 概述

Java提供了两个类型系统，基本类型与引用类型，使用基本类型在于效率，然而很多情况，会创建对象使用，因为对象可以做更多的功能，如果想要我们的基本类型像对象一样操作，就可以使用基本类型对应的包装类，如下：

| 基本类型 | 对应的包装类（位于java.lang包中） |
| -------- | --------------------------------- |
| byte     | Byte                              |
| short    | Short                             |
| int      | **Integer**                       |
| long     | Long                              |
| float    | Float                             |
| double   | Double                            |
| char     | **Character**                     |
| boolean  | Boolean                           |

## 8.2 装箱与拆箱

基本类型与对应的包装类对象之间，来回转换的过程称为"装箱"与"拆箱"：

- **装箱**：从基本类型转换为对应的包装类对象。
- **拆箱**：从包装类对象转换为对应的基本类型。

以`Integer`与`int`为例：

基本类型   ===>   包装类对象

```java
Integer i = new Integer(4); // 使用构造函数
Integer ii = Integer.valueOf(4); // 使用包装类中的valueOf方法
```

包装类对象   ===>   基本类型

```java
int num = i.intValue();
```

### 8.3 自动装箱与自动拆箱

由于我们经常要做基本类型与包装类之间的转换，从Java 5（JDK 1.5）开始，基本类型与包装类的装箱、拆箱动作可以自动完成。例如：

```java
Integer i = 4; // 自动装箱，相当于 Integer i = Integer.valueOf(4);
i = i + 5; // 等号右边，将i对象转换成基本数值（自动拆箱）i.intValue() + 5;
// 加法运算完成之后，再次装箱，把基本数值转换成对象。
```

## 8.4 基本数据与字符串之间的转换

**基本类型转换为String**

基本类型转换`String`总共有三种方式：

```java
Integer i = 3;
// 方式一
String s1 = i + "";
// 方式二
String s2 = i.toString();
// 方式三
String s3 = String.valueOf(i);
```

结论：把一个基本数据类型转换为String,使用**.toString()**是最快的方式、**String.valueOf**和**toString**差不多、**数据+””**的效率最慢。



**String转换为基本类型**

除了`Character`类之外，其他所有包装类都具有`parseXxx()`静态方法可以将字符串参数转换为对应的基本类型。

- `public static byte parseByte(String s)`：将字符串参数转换为对应的`byte`基本类型。

- `public static short parseShort(String s)`：将字符串参数转换为对应的`short`基本类型。
- `public static int parseInt(String s)`：将字符串参数转换为对应的`int`基本类型。
- `public static long parseLong(String s)`：将字符串参数转换为对应的`long`基本类型。
- `public static float parseFloat(String s)`：将字符串参数转换为对应的`float`基本类型。
- `public static double parseDouble(String s)`：将字符串参数转换为对应的`double`基本类型。
- `public static boolean parseBoolean(String s)`：将字符串参数转换为对应的`boolean`基本类型。

代码示例：

```java
public class StringToBaseMain {
    public static void main(String[] args) {
        String s = "123";
        int i = Integer.parseInt(s);
        System.out.println(i);
    }
}
```

