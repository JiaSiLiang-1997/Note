# 一 JSP简介

## 1.1 定义

JSP的全称是 Java Server Pages。Java服务器页面



## 1.2 主要作用

代替Servlet程序回传HTML页面的数据。因为Servlet程序回传HTML页面数据是意见非常繁琐的事情。开发成本和维护成本都极高。

**Servlet回传数据示例**：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
  这是HTML页面数据
<form action="http://localhost:8080/JSP/PrintHtml" method="get">
    <input type="submit">
</form>
</body>
</html>
```

```java
@WebServlet(name = "PrintHtml", value = "/PrintHtml")
public class PrintHtml extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置服务器和浏览器的编码
        response.setContentType("text/html; charset=UTF-8");

        // 1.得到响应流
        PrintWriter writer = response.getWriter();

        // 2.通过响应的回传流回传HTML页面数据
        writer.write("<!DOCTYPE html>\r\n");
        writer.write("<html lang=\"en\">\r\n");
        writer.write("<head>\r\n");
        writer.write("    <meta charset=\"UTF-8\">\r\n");
        writer.write("    <title>Title</title>\r\n");
        writer.write("</head>\r\n");
        writer.write("<body>\r\n");
        writer.write("这是回传的HTML\r\n");
        writer.write("</body>\r\n");
        writer.write("</html>\r\n");
    }
}
```



**JSP页面示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    这是HTML页面数据
</body>
</html>
```

JSP页面和HTML页面一样，都是存放在WEB目录下，访问也跟HTML页面一样。

比如：

​		在web目录下有如下得文件：

​		web目录

​				a.html页面		访问地址：http://ip:port/工程路径/a.html

​				b.jsp页面		   访问地址：http://ip:port/工程路径/b.jsp





# 二 JSP的本质

JSP页面本质上是一个Servlet程序。

当第一次访问JSP页面的时候，Tomcat服务器会把JSP页面翻译成为一个Java源文件。并且对它进行编译成为.class字节码程序。

Java源文件里面的内容是：

![image-20210622172402517](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622172402517.png)

HttpJspBase类，它直接地继承了HttpServlet类，即JSP翻译出来的Java类间接继承了HttpServlet类，是一个Servlet程序。



**总结**：JSP就是Servlet程序。

**注意**：在翻译出来的Servlet程序源代码中，不难发现，其底层实现，也是通过输出流，把HTML页面数据回传给客户端。



# 三 JSP的三种语法

## 3.1 JSP头部的page指令

JSP的page指令可以修改JSP页面中一些重要的属性，或者行为。

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```

**常用属性**：

|         属性         |                             功能                             |
| :------------------: | :----------------------------------------------------------: |
|   **language**属性   |        表示JSP翻译后是什么语言文件，暂时只支持Java。         |
| **contentType**属性  | 表示JSP返回的数据类型是什么？也是源码中的response.setContentType()参数值。 |
| **pageEncoding**属性 |              表示当前JSP页面文件本身的字符集。               |
|    **import**属性    |             跟Java源代码中一样，用于导包、导类。             |
|  **errorPage**属性   |     设置当JSP页面运行时出错，自动跳转去的错误页面路径。      |
| **isErrorPage**属性  | 设置当前JSP页面是否是错误信息页面，默认是false；如果是true可以获取异常信息。 |
|   **session**属性    | 设置访问当前JSP页面，是否会创建HttpSession对象，默认是true。 |
|   **extends**属性    |             设置JSP翻译出来的java类默认继承谁？              |

​		以下两个属性是给out输出流使用：

|       属性        |                             功能                             |
| :---------------: | :----------------------------------------------------------: |
| **autoFlush**属性 | 设置当out输出流缓冲区满了之后，是否自动刷新缓冲区。默认值为true。 |
|  **buffer**属性   |               设置out缓冲区的大小，默认是8kb。               |

**注意**：

- **errorPage**表示出错误后自动跳转去的路径，这个路径一般都是以斜杠打头，它表示请求地址为http://ip:port/工程路径/ ，映射到代码web目录。



## 3.2 JSP中的常用脚本

### 3.2.1 声明脚本（极少使用）

**格式**：

```jsp
<%! 声明java代码 %>
```

**作用**：可以给JSP翻译出来的java类定义属性和方法，甚至是静态代码块、内部类等。

**代码示例**：

```jsp
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %><%--
  Created by IntelliJ IDEA.
  User: Susanoo
  Date: 2021/5/31
  Time: 16:32
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--1. 声明类的属性--%>
    <%!
        private Integer id;
        private String name;
        private static Map<String, Object> map;
    %>
