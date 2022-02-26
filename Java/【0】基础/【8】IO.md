# 一 File类

## 1.1 概述

`java.io.File`类是文件和目录路径名抽象表示，主要用于文件和目录的创建、查找和删除等操作。

注意：File类是一个与系统无关的类，任何的操作系统都可以使用这个类中的方法。

重点：记住这三个单词

​		file：文件

​		directory：文件夹、目录

​		path：路径

## 1.2 构造方法

- `public File(String pathname)`：通过将给定的**路径名字符串**转换为抽象路径名来创建新的File实例；
- `public File(String parent, String child)`：从**父路径名字符串和子路径名字符串**创建新的File实例；
- `public File(File parent, String child)`：从**父抽象路径名和子路径名字符串**创建新的File实例。

代码如下：

```java
public class FileMain {
    public static void main(String[] args) {
        show01();
        show02();
        show03();
    }
    public static void show01() {
        /*
        	public File(String pathname)：通过将给定的**路径名字符串**转换为抽象路径名来创建新的File实例；
        	参数：
        		String pathname：字符串的路径名称
        		路径可以是以文件结尾，也可以是以文件夹结尾；
        		路径可以是相对路径，也可以是绝对路径；
        		路径可以是存在，也可以是不存在；
        		创建File对象，只是把字符串路径封装为File对象，不考虑路径的真假情况。
        */
        File f1 = new File("c:\\User\\a.txt");
        System.out.println(f1); // 重写了Object类的toString()方法
        
        File f2 = new File("c:\\User");
        System.out.println(f2); // 重写了Object类的toString()方法
        
        File f3 = new File("b.txt"); // 相对路径
        System.out.println(f3); // 重写了Object类的toString()方法
    }
    public static void show02(String parent, String child) {
        /*
        	File(String parent, String child)
        	根据parent路径名字符串和child路径名字符串创建一个新File实例
        	参数：
        		把路径分为了两部分（parent：父路径，child：子路径）
            好处：
            	父路径和子路径，可以单独书写，使用起来非常灵活；父路径和子路径都可以变化
        */
        File file = new File(parent, child);
        System.out.println(file);
    }
    public static void show03() {
        /*
        	File(File parent, String child)
        	根据parent抽象路径和child路径字符串创建一个新File实例。
        	参数：
        		把路径分为了两部分（parent：父路径，child：子路径）
        	好处：
        		父路径和子路径，可以单独书写，使用起来非常灵活，父路径和子路径都可以变化
        		父路径是File类型，可以使用File的方法对路径进行一些操作，再使用路径创建对象。
        */
        File parent = new File("c:\\");
        file file = new File(parent, "hello.java");
    }
}
```

## 1.3 静态成员变量

- `static String pathSeparator`：与系统有关的路径分隔符，为了方便，它被表示为一个字符串。
- `static char pathSeparatorChar`：与系统有关的路径分隔符。
- `static String separator`：与系统有关的默认名称分隔符，为了方便，它被表示为一个字符串。
- `static char separatorChar`：与系统有关的默认名称分隔符。

代码示例：

```java
public class FileMain {
    public static void main(String[] args) {
        String pathSeparator = File.pathSeparator; // 路径分隔符（win:分号，Linux:冒号）
        System.out.println(pathSeparator); // 输出：；
        
        String separator = File.separator; // 文件分隔符（win:\，Linux:/）
        System.out.println(separator); // 输出：\
    }
}
```

## 1.4 常用方法

### 1.4.1 获取功能的方法

- `public String getAbsolutePath()`：返回此File的绝对路径名字符串。

  获取构造方法中传递的路径，无论是绝对路径还是相对路径，返回的都是绝对路径。

- `public String getPath()`：将此File转换为路径名字符串。（`toString()`调用的就是此方法）

  获取构造方法中传递的路径，是绝对路径就返回绝对路径，是相对路径就返回相对路径。

- `public String getName()`：返回由此File表示的文件或目录的名称。

  获取构造方法中传递的路径的结尾部分（文件或文件夹）。

- `public long length()`：返回由此File表示的文件的长度（大小，字节为单位）。

  文件夹是没有大小概念的，不能获取文件夹的大小；

  如果构造方法中给出的路径不存在，那么`length()`方法返回0。

代码示例：

```java
public class FileGetMain {
    public static void main(String[] args) {
        File f1 = new File("d:/aaa/bbb.java");
        System.out.println("文件绝对路径：" + f1.getAbsolutePath());
        System.out.println("文件构造路径：" + f1.getPath());
        System.out.println("文件名称：" + f1.getName());
        System.out.println("文件长度：" + f1.length() + "字节");
        
        File f2 = new File("d:/aaa");
        System.out.println("目录绝对路径：" + f2.getAbsolutePath());
        System.out.println("目录构造路径：" + f2.getPath());
        System.out.println("目录名称：" + f2.getName());
        System.out.println("目录长度：" + f2.length() + "字节");
    }
}
```

### 1.4.2 判断功能的方法

- `public boolean exists()`：此File表示的文件或目录是否实际存在。

- `public boolean isDirectory()`：此File表示的是否为目录。

  用于判断构造方法中传递的路径是否以文件夹结尾。

- `public boolean isFile()`：此File表示的是否为文件。

  用于判断构造方法中传递的路径是否以文件结尾。

**注意事项**：

1. 电脑硬盘中只有文件、文件夹，两个方法是互斥的。
2. 这两个方法使用前提是路径必须是存在的，否则都返回`false`。

代码示例：

```java
public class FileIsMain {
    public static void main(String[] args) {
        File f1 = new File("d:\\aaa\\bbb.java");
        File f2 = new File("d:\\aaa");
        
        // 判断是否存在
        System.out.println("d:\\aaa\\bbb.java 是否存在：" + f1.exists());
        System.out.println("d:\\aaa 是否存在：" + f2.exists());
        
        // 判断是文件还是目录
        System.out.println("d:\\aaa\\bbb.java 文件？" + f1.isFile());
        System.out.println("d:\\aaa 目录？" + f2.isDirectory());
    }
}
```

### 1.4.3 创建删除功能的方法

- `public boolean createNewFile()`：当且仅当具有该名称的文件尚不存在时，创建一个新的空文件。

  创建文件的路径和名称在构造方法中给出（构造方法的参数），此方法只能创建文件，不能创建文件夹。创建文件的路径必须存在，否则会被抛出异常（**必须处理该异常**）。

- `public boolean delete()`：删除由此File表示的文件或目录。

  `delete()`方法是直接在硬盘删除文件、文件夹，不走回收站，删除要谨慎。

- `public boolean mkdir()`：创建由此File表示的目录。

  创建文件夹的路径和名称在构造方法中给出（构造方法的参数），只能创建单级空文件夹

- `public boolean mkdirs()`：创建由此File表示的目录，包括任何必须但不存在的父目录。

  创建文件夹的路径和名称在构造方法中给出（构造方法的参数），既可以创建单级空文件夹，又可以创建多级文件夹。

代码示例：

```java
public class FileCreateDeleteMain {
    public static void main(String[] args) throws IOException {
        // 文件的创建
        file f1 = new File("aaa.txt");
        System.out.println("是否存在：" + f1.exists()); // false
        System.out.println("是否创建：" + f1.createNewFile()); // true
        System.out.println("是否存在：" + f1.exists()); // true
        
        // 目录的创建
        file f2 = new File("newDir");
        System.out.println("是否存在：" + f2.exists()); // false
        System.out.println("是否创建：" + f2.mkdir()); // true
        System.out.println("是否存在：" + f2.exists()); // true
        
        // 创建多级目录
        file f3 = new File("newDir\\111\\222\\333");
        System.out.println("是否存在：" + f3.exists()); // false
        System.out.println("是否创建：" + f3.mkdirs()); // true
        System.out.println("是否存在：" + f3.exists()); // true
    }
}
```

