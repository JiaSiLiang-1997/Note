# 一 简介

## 1.1 什么是XML

XML是可扩展的标记性语言。

## 1.2 XML的作用

XML的主要作用有？

- 用来保存数据，而且这些数据具有自我描述性；

  ```xml
  Student[id=1,name="华仔"]
  Student[id=2,name="张三"]
  
  students.xml
  <students>
      <student>
      	<id>1</id>
          <name>华仔</name>
      </student>
      <student>
      	<id>2</id>
          <name>张三</name>
      </student>
  </students>
  ```

- 它还可以做为项目或者模块的配置文件；

- 还可以做为网络传输数据的格式（现在JSON为主）；



# 二 XML语法

- 文档声明
- 元素（标签）
- XML属性
- XML注释
- 文本区域（CDATA区 ）



## 2.1 文档声明

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--
    <?xml version="1.0" encoding="utf-8" ?>
    以上内容就是XML文件的声明
    version="1.0"       表示XML的版本
    encoding="utf-8"    表示XML文件本身的编码
-->
<!---->
<books> <!--books   表示多个图书信息-->
    <book sn="SN123412123412">  <!--book    表示一个图书信息    SN表示图书序列号-->
        <name>时间简史</name>      <!--name标签表示书名-->
        <author>霍金</author>     <!--author表示作者-->
        <price>75</price>   <!--price表示图书价格-->
    </book>
    <book sn="SN123412123412">  <!--book    表示一个图书信息    SN表示图书序列号-->
        <name>平凡的世界</name>      <!--name标签表示书名-->
        <author>路遥</author>     <!--author表示作者-->
        <price>9.9</price>   <!--price表示图书价格-->
    </book>
</books>
```



## 2.2 XML注释

HTML 和 XML 注释一样：<!--	HTML 注释 -->



## 2.3 元素（标签）

HTML的标签：

- 格式：<标签名>封装的数据</标签名>
- 单标签：<标签名/>
- 双标签：<标签名>封装的数据</标签名>
- 标签名大小写不敏感
- 标签有属性，有基本属性和事件属性。
- 标签要闭合（不闭合，HTML中不报错。但我们要养成良好的书写习惯，闭合）

### 2.3.1 什么是XML元素

**什么是XML元素？**

**XML元素**指的是从（且包括）开始标签直到（且包括）结束标签的部分。

元素可包含其他元素，文本或者两者的混合物。元素也可以拥有属性。

```xml
<bookstore>
    <book category="CHILDREN">
        <title>Harry Potter</title>
        <author>J K. Rowling</author>
        <year>2005</year>
        <price>29.99</price>
    </book>
    <book category="WEB">
        <title>Learning XML</title>
        <author>Erik T. Ray</author>
        <year>2003</year>
        <price>39.95</price>
    </book>
</bookstore>
```

在上例中，< bookstore >和< book >都拥有**元素内容**，因为它们包含了其他元素。<author>只有**文本内容**，因为它仅包含文本。

### 2.3.2 XML 命名规则

XML元素必须遵循以下命名规则：

- 名称可以包含字母、数字以及其他的字母；

  ```xml
  <book id="SN213412341">
      <author>班导</author>
      <name>Java 编程思想</name>
      <price>9.9</price>
  </book>
  ```

- 名称不能以数字或者标点符号开始

- 名称不能以字符"xml"（或者 XML、Xml）开始
- 名称不能包含空格

### 2.3.3 XML元素（标签）也分成单标签和双标签

```xml
<books>
    <book sn="SN123412123413" name="辟邪剑谱" author="林平之" price="999999" />
</books>
```



## 2.4 XML属性

XML的标签属性和HTML的标签属性是非常类似的，**属性可以提供元素的额外信息。**

在标签上可以书写属性：

- 一个标签上可以书写多个属性，**每个属性的值必须使用 引号 引起来。**
- 书写规则和标签的书写规则一致。



## 2.5 语法规则

- 所有XML元素都必须有关闭标签（也就是闭合）
- XML标签对大小写敏感

- XML 必须正确的嵌套

- XML文档必须有根元素

  根元素就是顶级元素，

  没有父标签的元素，叫顶级元素

  根元素是没有父标签的顶级元素，而且是唯一一个才行。

- XML的属性值必须加引号

- 文本区域（CDATA区）

  CDATA语法可以告诉XML解析器，CDATA里的文本内容，只是纯文本，不需要XML语法解析。

  CDATA格式：

  ```xml
  <![CDATA[这里可以把你输入的字符原样显示，不会解析XML]]>
  ```



# 三 XML解析技术介绍

XML可扩展的标记语言。

​		不管是HTML文件还是XML文件它们都是标记型文档，都可以使用w3c组织指定的DOM技术来解析。

![image-20210622163001948](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622163001948.png)

​		**document对象表示的是整个文档（可以是html文档，也可以是xml文档）**

​		早期JDK为我们提供了两种xml解析技术Dpm和Sax简介(已经过时，但我们需要知道这两种技术)

​		DOM解析技术是W3C组织制定的，而所有的编程语言都对这个解析技术使用了自己语言的特点进行实现。Java对DOM技术解析标记也做了实现。
​		sun公司在JDKS版本对DOM解析技术进行升级: SAX( Simple API for XML )
​		SAX解析，它跟W3C制定的解析不太- -样。它匙以类似事件机制通过回调告诉用户当前正在解析的内容。

​		它是一行一行的读职xml文件进行解析的。不会创建大量的dom对象。
所以它在解析xml的时候，在内存的使用上。和性能上。都优于Dom解析。
​		第三方的解析:
​				jdom在dom基础上进行了封装、
​				dom4j又对jdom进行了封装。
​				pull主要用在Android 手机开发，是在跟sax非常类似都是事件机制解析xml 文件。

**注意**：这个Dom4j 它是第三方的解析技术。我们需要使用第三方给我们提供好的类库才可以解析XML文件。



# 四 dom4j解析技术

由于dom4j它不是sun公司的技术，而属于第三方公司的技术，我们需要使用dom4j就需要到dom4j官网下载dom4j的jar包。

## 4.1 Dom4j类库的使用

略

## 4.2 解析步骤

需要解析的books.xml文件内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<book>
	<book sn="SN12341232">
    	<name>辟邪剑谱</name>
        <price>9.9</price>
        <author>班主任</author>
    </book>
    <book sn="SN12341231">
    	<name>葵花宝典</name>
        <price>99.99</price>
        <author>班长</author>
    </book>
</book>
```

解析获取Document对象的代码

第一步，先创建SaxReader对象。这个对象，用于读取XML文件，并创建Document

```java
@Test
public void test1() {
    // 创建一个SaxReader输入流，去读取XML配置文件，生成Document对象
    SAXReader saxReader = new SAXReader();
    Document document = saxReader.read("../books.xml");
}
```

第二步，读取books.xml文件生成Book类

```java
@Test
public void test2() {
    // 1 读取books.xml文件
    SAXReader reader = new SAXReader();
    // 2 通过Document对象获取根元素
    Document document = reader.read("src/books.xml");
    Element rootElement = document.getRootElement();
    // 3 通过根元素获取book标签对象
    List<Element> books = rootElement.elements("book");
    // 4 遍历，处理每个book标签转换为Book类
    for (Element book : books) {
        // asXML() 把标签对象，转换为标签字符串
        Element nameElement = book.element("name");
        // getText() 可以获取标签中的文本内容
        String nameText = nameElement.getText();
        // 直接 获取指定标签名的文本内容
        String priceText = book.elementText("price");
    }
}
```