<%--2. 声明static静态代码块--%>
    <%!
        static {
            map = new HashMap<String, Object>();
            map.put("key1", "value1");
            map.put("key2", "value2");
            map.put("key3", "value3");
        }
    %>
<%--3. 声明类的方法--%>
    <%!
        public int abc() {
            return 12;
        }
    %>
<%--4. 声明内部类--%>
<%!
    public static class A {
        private final Integer id = 12;
        private final String abc= "abc";
    }
%>
</body>
</html>
```



### 3.2.2 表达式脚本（常用）

**格式**：

```jsp
<%=表达式%>
```

**作用**：在JSP页面上输出数据。

**代码示例**：

```jsp
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %><%--
  Created by IntelliJ IDEA.
  User: Susanoo
  Date: 2021/5/31
  Time: 16:32
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--2. 声明static静态代码块--%>
    <%!
        static {
            map = new HashMap<String, Object>();
            map.put("key1", "value1");
            map.put("key2", "value2");
            map.put("key3", "value3");
        }
    %>
<%--1. 输出整型--%>
<%=12%> <br>
<%--2. 输出浮点型--%>
<%=12.12%> <br>
<%--3. 输出字符串--%>
<%="输出字符串！"%> <br>
<%--4. 输出对象--%>
<%=map%> <br>
</body>
</html>
```

**特点**：

- 所有的表达式脚本都会被翻译到`_jspService()`方法中；
- 表达式脚本都会被翻译成为`out.print()`输出到页面上；
- 由于表达式脚本翻译的内容都在`_jspService()`方法中，所以`_jspService()`方法中的对象都可以直接使用；
- 表达式脚本中的表达式不能以分号结束；



### 3.2.3 代码脚本

**格式**：

```jsp
<%
	java语句;
%>
```

**作用**：可以在jsp页面中，编写需要的功能（Java语句）。



**if语句**：

```jsp
<%--1. if语句--%>
<%
    int i = 12;
    if (i == 12) {
        System.out.println("YES");
    } else {
        System.out.println("NO");
    }
%>
```



**for循环语句**：

```jsp
<%--2. for循环--%>
<%
    for (int j = 0; j < 10; j++) {
        System.out.println("JSL");
    }
%>
```



**翻译后Java文件中_jspService方法内的代码都可以写**

```jsp
<%--3. 翻译后Java文件中_jspService方法内的代码都可以写--%>
<%
    String username = request.getParameter("username");
    System.out.println(username);
%>
```

**特点**：

-  代码脚本翻译之后都在`_jspService()`方法中；
- 代码脚本由于翻译到`_jspService()`方法中，所以在`_jspService()`方法中的现有对象都可以直接使用；
- 还可以由多个代码脚本块组合完成一个完整的Java语句；
- 代码脚本还可以和表达式脚本一起组合使用，在JSP页面上输出数据；



## 3.3 JSP中的三种注释

### 3.3.1 HTML注释

```jsp
<!--	这是HTML注释	-->
```

**注意**：HTML注释会被翻译到Java源代码中，在`_jspService()`方法里，以`out.write()`输出到客户端。

### 3.3.2 JAVA注释

```jsp
<%
	// 单行java注释
	/*
		多行java注释
	*/
%>
```

**注意**：java注释会被翻译到Java源代码中。

### 3.3.3 JSP注释

```jsp
<%--	JSP注释	--%>
```

**注意**：JSP注释可以注掉JSP页面中所有代码。



# 四 JSP九大内置对象

**JSP内置对象**：指Tomcat在翻译JSP页面成为Servlet源代码后，内部提供的九大对象。

![image-20210622172433811](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622172433811.png)



# 五 JSP四大域对象

**四大域对象**：

|     变量名      |          类名          |                        存取范围                        |
| :-------------: | :--------------------: | :----------------------------------------------------: |
| **pageContext** |  **PageContextimpl**   |                 当前JSP页面范围内有效                  |
|   **request**   | **HttpServletRequest** |                     一次请求内有效                     |
|   **session**   |    **HttpSession**     |                   一次会话范围内有效                   |
| **application** |   **ServletContext**   | 整个web工程范围内都有效（只要web工程不停止，数据都在） |

**域对象是可以像Map一样存取数据的对象。四个域对象功能一样，不同的是它们对数据的存取范围。**

**会话**：打开浏览器访问服务器，直到关闭浏览器。

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        // 向四个域中都分别保存了数据
        pageContext.setAttribute("key","pageContext");
        request.setAttribute("key", "request");
        session.setAttribute("key", "session");
        application.setAttribute("key", "application");
    %>
    pageContext域是否有值：<%=pageContext.getAttribute("key")%> <br>
    request域是否有值：<%=request.getAttribute("key")%> <br>
    session域是否有值：<%=session.getAttribute("key")%> <br>
    application域是否有值：<%=application.getAttribute("key")%> <br>
</body>
</html>
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    pageContext域是否有值：<%=pageContext.getAttribute("key")%> <br>
    request域是否有值：<%=request.getAttribute("key")%> <br>
    session域是否有值：<%=session.getAttribute("key")%> <br>
    application域是否有值：<%=application.getAttribute("key")%> <br>
</body>
</html>
```