## 1.5 目录的遍历

- `public String[] list()`：返回一个String数组，表示该File目录中的所有子文件或目录。
- `public File[] listFiles()`：返回一个File数组，表示该File目录中的所有的子文件或目录。

**注意事项**：

1. `list()`和`listFiles()`方法遍历的是构造方法中给出的目录；
2. 如果构造方法中给出的目录的路径不存在，会抛出空指针异常；
3. 如果构造方法中给出的路径不是一个目录，也会抛出空指针异常。

代码示例：

```java
public class FileFor {
    public static void main(String[] args) {
        File dir = new File("d:\\java_code");
        
        // 获取当前目录下的文件以及文件夹的名称
        String[] names = dir.list();
        for (String name : names) {
            System.out.println(name);
        }
        
        // 获取当前目录下的文件以及文件夹对象，只要拿到了文件对象，那么就可以获取更多信息
        File[] files = dir.listFiles();
        for (File file : files) {
            System.out.println(file);
        }
    }
}
```



# 二 综合案例

## 2.1 文件搜索

搜索`D:\aaa`目录中的`.java`文件。

**分析**：

1. 目录搜索，无法判断多少级目录，所以使用递归，遍历所有目录。
2. 遍历目录时，获取的子文件，通过文件名称，判断是否符合条件。

代码示例：

```java
public class FileFindMain {
    public static void main(String[] args) {
        
    }
    public static void getAllFile(File dir) {
        File[] files = dir.listFiles();
        for (File f : files) {
            if (f.isDirectory()) {
                getAllFile(f);
            } else {
                if (f.toString().toLowerCase().endsWith(".java")) {
                    System.out.println(f);
                }
            }
        }
    }
}
```

## 3.2 文件过滤器

`java.io.FileFilter`是一个接口，是File的过滤器（抽象路径名：File对象）。该接口的对象可以传递给File类的`listFiles(FileFilter)`作为参数，接口中只有一个方法`accept()`。

`boolean accept(File pathname)`：测试`pathname`是否应该包含在当前File目录中，符合则返回`true`。其参数就是使用`ListFiles`方法遍历目录，得到的每一个文件对象。

参数：

​		`File pathname`：使用`ListFiles`方法遍历目录，得到的每一个文件对象。



`java.io.FileNameFilter`是一个接口，用于过滤文件名称， 接口中只有一个方法`accept()`。

`boolean accept(File dir, String name)`：测试指定文件是否应该包含在某一文件列表中。

参数：

​		`File dir`：构造方法中传递的被遍历的目录。

​		`String name`：使用`ListFiles`方法遍历目录，获取的每一个文件、文件夹的名称。

**注意事项**：

​		两个过滤器接口是没有实现类的，需要我们自己写实现类，重写过滤的方法`accept()`，在方法中自己定义过滤的规则。

**分析**：

1. 接口作为参数，需要传递子类对象，重写其中方法。我们选择匿名内部类方法，比较简单。

2. `accept()`方法，参数为File，表示当前File下所有的子文件和目录。保留住则返回`true`，过滤掉则返回`false`。

   保留规则：

   1. 要么是`.java`文件。
   2. 要么是目录，用于继续遍历。

代码示例：

```java
/*
	创建过滤器FileFilter的实现类，重写过滤方法accept()，定义过滤规则。
*/
public class FileFilterImpl implements FileFilter {
    @Override
    public boolean accept(File pathname) {
        return pathname.isDirectory() || pathname.getName().toLowerCase().endsWith(".java");
    }
}
```

```java
public class FileFindMain {
    public static void main(String[] args) {
        
    }
    public static void getAllFile(File dir) {
        File[] files = dir.listFiles(new FileFilterImpl); // 传递过滤器对象
        for (File f : files) {
            if (f.isDirectory()) {
                getAllFile(f);
            } else {
                if (f.toString().toLowerCase().endsWith(".java")) {
                    System.out.println(f);
                }
            }
        }
    }
}
```

**过滤器原理**：

<center><a href="https://imgtu.com/i/caX2Os"><img src="https://z3.ax1x.com/2021/04/10/caX2Os.png" alt="caX2Os.png" border="0" /></a></center>

匿名内部类进行代码优化：

```java
public class FileFindMain {
    public static void main(String[] args) {
        
    }
    public static void getAllFile(File dir) {
        File[] files = dir.listFiles(new FileFilter () {
            @Override
            public boolean accept(File pathname) {
        		return pathname.isDirectory() || pathname.getName().toLowerCase().endsWith(".java");
    		}
        }); // 传递过滤器对象
        for (File f : files) {
            if (f.isDirectory()) {
                getAllFile(f);
            } else {
                if (f.toString().toLowerCase().endsWith(".java")) {
                    System.out.println(f);
                }
            }
        }
    }
}
```

Lambda表达式继续优化：

```java
public class FileFindMain {
    public static void main(String[] args) {
        
    }
    public static void getAllFile(File dir) {
        File[] files = dir.listFiles((d, name) -> new File(d, name).isDirectory() || name.toLowerCase().endsWith(".java");
        ); // 传递过滤器对象
        for (File f : files) {
            if (f.isDirectory()) {
                getAllFile(f);
            } else {
                if (f.toString().toLowerCase().endsWith(".java")) {
                    System.out.println(f);
                }
            }
        }
    }
}
```



# 三 IO概述

## 3.1 什么是IO

生活中，你肯定经历过这样的场景。当你编辑一个文本文件，忘记了`ctrl+s`，可能文件就白白编辑了。当你电脑上插入一个U盘，可以把一个视频，拷贝到你的电脑硬盘里。那么数据都是在哪些设备上的呢？键盘、内存、硬盘、外接设备等等。

我们把这种数据的传输，可以看做是一种数据的流动，按照流动的方向，以内存为基准，分为输入`input`和输出`output`，即流向内存是输入流，流出内存的输出流。

Java中I/O操作主要是指使用`java. io`包下的内容，进行输入、输出操作。**输入**也叫做读取数据，**输出**也叫做作写出数据。

## 3.2 IO的分类

根据数据的流向分为：**输入流**和**输出流**。

- **输入流**：把数据从**其他设备**上读取到**内存**中的流。
- **输出流**：把数据从**内存**中写出到**其他设备**上的流。

根据数据的类型分为：**字节流**和**字符流**。

- **字节流**：以字节为单位，读写数据的流。（1字节8位二进制）
- **字符流**：以字符位单位，读写数据的流。（1字符2字节）

## 3.3 IO的流向

<a href="https://imgtu.com/i/cax8YV"><img src="https://z3.ax1x.com/2021/04/10/cax8YV.png" alt="cax8YV.png" border="0" /></a>

## 3.4 顶级父类

|        |             输入流             |             输出流              |
| :----: | :----------------------------: | :-----------------------------: |
| 字节流 | 字节输入流<br/>**InputStream** | 字节输出流<br/>**OutputStream** |
| 字符流 |    字符输入流<br>**Reader**    |    字符输出流<br>**Writer**     |



# 四 字节流

## 4.1 一切皆为字节

一切文件数据（文本、图片、视频等）在存储时，都是以二进制数字的形式保存，都一个一个的字节，那么传输时一样如此。所以，字节流可以传输任意文件数据。在操作流的时候，我们要时刻明确，无论使用什么样的流对象，底层传输的始终为二进制数据。

## 4.2 字节输出流 OutputStream

`java.io.OutputStream`抽象类是表示**字节输出流的所有类的超类**，将指定的字节信息写出到目的地，它定义了字节输出流的基本共性功能方法：

- `public void close()`：关闭此输出流并释放与此流相关联的所有系统资源。
- `public void flush()`：刷新此输出流并强制写出所有缓冲的输出字节。
- `public void write(byte[] b)`：将`b.length`字节从指定的字节数组写入此输出流。
- `public void write(byte[] b, int off, int len)`：从指定的字节数组写入`len`字节，从偏移量`off`开始输出到此输出流。
- `public abstract void write(int b)`：将指定的字节写入此输出流。

```tex
注意：close()方法，当完成流的操作时，必须调用此方法，释放系统资源。
```

## 4.3 FileOutputStream类

`OutputStream`有很多子类，`java.io.FileOutputStream`类是文件字节输出流，用于将数据（内存中）写出到文件（硬盘中）。

### 4.3.1 构造方法

- `public FileOutputStream(String name)`：创建一个向指定名称的文件中写入数据的输出文件流。

- `public FileOutputStream(File file)`：创建一个向File对象表示的文件中写入数据的文件输出流。

  参数：写入数据的目的地

  ​		`String name`：目的地是一个文件的路径。

  ​		`File file`：目的地是一个文件。

  作用：

  		1. 创建一个`FileOutputStream`对象；
  		2. 会根据构造方法中传递的文件、文件路径，创建一个空的文件 ；
  		3. 会把`FileOutputStream`对象指向创建

代码示例：

```java
public class FileOutputStreamConstructor throws IOEception {
    public static void main(String[] args) {
        // 使用File对象创建流对象
        File file = new File("a.txt");
        FileOutputStream fos = new FileOutputStream(file);
        
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("b.txt");
    }
}
```

### 4.3.2 写出字节数据（一个字节）

**写出字节**：

`write(int b)`方法，每次可以写出一个字节数据。

**使用步骤**：

1. 创建一个`FileOutputStream`对象，构造方法中传递写入数据的目的地；
2. 调用`FileOutputStream`对象中的方法`write()`，把数据写入到文件中；
3. 释放资源。

**代码示例**：

```java
public class FOSWrite {
    public static void main(String[] args) {
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("fos.txt");
        // 写出数据
        fos.write(97); // 写出第1个字节
        fos.write(98); // 写出第2个字节
        fos.write(99); // 写出第3个字节
        // 关闭资源
        fos.close();
    }
}
```

**写入原理**：

```tex
java程序 ——> JVM(java虚拟机) ——> OS(操作系统) ——> OS调用写数据的方法 ——> 把数据写入到文件中
```

<center><a href="https://imgtu.com/i/cdV79J"><img src="https://z3.ax1x.com/2021/04/10/cdV79J.png" alt="cdV79J.png" border="0" /></a></center>

### 4.3.3 写出字节数据（多个字节）

**写出字节**：

`public void write(byte[] b)`：将`b.length`字节从指定的字节数组写入此输出流。

​		如果写入的第一个字节是**正数**（0-127），那么显示的时候会查询ASCII表；

​		如果写入的第一个字节是**负数**，那么第一个字节会和第二个字节，两个字节组成一个中文显示，查询系统默认码表（GBK：简体中文）。

`public void writer(byte[] b, int off, int len)`：将指定的字节数组从偏移量为`off`的地方写入`len`个字节。

**使用步骤**：

1. 创建一个`FileOutputStream`对象，构造方法中传递写入数据的目的地；
2. 调用`FileOutputStream`对象中的方法`write()`，把数据写入到文件中；
3. 释放资源。

**代码示例**：

```java
/*
	public void write(byte[] b)
*/
public class FOSWrite {
    public static void main(String[] args) {
        // 使用File对象称创建流对象
        FileOutputStream fos = new FileOutputStream(new File("b.txt"));
        // 写出数据（在文件中显示100，需要写入3个字节）
        byte[] bytes = {65, 66, 67, 68, 69}; //ABCDE
        byte[] bytes = {-65, -66, -67, 68, 69}; //ABCDE
        fos.write(bytes);
        // 关闭资源
        fos.close();
    }
}
```

```java
/*
	public void writer(byte[] b, int off, int len)
*/
public class FOSWrite {
    public static void main(String[] args) {
        // 使用File对象称创建流对象
        FileOutputStream fos = new FileOutputStream(new File("c.txt"));
        // 写出数据（在文件中显示100，需要写入3个字节）
        byte[] bytes = {65, 66, 67, 68, 69}; //ABCDE
        fos.write(bytes, 1, 2); // BC
        // 关闭资源
        fos.close();
    }
}
```

**写入字符**：

可以使用`String`类中的方法`byte[] getByte()`把字符串，转换为字节数组；

```java
public class FOSWrite {
    public static void main(String[] args) {
        // 使用File对象称创建流对象
        FileOutputStream fos = new FileOutputStream(new File("d.txt"));
        // 写出数据
        byte[] bytes = "你好".getByte();
        fos.write(bytes);
        // 关闭资源
        fos.close();
    }
}
```

### 4.3.4 数据追加续写

经过以上的演示，每次程序运行，创建输出流对象，都会清空目标文件中的数据。如何保留目标文件中数据，还能继续添加新数据呢？

`public FileOutputStream(File file, boolean append)`：创建文件输出流以写入由指定的File对象表示的文件。

`public FileOutputStream(String file, boolean append)`：创建文件输出流以指定的名称写入文件。

这两个构造方法，参数中都需要传入一个boolean类型的值，`true`表示追加数据，`false`表示清空原有数据。这样创建的输出流对象，就可以指定是否追加续写了。

**注意事项**：

1. `true`创建对象不会覆盖原文件，继续在文件的末尾追加写数据。
2. `false`创建一个新文件，覆盖源文件。

**代码示例**：

```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        FileOutputStream fos = new FileOutputStream("fos.txt", true);
        fos.write("abcde".getByte(););
        fos.close();
    }
}
```

### 4.3.5 数据写入换行

通过写换行符号在字节写入时换行，不同的操作系统，换行符号不同。（windows：\r\n，linux：/n，mac：/r）

**代码示例**：

```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        FileOutputStream fos = new FileOutputStream("a.txt");
        for (int i = 0; i < 10; i++) {
            fos.write("你好".getByte() + "\r\n".getByte());
        }
        fos.close();
    }
}
```

## 4.4 字节输入流 InputStream

`java.io.InputStream`抽象类是表示字节**输入流的所有类的超类**，可以读取字节信息到内存中。它定义了字节输入流的基本共性功能方法。

- `public void close()`：关闭此输入流并释放与此流相关联的任何系统资源。
- `public abstract int read()`：从输入流读取数据的下一个字节。
- `public int read(byte[] b)`：从输入流中读取一些字节数，并将它们存储到字节数组`b`中。

## 4.5 FileInputStream类

`java.io.FileInputStream`类是文件输入流，从文件中读取字节。

### 4.5.1 构造方法

- `FileInputStream(File file)`：通过打开与实际文件的链接来创建一个`FileInputStream`，该文件由文件系统中的File对象file命名。

- `FileInputStream(String name)`：通过打开与实际文件的链接来创建一个`FileInputStream`，该文件由文件系统中的路径名`nama`命名。

当你创建一个流对象时，必须传入一个文件路径。该路径下，如果没有该文件，则会抛出`FileNotFoundException`。

参数：写入数据的目的地