**注意**：四个域对象都可以存取数据，在使用的时候，优先顺序分别是：**从小到大的范围的顺序。**

**pageContext**  ==>  **request**  ==>  **session**  ==>  **application**

主要原因是能够有效利用内存，让不再需要的数据，尽快释放。



# 六 out输出和response.getWriter输出

response表示响应，经常用于设置返回给客户端的内容（输出）。

out也是给用户做输出使用的。

![image-20210622172451685](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622172451685.png)

由于JSP翻译之后，底层源代码都是使用out输出，所以一般情况下。我们在JSP页面中统一使用out来进行输出。避免打乱页面输出内容的顺序。



**注意**：

​		`out.write()`方法输出字符串没有问题；

​		`out.print()`输出任意数据都没有问题（都转换成为字符串后调用`write()`输出）；

**总结**：在JSP页面中，可以统一使用`out.print()`来进行输出。



# 七 JSP的常用标签

## 7.1 JSP静态包含

**main.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    头部信息 <br>
    主体内容 <br>
    <%--
        静态包含  <%@ include file=""%>
        file属性指定要包含的JSP页面的路径
        地址中第一个斜杠表示为 http://ip:port/工程路径/    映射到代码的web目录
    --%>
    <%@ include file="footer.jsp"%>
</body>
</html>
```

**footer.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
脚页信息 <br>
```

**特点**：

- 静态包含不会翻译被包含的JSP页面；
- 静态包含其实是把被包含的JSP页面的代码拷贝到包含的位置执行输出；



## 7.2 JSP动态包含

**main.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    头部信息 <br>
    主体内容 <br>
    <%--
        动态包含    <jsp:include page=""></jsp:include>
        page属性指定要包含的JSP页面的路径
        动态包含也可以像静态包含一样，把被包含的内容执行输出到包含位置
    --%>
    <jsp:include page="footer.jsp">
        <jsp:param name="username" value="bbj" />
        <jsp:param name="password" value="root" />
    </jsp:include>
</body>
</html>
```

**footer.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    脚页信息 <br>
    用户名：<%=request.getParameter("username")%>
    密码：<%=request.getParameter("password")%>
</body>
</html>

```

**特点**：

- 动态包含会把包含的JSP页面也翻译成为java代码；

- 动态包含底层代码使用如下代码去调用被包含的JSP页面执行输出。

  `JspRuntimeLibrary.include(request, response, "/include/footer.jsp", out, false)`

**底层原理**：

![image-20210622172531985](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622172531985.png)

- 动态包含，还可以传递参数。



## 7.3 JSP标签-转发

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    头部信息 <br>
    主体内容 <br>
    底部信息 <br>
    <%--
        <jsp:forward page=""></jsp:forward> 是请求转发标签，它的功能就是请求转发
        page 属性设置请求转发的路径
    --%>
    <jsp:forward page="/index.jsp"></jsp:forward>
</body>
</html>

```



![image-20210622172553537](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622172553537.png)



# 八 Listener 监听器

- Listener监听器是Java Web的三大组件之一。Java Web的三大组件分别是：Servlet程序、Filter过滤器、Listener监听器。
- Listener是Java EE 的规范，就是接口。
- 监听器的作用是，监听某种事务的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理。



## 8.1 ServletContextListener监听器

ServletContextListener可以监听ServletContext对象的创建和销毁。

ServletContext对象在web工程启动的时候创建，在web工程停止的时候销毁。

监听到创建和销毁之后都会分别调用`ServletContextListener()`监听器的方法反馈。

两个方法分别是：

### 8.1.1 contextInitialized(ServletContextEvent sce)

在ServletContext对象创建之后马上调用，做初始化；

### 8.1.2 contextDestroyed(ServletContextEvent sce)

在ServletContext对象销毁之后马上调用，

### 8.1.3 使用步骤

1. 编写一个类去实现ServletContextListener；
2. 实现其两个回调方法；
3. 到web.xml中去配置监听器；

**代码示例**：

```java
public class MyServletContextListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContext对象被创建了");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext对象被销毁了");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--配置监听器-->
    <listener>
        <listener-class>com.example.listener.MyServletContextListener</listener-class>
    </listener>
</web-app>
```