​		`String name`：目的地是一个文件的路径。

​		`File file`：目的地是一个文件。

作用：

  		1. 创建一个`FileOutputStream`对象；
  		2. 会把`FileInputStream`对象指定到构造方法中要读取的文件。

### 4.5.2 读取字节数据（一个字节）

**读取字节**：

`read()`方法，每次可以读取一个字节的数据，提升为`int`类型，读取到文件末尾，返回`-1`。

**使用步骤**：

1. 创建`FileInputStream`对象，构造方法中绑定要读取的数据源；
2. 使用`FileInputStream`对象中的方法`read()`，读取文件；
3. 释放资源。

**代码示例**：

```java
public class FISRead {
    public static void main(String[] args) {
        // 使用文件名称创建流对象
        FileInputStream fis = new FileInputStream("read.xtx");
        // 读取数据，返回一个字节
        int read = 0;
        while ((read = fis.read()) != -1) {
            System.out.println(read);
        }
        // 释放资源
        fis.close(); 
    }
}
```

**读取原理**：

```tex
java程序 ——> JVM(java虚拟机) ——> OS ——> OS读取数据的方法 ——> 读取文件
```

<center><a href="https://imgtu.com/i/cd8ujS"><img src="https://z3.ax1x.com/2021/04/10/cd8ujS.png" alt="cd8ujS.png" border="0" /></a></center>

### 4.5.3 读取字节数据（多个字节）

**读取字节**：

`public int read(byte[] b)`：从输入流中读取一些字节数，并将它们存储到字节数组`b`中。

**明确两件事情**：

1. 方法的参数`byte[]`的作用？

   起到缓冲作用，存储每次读到的多个字节；

   数组的长度一般定义为1024（1kb）或者1024的整数倍。

2. 方法的返回值`int`是什么？

   每次读取的有效字节个数。

**使用步骤**：

1. 创建`FileInputStream`对象，构造方法中绑定要读取的数据源；
2. 使用`FileInputStream`对象中的方法`read()`，读取文件；
3. 释放资源。

**代码示例**：

```java
public class InputStream {
    public static void main(String[] args) throws IOException {
        // 创建FileInputStream对象，构造方法中绑定要读取的数据源
        FileInputStream fis = new FileInputStream("b.txt");
        // 调用read(byte[] b)
        byte[] bytes = new byte[1024];
        int len = 0;
        while ((len = fis.read(bytes)) != -1) {
            System.out.ptintln(new String(bytes, 0, len));
        }
        // 释放资源
        fis.close();
    }
}
```

**读取原理**：

<center><a href="https://imgtu.com/i/cdJXkj"><img src="https://z3.ax1x.com/2021/04/10/cdJXkj.png" alt="cdJXkj.png" border="0" /></a></center>

## 4.6 图片复制

### 4.6.1 复制原理图解

<center><a href="https://imgtu.com/i/cdY6Cn"><img src="https://z3.ax1x.com/2021/04/10/cdY6Cn.png" alt="cdY6Cn.png" border="0" /></a></center>

### 4.6.2 代码实现

**明确**：数据源，数据的目的地。

**文件复制的步骤**：

1. 创建一个字节输入流对象，构造方法中绑定要读取的数据源；
2. 创建一个字节输出流对象，构造方法中绑定要写入的目的地；
3. 使用字节输入流对象中的方法`read()`读取文件；
4. 使用字节输出流对象中的方法`write()`，把读取到的字节写入到目的地的文件中。
5. 释放资源

**代码实现**：

```java
public class FileCopy {
    public static void main(String[] args) throws IOException {
        // 1.创建字节输入流对象
        FileInputStream fis = new FileInputStream("c:\\test.jpg");
        // 2.创建字节输出流对象
        FileOutputStream fos = new FileOutputStream("c:\\test_copy.jpg");
        
        /*
        	方式一：一次读取一个字节，然后写入一个字节。
        */
        int len = 0;
        // 3.读取文件
        while ((len = fis.read()) != -1) {
            // 4.写入文件
            fos.write(len);
        }

        /*
        	方式二：使用数组缓冲，读取多个字节。
        */
        byte[] bytes = new byte[1024];
        int len = 0; // 每次读取的有效字节的个数
        // 3.读取文件
        while ((len = fis.read(bytes)) != -1) {
            // 4.写入文件
            fos.write(bytes, 0, len);
        }
        
        // 5.资源释放（先关闭写的，再关闭读的；如果写完了，肯定读取完毕了。）
        fis.close();
        fos.close();
    }
}
```



# 五 字符流

当使用字节流读取文本文件时，可能会有一个小问题。就是遇到中文字符时，可能不会显示完整的字符，那是因为一个中文字符可能占用多个字节存储。所以Java提供了一些字符流类，以字符为单位读写数据，专门用于处理文本文件。

## 5.1 字符输入流 Reader

`java.io.Reader`抽象类是表示用于**读取字符流的所有类的超类**，可以读取字符信息到内存中。它定义了字符输入流的基本共性功能方法。

- `public void close()`：关闭此流并释放与此流相关联的所有系统资源；
- `public int read()`：从输入流读取一个字符；
- `public int read(char[] cbuf)`：从输入流中读取一些字符，并将它们存储到字符数组`cbuf`中。

## 5.2 FileReader类

`java.io.FileReader`类是读取字符文件的便利类。构造时使用系统默认的字符编码和默认字节缓冲区。

注意：`java.io.FileReader extends InputStreamReader extends Reader`

**作用**：把硬盘文件中的数据以字符的方式读取到内存中。

### 5.2.1 构造方法

- `FileReader(File file)`：在给定读取数据的File的情况下创建一个新的`FileReader`对象。
- `FileReader(String fileName)`：在给定读出数据的文件名的情况下创建一个新的`FileReader`对象。

**参数**：

​		`String fileName`：文件的路径；

​		`File file`：一个文件；

**作用**：

1. 创建一个`FileReader`对象；
2. 会把`FileReader`对象指向要读取的文件

**代码示例**：

```java
public class FileReaderConstructor throws IOException {
    public static void main(String[] aargs) {
        // 使用File对象创建流对象
        File file = new File("a.txt");
        FileReader fr = new FileReader(file);
        
        // 使用文件名称创建流对象
        FileReader fr = new FileReader("b.txt");
    }
}
```

### 5.2.2 读取字符数据（一个字符）

**读取字符**：

`int read()`方法，每次可以读取一个字符的数据，提升为`int`类型，读取到文件末尾，返回`-1`，循环读取。

**使用步骤**：

1. 创建`FileReader`对象，构造方法中绑定要读取的数据源；
2. 使用`FileReader`对象中的方法`read()`读取文件；
3. 释放资源。

**代码示例**：

```java
public class FileReader {
    public static void main(String[] args) throws IOException {
        // 1.创建FileReader对象
        FileReader fr = new FileReader("a.txt");
        // 2.使用read()方法读取字符
        int len = 0;
        while ((len = fr.read()) != -1) {
            System.out.println((cahr)len);
        }
        // 3.释放资源
        fr.close();
    }
}
```

### 5.2.3 读取字符数据（多个字符）

**读取字符**：

`int read(char[] cbuf)`方法，每次可以读取多个字符，将字符读入数组。

**代码示例**：

```java
public class FileReader {
    public static void main(String[] args) throws IOException {
        // 1.创建FileReader对象
        FileReader fr = new FileReader("a.txt");
        // 2.使用read()方法读取字符
        int len = 0;
        char[] cs = new char[1024];
        while ((len = fr.read(cs)) != -1) {
            System.out.println(new String(cs, 0, len));
        }
        // 3.释放资源
        fr.close();
    }
}
```

## 5.3 字符输出流 Writer

`java.io.Write`抽象类是表示用于**写出字符流的所有类的超类**，将指定的字符信息写出到目的地。它定义了字节输出流的基本共性功能方法。

- `void write(int c)`：写入单个字符；
- `void write(char[] cbuf)`：写入字符数组；
- `abstract void write(char[] cbuf, ing off, int len)`：写入字符数组的某一部分，从偏移量为`off`开始，写入`len`个字符；
- `void write(String str)`：写入字符串；
- `void write(String str, int off, int len)`：写入字符串的某一部分，从偏移量为`off`开始，写入`len`个字符；
- `void flush()`：刷新该流的缓冲；
- `void close()`：关闭此流，但要先刷新它。

## 5.4 FileWriter类

`java.io.FileWriter`类是写出字符到文件的便利类。构造时使用系统默认的字符编码和默认字节缓冲区。

`java.io.FileWriter extends OutputStreamWriter extends Writer`

**作用**：把内存中的字符数据写入到文件中。

### 5.4.1 构造方法

- `FileWriter(File file)`：创建一个新的`FileWriter`，给定要读取的File对象。
- `FileWriter(String fileName)`：创建一个新的`FileWriter`，给定要读取的文件的名称。

**作用**：

1. 会创建一个`FileWriter`对象；
2. 会根据构造方法中传递的文件、文件路径来创建文件；
3. 会把`FileWriter`对象指向创建好的文件。

**代码示例**：

```java
public class FileWriterConstructor {
    public static void main(String[] args) throws IOException {
        // 使用File对象创建流对象
        File file = new file("a.txt");
        FileWriter fw = new FileWriter(file);
        
        // 使用文件名称创建流对象
        FileWriter fw = new FileWriter("b.txt");
    }
}
```

### 5.4.2 写出字符数据

**写出字符**：

`int writer(int b)`方法，每次可以写出一个字符数据。

**使用步骤**：

1. 创建一个`FileWriter`对象，构造方法中绑定要写入数据的目的地；
2. 使用`FileWriter`中的`write()`方法，把数据写入到内存缓冲区中（**字符转换为字节的过程**）。
3. 使用`FileWriter`中的方法`flush()`，把内存缓冲区中的数据，刷新到文件中。
4. 释放资源（会先把内存缓冲区中的内容刷新到文件中）。

**代码示例**：

```java
public class FWWriter {
    public static void main(String[] args) throws IOException {
        // 1.使用文件名称创建流对象
        FileWriter fw = new FileWriter("fw.txt");
        // 2.写出数据
        fw.write(97);
        // 3.刷新数据
        fw.flush();
        // 4.释放资源
        fw.close();
    }
}
```

### 5.4.3 关闭和刷新

因为内置缓冲区的原因，如果不关闭输出流，无法写出字符到文件中。但是关闭的流对象，是无法继续写出数据的。如果我们既想写出数据，又想继续使用流，就需要`flush()`方法了。

- `flush()`：刷新缓冲区，流对象可以继续使用。
- `close()`：先刷新缓冲区，然后通知系统释放资源，流对象不可以再被使用了。

**代码示例**：

```java
public class FWWriter {
    public static void main(String[] args) {
        // 使用文件名称创建流对象
        FileWriter fw = new FileWriter("a.txt");
        // 写出数据，通过flush()
        fw.write('刷'); // 写出第一个字符
        fw.flush();
        fw.write('新'); // 继续写出第2个字符，写出成功
        fw.flush();
        
        // 写出数据，通过close()
        fw.close();
    }
}
```

### 5.4.4 写出其他数据

**写出字符数组**：

`write(char[] cbuf)`和`write(char[] cbuf, int off, int len)`，每次可以写出字符数组中的数据，用法类似`FileOutputStream`。

代码示例：

```java
public class FWWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileWriter fw = new FileWriter("fw.txt");
        // 字符串转为字节数组
        char[] chars = "黑马程序员".toCharArray();
        
        // 写出字符数组
        fw.write(chars);
        
        // 写出从索引2开始，2个字节索引2是'程'，两个字节，也就是"程序"。
        fw.write(b, 2, 2);
        
        // 释放资源
        fos.close();
    }
}
```

**写字符串**：

`write(String str)`和`write(String str, int off, int len)`每次可以写出字符串中的数据，更为方便。

代码示例：

```java
public class FWWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileWriter fw = new FileWriter("fw.txt");

        // 写出字符串
        fw.write("传智播客");
        
        // 写出从索引2开始，2个字节索引2是'程'，三个字节，也就是"程序员"。
        fw.write("黑马程序员", 2, 3);
        
        // 释放资源
        fos.close();
    }
}
```

### 5.4.5 续写和换行

操纵类似于`FileOutputStream`。

代码示例：

```java
public static void main(String[] args) throws IOException {
    // 使用文件名称创建流对象，可以续写数据
    FileWriter fw = new FileWriter("a.txt", true);
    // 写出字符串
    fw.write("黑马");
    // 写出换行符
    fw.write("\r\n");
    // 写出字符串
    fw.write("程序员");
    // 释放资源
    fw.close();
}
```

**注意事项**：字符流，只能操作文本文件，不能操作图片、视频等非文本文件。当我们单纯读或者写文本文件时，使用字符流，其他情况使用字节流。



# 六 IO异常

## 6.1 JDK7前处理

之前练习中，一直把异常抛出，而实际开发中并不能这样处理，建议使用`try...catch...finally`代码块，处理异常部分。

代码示例：

```java
public class HandleException {
    public static void main(String[] args) {
        // 声明变量
        FileWriter fw = null;
        try {
            // 创建流对象
            fw = new FileWriter("fw.txt",);
            // 写出数据
            fw.write("黑马程序员");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fw != null)
                    fw.close();
            } catch (IOException e) {
                    e.printStackTrace();
            }
        }
    }
}
```

## 6.2 JDK7新特性

在`try`的后边可以增加一个`()`，在括号中可以定义流对象，那么这个流对象的作用域就在`try`中有效，`try`中的代码执行完毕，会自动把流对象释放，不用写`finally`。

**格式**：

```java
try (定义流对象; 定义流对象; ...) {
    可能会产生异常的代码;
} catch (异常类型 变量名) {
    异常的处理逻辑;
}
```

代码示例：

```java
public class JDK7Main {
    public static void main(String[] args) {
        try (
            FileInputStream fis = new FileInputStream("c:\\1.jpg");
            FileOutputStream fos = new FileOutputStream("d:\\2.jpg");
        ) {
            int len = 0;
            while ((len = fis.read()) != -1) {
                fos.write(len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 6.3 JDK9新特性

`try`的前边可以定义流对象，在`try`后边的`()`中可以直接引入流对象的名称（变量名），在`try`代码执行完毕之后，流对象也可以释放掉，不用写`finally`。

**格式**：

```java
A a = new A();
B b = new B();
try (a; b) {
    可能会产生异常的代码;
} catch (异常类型 变量名) {
    异常的处理逻辑;
}
```

使用示例：

```java
public class JDK7Main {
    public static void main(String[] args) {
        FileInputStream fis = new FileInputStream("c:\\1.jpg");
        FileOutputStream fos = new FileOutputStream("d:\\2.jpg");
        try (fis; fos) {
            int len = 0;
            while ((len = fis.read()) != -1) {
                fos.write(len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



# 七 属性集

## 7.1 概述

`java.util.Properties extends Hashtable<K, V> implements Map<K, V>`，来表示一个**持久的属性集**，可保存在流中或从流中加载。它使用键值结构存储数据，**每个键及其对应值都是一个字符串**（默认），是**唯一和IO流相结合的集合**。该类也被许多Java类使用，比如获取系统属性时，`System.getProperties()`方法就是返回一个`Properties`对象。

## 7.2 Properties类

### 7.2.1 构造方法

- `public Properties()`：创建一个空的属性列表。

### 7.2.2 基本的存储方法

- `public Object setProperty(String key, String value)`：保存一对属性，调用`Hashtable`的`put()`方法。
- `public String getProperty(String key)`：使用此属性列表中指定的键搜索属性值，相当于`Map`集合中的`getKey(key)`方法。
- `public Set<String> stringPropertyNames()`：所有键的名称的集合，相当于`Map`集合中的`keySet()`方法。

代码示例：

```java
public class PropertiesMain {
    public static void main(String[] args) {
        // 创建属性集对象
        Properties properties = new Properties();
        // 向集合中添加数据
        properties.setProperty("迪丽热巴", "178");
        properties.setProperty("古力娜扎", "168");
        properties.setProperty("马尔扎哈", "158");
        // 遍历集合
        Set<String> set = properties.stringPropertyNames();
        for (String key : set) {\
            // 通过键获取值
            String value = getProperty(key);
            System.out.println(value);
        }
    }
}
```

### 7.2.3 store()方法

可以使用`Properties`集合中的方法`stor()`，把集合中的临时数据，持久化写入到硬盘中存储。

- `public void store(OutputStream out, String comments)`：以适合使用`load(InputStream)`方法加载到`Properties`表中的格式，将此`Properties`表中的属性列表（键和元素对）写入输出流。
- `public void store(Writer writer, String comments)`：以适合使用`load(Reader)`方法加载到`Properties`表中的格式，将此`Properties`表中的属性列表（键和元素对）写入输出字符。

**参数**：

​		`OutputStream out`：字节输出流，不能写入中文；

​		`Writer writer`：字符输出流，可以写入中文；

​		`String comments`：注释，用来解释说明保存的文件是做什么用的，不能使用中文，会产生乱码，默认是`Unicode`编码，一般使用"空字符串"。

**使用步骤**：

1. 创建`Properties`集合对象，并添加数据；
2. 创建字节输出流、字符输出流对象，构造方法中绑定要输出的目的地；
3. 使用`Properties`集合中`store()`方法；

4. 释放资源。

代码示例：

```java
public class PropertiesStore {
    public static void main(String[] args) throws IOException {
        // 创建属性集对象
        Properties properties = new Properties();
        // 向集合中添加数据
        properties.setProperty("迪丽热巴", "178");
        properties.setProperty("古力娜扎", "168");
        properties.setProperty("马尔扎哈", "158");
        // 创建输出流对象
        FileWriter writer = new FileWriter("a.txt");
        
        // 使用sotre()方法
        properties.store(fw, "save data");
        
        // 释放资源
        fw.close();
    }
}
```

### 7.2.4 load()方法

可以使用`Properties`集合中的方法`load()`，把硬盘中保存的文件（键值对），读取到集合中使用。

- `public void load(InputStream inStream)`：从输入流中读取属性列表（键和元素对）；
- `public void load(Reader reader)`：按简单的面向行的格式从输入字符流中读取属性列表（键和元素对）；

**参数**：

​		`InputStream inStream`：字节输入流，不能读取含有中文的键值对；

​		`Reader reader`：字符输入流，能读取含有中文的键值对；

**使用步骤**：

1. 创建`Properties`集合对象；
2. 使用`Properties`集合对象中的方法`load()`读取保存键值对的文件；
3. 遍历`Properties`集合。

**注意事项**：

1. 存储键值对的文件中，键与值默认的连接符号可以使用=，空格（其他符号）；
2. 存储键值对的文件中，可以使用#进行注释，被注释的键值对不会再被读取；
3. 存储键值对的文件中，键与值默认都是字符串，不用再加引号；

代码示例：

```java
public class PropertiesLoad {
    public static void main(String[] args) throws IOException {
        // 创建属性集对象
        Properties properties = new Properties();
        // 读取文件
        properties.load(new FileReader("a.txt"));
        // 遍历集合
        Set<String> set = properties.stringPropertyNames();
        for (String key : set) {
            String value = properties.getProperty(key);
            System.out.println(key + "=" + value);
        }
    }
}
```



# 八 缓冲流

## 8.1 概述

缓冲流，也叫高效流，是对4个基本的`FileXxx`流的增强，所以也是4个流，按照数据类型分类：

- **字节缓冲流**：`BufferedInputStream`、`BufferedOutputStream`
- **字符缓冲流**：`BufferedReader`、`BufferedWriter`

缓冲流的基本原理，是在创建流对象时，会创建一个内置的默认大小的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。

<center><a href="https://imgtu.com/i/cw1R9P"><img src="https://z3.ax1x.com/2021/04/11/cw1R9P.png" alt="cw1R9P.png" border="0" /></a></center>

## 8.2 字节缓冲输出流

**构造方法**

`java.io.BufferedOutputStream extends OutputStream`

- `public BufferedOutputStream(OutputStream out)`：创建一个新的缓冲输出流，以将数据写入指定的底层输出流；
- `public BufferedOutputStream(OUtputStream out, int siez)`：创建一个新的缓冲输出流，以将具有指定缓冲区大小的数据写入指定的底层输出流；

**参数**：

​		`OutputStream out`：字节输出流，传递`FileOutputStream`，缓冲区会给`FileOutputStream`增加一个缓冲区，提高`FileOutputStream`的写入效率。

​		`int size`：指定缓冲流内部缓冲区的大小，不指定就默认（1024）。

**使用步骤**：

1. 创建`FileOutputStream`对象，构造方法中绑定要输出的目的地。
2. 创建`BufferedOutputStream`对象，构造方法中传递`FileOutputStram`对象，提高`FileOutputStream`对象效率。
3. 使用`BufferedOutputStream`对象中的`write()`方法，把数据写入到内部缓冲区中。
4. 使用`BufferedOutputStream`对象中的`flush()`方法，把内部缓冲区中的数据，刷新到文件中。
5. 释放资源（会先调用`flush()`方法刷新数据，第4步可以省略）。

代码示例：

```java
public class BufferedOutputStreamMain {
    public static void main(String[] args) {
        // 1.创建字节输出流对象
        FileOutputStream fos = new FileOutputStream("a.txt");
        // 2.创建字节输出缓冲流对象
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        // 3.调用write()，写入数据
        bos.write("写入的数据！".getBytes());
        // 4.刷新
        bos.flush();
        // 5.释放资源
        bos.close();
    }
}
```

## 8.3 字节缓冲输入流

**构造方法**：

`java.io.BufferedInputStream extends InputStream`

- `public BufferedInputStream(InputStream in)`：创建一个`BufferedInputStream`并保存其参数，即输入流`in`，以便将来使用；
- `public BufferedInputStream(InputStream in, int size)`：创建具有指定缓冲区大小的`BufferedInputStream`并保存其参数，即输入流`in`，以便将来使用；

**参数**：

​		`InputStream in`：字节输入流，传递`FileInputStream`，缓冲流会给`FileInputStream`增加一个缓冲区，提高`FileInputStream`的读取效率。

​		`int size`：指定缓冲流内部缓冲区的大小，不指定就默认（1024）。

**使用步骤**：

1. 创建一个`FileInputStream`对象，构造方法中绑定要读取的数据源；
2. 创建`BufferedInputStream`对象，构造方法中传递`FileInputStream`对象，提高`FileInputStream`对象的读取效率；
3. 使用`BufferedInputStream`对象中的`read()`方法来读取文件；
4. 释放资源。

代码示例：

```java
public class BufferedInputStreamMain {
    public static void main(String[] agrs) {
        // 1.创建一个字节输入流对象
        FileInputStream fis = new FileInputStream("a.txt");
        // 2.创建一个字节输入缓冲流对象
        BufferedInputStream bis = new BufferedInputStream(fis);
        // 3.调用read()方法来读取数据
        int len = 0;
        while((len = bis.read()) != -1) {
            System.out.println(len);
        }
        // 4.释放资源（关闭输入缓冲流后，会自动把输入流也关闭，不用手动关闭）
        bis.close();
        
        /*
        	int read(byte[] b)
        */
        byte[] bytes = new byte[1024];
        while ((len = bis.read(bytes)) != -1) {
            System.out.println(new String(bytes), 0, len);
        }
    }
}
```

## 8.4 字符缓冲输出流

**构造方法**：

`java.io.BufferedWriter extends Writer`

- `public BufferedWriter(Writer out)`：创建一个使用默认大小输出缓冲区的缓冲字符输出流；
- `public BufferedWriter(Writer out, int size)`：创建一个使用给定大小输出缓冲区的新缓冲字符输出流；

**参数**：

​		`Writer out`：字符输出流，传递`FileWriter`，缓冲流会给`FileWriter`增加一个缓冲区，提高`FileWriter`的写入效率；

​		`int size`：指定缓冲流内部缓冲区的大小，不指定就默认（1024）。

**特有的成员方法**：

- `void newLine()`：写入一个行分隔符，会根据不同的操作系统，获取不同的行分割符。

**使用步骤**：

1. 创建字符缓冲输出流对象，构造方法中传递字符输出流；
2. 调用字符缓冲输出流中的方法`write()`，把数据写入到内存缓冲区中；
3. 调用字符缓冲输出流中的方法`flush()`，把内存缓冲区中的数据，刷新到文件中；
4. 释放资源。

代码示例：

```java
public class BufferedWriter {
    public static void main(Stirng[] args) throws IOException {
        // 1.创建字符缓冲输出流对象
        BufferedWriter bw = new BufferedWriter(new FileWriter("out.txt"));
        // 2.调用write()方法
        for (int i = 0; i < 10; i++) {
            bw.write("传智播客");
            // bw.write("\r\n");
            bw.newLine(); // 等效换行符
        }
        // 3.刷新
        bw.flush();
        // 4.释放资源
        bw.close();
    }
}
```

## 8.5 字符缓冲输入流

**构造方法**：

`java.io.BufferedReader extends Reader`

- `public BufferedReader(Reader in)`：创建一个使用默认大小输入缓冲区的缓冲字符输入流。
- `public BufferedReader(Reader in, int size)`：创建一个使用指定大小输入缓冲区的缓冲字符输入流。

**参数**：

​		`Reader in`：字符输入流，传递`FileReader`，缓冲流会给`FileReader`增加一个缓冲区，提高`FileReader`的读取效率。

​		`int size`：指定缓冲流内部缓冲区的大小，不指定就默认（1024）。

**特有的成员方法**：

- `String readLine()`：读取一个文本行，读取一行数据。

  行的终止符号：通过下列字符之一即可认为某行已终止：换行（'\n'）、回车（'\r'）或回车后直接跟着换行（"\r\n"）。

  返回值：包含改行内容的字符串，不包含任何终止符，如果已到达流末尾，则返回`null`。

**使用步骤**：

1. 创建字符缓冲输入流对象，构造方法中传递字符输入流；
2. 使用字符缓冲输入流对象中的方法`read()`、`readLine()`读取文本；
3. 释放资源。

```java
public class BufferedReader {
    public static void main(String[] args) throws IOException {
        // 1.创建字符缓冲输入流对象，构造方法中传递字符输入流；
        BufferedReader br = new BufferedReader(new FileReader("in.txt"));
        // 2.调用readLine()方法
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
        // 3.释放资源
        br.close();
    }
}
```



# 九 转换流

## 9.1 字符编码和字符集

### 9.1.1 字符编码

计算机中储存的信息都是用二进制数表示的，而我们在屏幕上看到的数字、英文、标点符号、汉字等字符是二进制数转换之后的结果。按照某种规则,将字符存储到计算机中，称为**编码**。反之，将存储在计算机中的二进制数按照某种规则解析显示出来，称为**解码**。比如说，按照A规则存储，同样按照A规则解析，那么就能显示正确的文本符号。反之，按照A规则存储，再按照B规则解析，就会导致乱码现象。

**编码**：字符（能看懂的） ---->   字节（看不懂的）

**解码**：字节（看不懂的） ---->    字符（能看懂的）

- **字符编码**`Character Encoding`：就是一套自然语言的字符与二进制数之间的对应规则。

  编码表：生活中文字和计算机中二进制的对应规则。

### 9.1.2 字符集

- **字符集**`Charset`：也叫编码表，是一个系统支持的所有字符的集合，包括各国家文字、标点符号、图形符号、数字等。

计算机要准确的存储和识别各种字符集符号，需要进行字符编码，一套字符集必然至少有一套字符编码。常见字符集有`ASCII`字符集、`GBK`字符集、`Unicode`字符集。

<center><a href="https://imgtu.com/i/csvLvt"><img src="https://z3.ax1x.com/2021/04/13/csvLvt.png" alt="csvLvt.png" border="0" /></a></center>

可见，当指定了**编码**，它所对应的**字符集**自然就指定了，所以**编码**才是我们最终要关心的。

## 9.2 编码引出的问题

在IDEA中，使用`FileReader`，读取项目中的文本文件。由于IDEA的设置，都是默认的`UTF-8`编码，所以没有任何问题。但是，当读取Windows系统中创建的文本文件时，由于

Windows系统的默认是GBK编码，就会出现乱码。

## 9.3 InputStreamReader类

转换流`java.io.InputStreamReader`，是`Reader`的子类，是从字节流到字符流的桥梁。它读取字节，并使用指定的字符集将其解码为字符。它的字符集可以由名称指定，也可以接受平台的默认字符集。

### 9.3.1 构造方法

- `InputStreamReader(InputStream in)`：创建一个使用默认字符集的字符流。
- `InputStreamReader(InputStream in, String charsetName)`：创建一个指定字符集的字符流。

**参数**：

​		`InputStream in`：字节输入流，用来读取文件中保存的字节；

​		`String charsetName`：指定的编码表名称，不区分大小写，可以是`utf-8`、`UTF-8`、`gbk`、`GBK`...不指定默认使用`UTF-8`；

**使用步骤**：

1. 创建`InputStreamReader`对象，构造方法中传递字节输入流和指定的编码表名称；
2. 使用`InputStreamReader`对象中的`read()`方法读取文件；

**注意事项**：

​		构造方法中指定的编码表名称要和文件的编码相同，否则会发生乱码。

### 9.3.2 指定编码读取

```java
public class ReaderDemo {
    public static void main(String[] args) {
        // 定义文件路径，文件为gbk编码
        String FileName = "E:\\file_gbk.txt";
        // 1.创建流对象，默认UTF8编码
        InputStreamReader isr = new InputStream(new FileInputStream(FileName), "UTF-8");
        // 2.调用read()方法
        int len = 0;
        while((len = isr.read()) != -1) {
            System.out.println(len);
        }
        // 3.释放资源
        isr.close();
    }
}
```

## 9.4 OutputStreamWriter

转换流`java.io.OutputStreamWriter`，是`Writer`的子类，是字符流通向字节流的桥梁。使用指定的字符集将字符编码为字节。它的字符集可以由名称指定，也可以接受平台的默认字符集。

### 9.4.1 构造方法

- `OutputStreamWriter(OutputStream in)`：创建一个使用默认字符集的字符流；
- `OutputStreamWriter(OutputStream in, String charsetName)`：创建一个指定字符集的字符流。

**参数**：

​		`OutputStre out`：字节输出流，可以用来写转换之后的字节到文件中；

​		`String charsetName`：指定的编码表名称，不区分大小写，可以是`utf-8`、`UTF-8`、`gbk`、`GBK`...不指定默认使用`UTF-8`；

**使用步骤**：

1. 创建`OutputStreamWriter`对象，构造方法中传递字节输出流和指定的编码表名称；

2. 使用`OutputStreamWriter`对象中的`Writer()`方法，把字符转化为字节存储到缓冲区中（编码）；
3. 使用`OutputStreamWriter`对象中的`flush()`方法，把内存缓冲区中的字节刷新到文件中（使用字节流写字节的过程）；
4. 释放资源。

### 9.4.2 指定编码写出

```java
public class OutputDemo {
    public class void main(String[] args) throws IOException {
        // 定义文件路径
        String FileName = "E:\\out.";
        // 1.创建流对象，默认UTF编码
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(FileName), "UTF-8");
        // 2.写出数据
        osw.writer("你好"); // 保存为6个字节
        // 3.刷新
        osw.flush();
        // 4.释放资源
        osw.close();
    }
}
```



# 十 序列化

## 10.1 概述

Java提供了一种对象**序列化**的机制，用一个字节序列可以表示一个对象，该字节序列包含该`对象的数据`、`对象的类型`、`对象中存储的属性`等信息。字节序列写出到文件之后，相当于文件中**持节保存**了一个对象的信息。

反之，该字节序列还可以从文件中读取回来，重构对象，对它进行**反序列化**。`对象的数据`、`对象的类型`、`对象中存储的数据`信息，都可以用来在内存中创建对象。

**序列化图解**：

<center><a href="https://imgtu.com/i/cyAqnH"><img src="https://z3.ax1x.com/2021/04/13/cyAqnH.png" alt="cyAqnH.png" border="0" /></a></center>

## 10.2 ObjectOutputStream类

`java.io.ObjectOutputStream extends OutputStream`类，将Java对象的原始数据类型写出到文件，实现对象的持久存储。

### 10.2.1 构造方法

- `public ObjectOutputStream(OutputStream out)`：创建一个指定`OutputStream`的`ObjectOutputStream`。

**参数**：

​		`OutputStream out`：字节输出流；

**特有的成员方法**：

​		`void writeObject(Object obj)`：将指定的对象写入`ObjectOutputStream`。

**使用步骤**：

1. 创建`ObjectOutputStream`对象，构造方法中传递字节输出流；
2. 使用`ObjectOutputStream`对象中的`writeObject()`方法，把对象写入到文件中；
3. 释放资源。

**构造举例**：

```java
FileOutputStream fileOut = new FileOutputStream("employee.txt");
ObjectOutputStream out = new ObjectOutputStream(fileOut);
```

### 10.2.2 序列化操作

一个对象要想序列化，必须满足**两个条件**：

- 该类必须实现`java.io.Serializable`接口，`Serializable`是一个标记接口，不实现此接口的类将不会使任何状态序列化或反序列化，会抛出`NotSerialiazableException`。

- 该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用`transient`关键字修饰。

  `static`关键字：静态关键字

  ​		静态优于非静态加载到内存中（静态优于对象进入到内存中）；

  ​		被`static`修饰的成员变量不能被序列化，序列化的都是对象；

  `transient`关键字：瞬态关键字

  ​		被`transient`关键字修饰的成员变量，不能被序列化。

## 10.3 ObjectInputStream类

`ObjectInputStream extends InputStream`反序列化流，将之前使用`ObjectOutputStream`序列化的原始数据恢复为对象。

### 10.3.1 构造方法

- `public ObjectInputStream(InputStream in)`：创建一个指定的`InputStream`的`ObjectInputStream`。

**参数**：

​		`InputStream in`：字节输入流；

**特有的成员方法**：

​		`Object readObject()`：从`ObjectInputStream`读取对象。

​		该方法声明抛出了`ClassNotFoundException`异常（class文件找不到异常），当不存在对象的class文件时抛出此异常。

**使用步骤**：

1. 创建`ObjectInputStream`对象，构造方法中传递字节输入流；
2. 使用`ObjectInputStream`对象中的`readObject()`读取保存对象的文件；
3. 释放资源；
4. 使用读取出来的对象（打印）。

### 10.3.2 反序列化操作1

如果能找到一个对象的class文件，我们可以进行反序列化操作，调用`ObjectInputStream`读取对象的方法：

- `public final Object readObject()`：读取一个对象。

代码示例：

```java
public class DeserializableDemo {
    public static void main(String[] args) {
        Employee e = null;
        try {
            // 创建反序列化流
            FileInputStream fileIn = new FileInputStream("employee.txt");
            ObjectInputStream in = new ObjectInputStream(fileIn);
            // 读取一个对象
            e = (Employee) in.readObject();
            // 释放资源
            in.close();
            fileIn.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 10.3.3 反序列化操作2

另外，当JVM反序列化对象时，能找到class文件，但是class文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个`InvalidClassException`异常，发生这个异常的原因如下：

- 该类的序列版本号与从流中读取的类描述符的版本号不匹配；
- 该类包含未知数据类型；
- 该类没有可访问的无参数构造方法；

`Serializable`接口给需要序列化的类，提供了一个序列版本号。`serialVersionUID`该版本号的目的在于验证序列化的对象和对应类是否版本匹配。

代码示例：

```java
public class Employee implements java.io.Serializable {
    // 加入序列版本号
    private static final long serialVersionUID = 1L;
    public String name;
    public String address;
}
```

**异常产生原因**：

<center><a href="https://imgtu.com/i/cyQ4qf"><img src="https://z3.ax1x.com/2021/04/13/cyQ4qf.png" alt="cyQ4qf.png" border="0" /></a></center>



# 十一 打印流

## 11.1 概述

平时我们在控制台打印输出，是调用`print()`方法和`println()`方法完成的，这两个方法都来自于`java.io.PrintStream extends OutputStream`类，该类能够方便地打印各种数据类型的值，是一种便捷的输出方式。

**特点**：

1. 只负责数据的输出，不负责数据的读取；
2. 与其他输出流不同，`PrintStream`永远不会抛出`IOException`异常；
3. 有特有的方法，`print()`、`println()`，都可以输出任意类型的值。

## 11.2 PrintStream类

### 11.2.1 构造方法

- `public PrintStream(String fileName)`：输出的目的地是一个文件路径；
- `public PrintStream(OutputStream out)`：输出的目的地是一个字节输出流；
- `public PrintStream(File file)`：输出的目的地是一个文件。

**注意事项**：

1. 如果使用继承自父类的`write()`方法写数据，那么查看数据的时候会查询编码表。（97->a）
2. 如果使用自己特有的`print()`、`println()`方法写数据，写的数据原样输出。（97->97）

### 11.2.2 改变打印流向

`System.out`就是`printStream`类型的，只不过它的流向是系统规定的，打印在控制台上。

使用`System.out`

代码示例：

```java
public class printMain {
    public static void main(String[] args) {
        
    }
}
```

