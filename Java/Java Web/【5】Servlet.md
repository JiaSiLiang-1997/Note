# 一 Servlet介绍

## 1.1 什么是Servlet

- Servlet 是 Java EE 的规范之一，规范就是接口。
- Servlet 是 Java Web 三大组件之一。三大组件：Servlet程序、Filter过滤器、Listener监听器。
- Servlet 是运行在服务器上的一个Java小程序，**可以接收客户端发送过来的请求，并响应数据给客户端。**



## 1.2 创建Servlet程序

1. 编写一个类去实现Servlet接口；
2. 实现 Service 方法，处理请求，并响应数据；
3. 到 web.xml 中去配置 servlet 程序的访问地址；

配置示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--  servlet标签给Tomcat配置Servlet程序  -->
    <servlet>
        <!--  servlet-name    给Servlet程序起别名（一般是类名）  -->
        <servlet-name>HelloServlet</servlet-name>
        <!--  servlet-class   是Servlet程序的全类名  -->
        <servlet-class>com.example.Servlet.HelloServlet</servlet-class>
    </servlet>

    <!--  servlet-mapping 给Servlet程序配置访问地址  -->
    <servlet-mapping>
        <!--  servlet-name  告诉服务器，当前配置的地址给哪个Servlet程序使用  -->
        <servlet-name>HelloServlet</servlet-name>
        <!--
        配置访问地址
        /           斜杠在服务器解析的时候，表示地址为http://ip:port/工程路径
        /hello      表示地址为：http://ip:port/工程路径/hello
        -->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```



## 1.3 通过Servlet-url地址定位到Servlet程序进行访问

![image-20210622170724896](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170724896.png)



## 1.4 Servlet的生命周期

1. 执行Servlet构造器方法
2. 执行init初始化方法
3. 执行service方法
4. 执行destroy销毁方法

**注意**：

- 第1步和第2步，是在第一次访问的时候创建Servlet程序会调用。
- 第3步，每次访问都会调用。
- 第4步，在web工程停止的时候调用。



## 1.5 请求的分发处理

在Servlet程序中，service方法中：

```java
@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String method = req.getMethod();
    if ("POST".equals(method)){
        System.out.println("POST请求");
    } else if ("GET".equals(method)) {
        System.out.println("GET请求");
    }
}
```



## 1.6 通过继承HttpServlet实现Servlet程序

一般在实际项目开发中，都是使用继承**HttpServlet**类的方式去实现Servlet程序。

**使用步骤**：

1. 编写一个类去继承**HttpServlet**类；
2. 根据业务需要重写`doGet()`或`doPost()`方法；
3. 到web.xml中的配置Servlet程序的访问地址；

**使用示例**：

```java
public class HelloServlet2 extends HttpServlet {

    /**
     * doGet()方法在GET请求时调用
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("GET请求");
    }

    /**
     * doPOST()方法在POST请求时调用
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("POST请求");
    }
}
```



## 1.7 IDEA菜单生成Servlet程序

![image-20210622170835426](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170835426.png)

![image-20210622170848839](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170848839.png)



## 1.8 Servlet类的继承体系

![image-20210622170905071](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170905071.png)



# 二 ServletConfig类

ServletConfig类是Servlet程序的配置信息类。

## 2.1 ServletConfig类的三大作用

1. 可以获取Servlet程序的别名，即**servlet-name**的值；

   `servletConfig.getServletName();`

2. 获取初始化参数**init-param**；

   需要在web.xml中配置初始化参数

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <!--  servlet标签给Tomcat配置Servlet程序  -->
       <servlet>
           <!--  servlet-name    给Servlet程序起别名（一般是类名）  -->
           <servlet-name>HelloServlet2</servlet-name>
           <!--  servlet-class   是Servlet程序的全类名  -->
           <servlet-class>com.example.Servlet.HelloServlet2</servlet-class>
           <!--  init-param是初始化参数  -->
           <init-param>
               <!--  param-name是参数名  -->
               <param-name>username</param-name>
               <!--  param-value是参数值  -->
               <param-value>root</param-value>
           </init-param>
       </servlet>
   </web-app>
   ```

   然后在java程序中就可以获取到这些初始化参数。

   `servletConfig.getInitParameter("参数名");`

3. 获取ServletContext对象；

   `servletConfig.getServletContext();`



## 2.2 补充说明

Servlet程序和ServletConfig对象都是由Tomcat服务器负责创建，我们负责使用；

Servlet程序默认是第一次访问的时候创建；

ServletConfig是每个Servlet程序创建时，就创建一个对应的ServletConfig对象。



# 三 ServletContext类

## 3.1 什么是ServletContext类

1. ServletContext是一个接口，表示Servlet上下文对象；

2. 一个web工程，只有一个ServletContext对象实例；

3. ServletContext对象是一个域对象；

   **域对象**：是可以像Map一样存取数据的对象，叫做域对象。

   这里的域指的是存取数据的操作范围，范围为整个web工程。

   |            |      存数据      |      取数据      |      删除数据       |
   | :--------: | :--------------: | :--------------: | :-----------------: |
   |  **Map**   |     `put()`      |     `get()`      |     `remove()`      |
   | **域对象** | `setAttribute()` | `getAttribute()` | `removeAttribute()` |

4. ServletContext是在web工程部署启动的时候创建。在web工程停止的时候销毁。



## 3.2 ServletContext类的四个作用

1. 获取 web.xml 中配置的上下文参数 context-param；
2. 获取当前的工程路径，格式：/工程路径；
3. 获取工程部署后在服务器硬盘上的绝对路径；

4. 像Map一样存取数据；

**上下文参数配置示例**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--  context-param是上下文参数（属于整个web工程）  -->
    <context-param>
        <param-name>username</param-name>
        <param-value>context</param-value>
    </context-param>
    <context-param>
        <param-name>password</param-name>
        <param-value>root</param-value>
    </context-param>
</web-app>
```

**代码示例**：

```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    // 1. 获取 web.xml 中配置的上下文参数 context-param；
    ServletContext context = getServletConfig().getServletContext();
    String username = context.getInitParameter("username");
    String password = context.getInitParameter("password");
    System.out.println("用户名：" + username);
    System.out.println("密码：" + password);
    // 2. 获取当前的工程路径，格式：/工程路径；
    System.out.println("当前工程路径：" + context.getContextPath());
    // 3. 获取工程部署后在服务器硬盘上的绝对路径；
    /*
		斜杠/被服务器解析地址为：http://ip:port/
    */
    System.out.println("工程部署的路径是：" + context.getRealPath("/"));
    System.out.println("工程下CSS目录的绝对路径是：" + context.getRealPath("/css"));
    System.out.println("工程下imgs目录1.jpg的绝对路径是：" + context.getRealPath("/imgs/1.jpg"));
}
```





# 四 HTTP协议

## 4.1 什么是HTTP协议

**协议**：是指双方、或多方，相互约定好，大家都需要遵守的规则，叫协议。

**HTTP**：就是指，客户端和服务器之间通信时，发送的数据，需要遵守的规则，叫HTTP协议。

HTTP协议中的数据又叫报文。



## 4.2 请求的HTTP协议格式

- 客户端给服务器发送数据叫请求；

- 服务器给客户端回传数据叫响应；

请求又分为GET请求和POST请求。

### 4.2.1 GET请求

**组成**：

1. **请求行**
   - 请求的方式；											GET
   - 请求的资源路径[+?+请求参数]；             
   - 请求的协议的版本好；                            HTTP/1.1
2. **请求头**
   - key:value 组成，不同的键值对，表示不同的含义；

**分析GET请求内容**：

![image-20210622170925842](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170925842.png)



### 4.2.2 POST请求

**组成**：

1. **请求行**

   - 请求的方式；											POST
   - 请求的资源路径[+?+请求参数]；             
   - 请求的协议的版本好；                            HTTP/1.1

2. **请求头**

   - key:value 组成，不同的键值对，表示不同的含义；

   **空行**

3. **请求体**：发送给服务器的数据

**分析POST请求内容**：

![image-20210622170946806](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170946806.png)



### 4.2.3 常用的请求头的说明

- Accept：表示客户端可以接收的数据类型；
- Accept-Languege：表示客户端可以接收的语言类型；
- User-Agent：表示客户端游览器的信息；
- Host：表示请求时的服务器ip和端口号；



### 4.2.4 那些是GET请求，那些是POST请求

GET请求：

- form标签，method = get
- a标签
- link标签引入CSS
- Script标签引入JS文件
- img标签引入图片
- iframe引入html页面
- 在游览器地址栏中输入地址后敲回车

POST请求：

- form标签，method = post



## 4.3 响应的HTTP协议格式

1. **响应行**

   - 响应的协议和版本号				HTTP/1.1
   - 响应状态码                                200
   - 响应状态描述符                        OK

2. **响应头**

   - key:value									不同的响应头，有其不同的含义

   **空行**

3. **响应体**：就是回传给客户端的数据

**响应协议格式分析**：

![image-20210622171003809](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622171003809.png)



## 4.4 常见的响应码说明

- 200：表示请求成功；
- 302：表示请求重定向；
- 404：表示请求服务器已经收到了，但请求的数据不存在；（请求地址错误）
- 500：表示服务器已经收到请求，但是服务器内部错误；



## 4.5 MIME类型说明

MIME是HTTP协议中的数据类型。

MIME的英文全称是"Multipurpose Internet Mail Extensions" 多功能Internet邮件扩充服务。MIME类型的格式是"大类型/小类型"，并与某一种文件的扩展名相对应。

常见的MIME类型：

|        文件        | MIME类型                                                     |
| :----------------: | :----------------------------------------------------------- |
| 超文本标记语言文本 | .html，.html                        text/html                |
|      普通文本      | .txt                                        text/plan        |
|      RTF文本       | .rtf                                         application/rtf |
|      GIF图形       | .gif                                         image/gif       |
|      JPEG图形      | .jpeg，.jpg                            image/jpeg            |
|     au声音文件     | .au                                          audio/basic     |
|    MIDI音乐文件    | mid，.midi                            audio/midi，audio/x-midi |
| RealAudio音乐文件  | .ra，.ram                               audio/x-pn-realaaudio |
|      MPEG文件      | .mpg，.mpeg                        video/mpeg                |
|      AVI文件       | .avi                                          video/x-m svideo |
|      GZIP文件      | .gz                                           application/x-gzip |
|      TAR文件       | .tar                                          appliction/x-tar |





# 五 HttpServletRequest类

## 5.1 作用

每次只要有请求进入到Tomcat服务器，Tomcat服务器就会把请求过来的HTTP协议信息解析好封装到Request对象中。

然后传递到`service()`方法（`doGet()`和`doPost`）中，可以通过HttpServletRequest对象，获取到所有请求的信息。

## 5.2 常用方法

- `getRequestURI()`：获取请求的URI地址（资源路径）；
- `getRequestURL()`：获取请求的统一资源定位符（绝对路径）；
- `getRemoteHost()`：获取客户端的IP地址；
- `getHeader()`：获取请求头；
- `getParameter()`：获取请求的参数；
- `getParameterValues()`：获取请求的参数（多个值的时候使用）；
- `getMethod()`：获取请求的方式GET或POST；
- `setAttribute(key, value)`：设置域数据；
- `getAttribute(key)`：获取域数据；
- `getRequestDispatcher()`：获取请求转发对象；

**代码示例**：

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //        - `getRequestURI()`：获取请求的URI地址（资源路径）；
    System.out.println("URI -> " + req.getRequestURI());
    //        - `getRequestURL()`：获取请求的统一资源定位符（绝对路径）；
    System.out.println("URL -> " + req.getRequestURL());
    //        - `getRemoteHost()`：获取客户端的IP地址；
    /*
            在IDEA中，使用localhost访问时，得到的客户端IP地址是 ===>>> 127.0.0.1
            在IDEA中，使用127.0.0.1访问时，得到的客户端IP地址是 ===>>> 127.0.0.1
            在IDEA中，使用 真实IP 访问时，得到的客户端IP地址是 ===>>> 真实的客户端IP地址
         */
    System.out.println("客户端IP地址 -> " + req.getRemoteHost());
    //        - `getHeader()`：获取请求头；
    System.out.println("请求头User-Agent -> " + req.getHeader("User-Agent"));
    //        - `getParameter()`：获取请求的参数；

    //        - `getParameterValues()`：获取请求的参数（多个值的时候使用）；
    //        - `getMethod()`：获取请求的方式GET或POST；
    System.out.println("请求的方式 -> " + req.getMethod());
}
```



## 5.3 如何获取请求参数

**代码示例**：

```html
<form action="http://localhost:8080/RequestAIPServlet/param" method="get">
        用户名：<input type="text" name="username">
        密码：<input type="password" name="password">
        兴趣爱好：<input type="checkbox" name="hobby" value="cpp">C++
        <input type="checkbox" name="hobby" value="java">Java
        <input type="checkbox" name="hobby" value="js">JavaScript
        <input type="submit">
    </form>
```

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String username = req.getParameter("username");
    String password = req.getParameter("password");
    String[] hobbies = req.getParameterValues("hobby");
    System.out.println("账号：" + username + ", 密码：" + password);
    System.out.println("兴趣爱好：" + Arrays.toString(hobbies));
}
```



## 5.4 POST请求中文乱码问题

`setCharacterEncoding("UTF-8");`设置请求体的字符集为UTF-8，从而解决POST请求的中文乱码问题。



## 5.5 请求的转发

**请求转发**：服务器收到请求后，从一个资源跳转到另一个服务器资源的操作叫请求转发。

![image-20210622171017777](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622171017777.png)

**请求转发的特点**：

- 浏览器地址栏没有变化；

- 它们是一次请求；

- 他们共享Request域中的数据；

- 可以转发到WEB-INF目录下；

  ```java
  request.getRequestDispatcher("/WEB-INF/资源名")
  ```

- 不可以访问工程以外的资源；

**代码示例**：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/RequestAIPServlet/servlet1" method="get">
        用户名：<input type="text" name="username">
    </form>
</body>
</html>
```

```java
public class Servlet1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 获取请求的参数（办事的材料）查看
        String username = request.getParameter("username");
        System.out.println("在Servlet1（柜台1）中查看参数（材料）：" + username);

        // 给材料 盖一个章，并传递到Servlet2（柜台2）去查看
        request.setAttribute("key", "柜台1的章");

        // 问路：Servlet2（柜台2）怎么走
        /*
            请求转发必须要以斜杠打头，斜杠表示地址为：http://ip:port/工程名/，映射到IDEA代码的web目录

         */
        RequestDispatcher servlet2Path = request.getRequestDispatcher("/servlet2");

        // 走向Servlet2（柜台2）
        servlet2Path.forward(request, response);
    }

}
```

```java
public class Servlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 获取请求的参数（办事的材料）查看
        String username = request.getParameter("username");
        System.out.println("在Servlet2（柜台2）中查看参数（材料）：" + username);

        // 查看（柜台1）是否有盖章
        Object key = request.getAttribute("key");
        System.out.println("柜台1 是否有章：" + key);

        // 处理自己的业务
        System.out.println("Servlet2 处理自己的业务");
    }
}
```



## 5.6 base标签的作用

普通的标签和请求转发进行跳转的区别：

![image-20210622171034427](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622171034427.png)

![image-20210622171043754](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622171043754.png)

**base标签可以设置当前页面中所有相对路径工作时，参照哪个路径来进行跳转。**

**代码示例**：

```html
<html lang="zh_CN">
    <head>
        <meta charset="UTF-8">
        <title>Titile</title>
        <!--
        	base标签设置页面相对路径工作时参照的地址
			href就是参照的地址值
        -->
        <base href="http://localhost:8080/工程名/资源名"
    </head>
    <body>
		这是a下的b下的c.html页面
        <a href="../../index.html">跳回首页</a>
    </body>
```



## 5.7 Web中的相对路径和绝对路径

在Java Web中，路径分为相对路径和绝对路径两种：

**相对路径**：

- .					表示当前目录
- ..                   表示上一级目录
- 资源名          表示当前目录/资源名

**绝对路径**：

- http://ip:port/工程路径/资源路径



## 5.8 Web中/斜杠的不同意义

在Web中， /斜杠 是一种绝对路径。

- /斜杠 如果被游览器解析，得到的地址是：http://ip:port/

  ```html
  <a href="/">斜杠</a>
  ```

- /斜杠 如果被服务器解析，得到的地址是：http://ip:port/工程路径

  ```xml
  <url-pattern>/servlet1</url-pattern>
  ```

  ```java
  servletContext.getRealPath("/");
  request.getRequestDispatcher("/");
  ```

**特殊情况**：

```java
response.sendRediect("/"); // 把斜杠发送给游览器解析，得到： http://ip:port/
```



# 六 HttpServletResponse类

## 6.1 HttpServletResponse类的作用

HttpServletResponse类和HttpServletRequest类一样。每次请求进来，Tomcat服务器都会创建一个Response对象传递给Servlet程序去使用。HttpServletRequest表示请求过来的信息，HttpServletResponse表示所有响应的信息。

如果需要设置返回给客户端的信息，都可以通过HttpServletResponse对象来进行设置。



## 6.2 两个输出流的说明

- 字节流`getOutputStream();`：常用于下载（传递二进制数据）；
- 字符流`getWriter();`：常用于回传字符串（常用）；

**注意**：两个流同时只能使用一个。使用了字节流，就不能再使用字符流，反之亦然，否则就会报错。

![image-20210622171117000](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622171117000.png)

## 6.3 往客户端回传数据

**要求**：往客户端回传 字符串 数据。

**代码示例**：

```java
@WebServlet(name = "ResponseIOServlet", value = "/responseIOServlet")
public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 要求：往客户端回传 字符串 数据
        PrintWriter writer = resp.getWriter();
        writer.write("Response's content!");

    }
}
```



## 6.4 解决响应乱码

### 6.4.1 方式一（不推荐）

默认编码：`ISO-8859-1`

**代码示例**：

```java
@WebServlet(name = "ResponseIOServlet", value = "/responseIOServlet")
public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // ISO-8859-1默认
        System.out.println(resp.getCharacterEncoding());
        // 设置服务器字符集为UTF-8
        resp.setCharacterEncoding("UTF-8");
        // 通过响应头，设置浏览器也使用UTF-8字符集
        resp.setHeader("Content-Type", "text/html; charset-UTF-8");
        // 要求：往客户端回传 字符串 数据
        PrintWriter writer = resp.getWriter();
        writer.write("我爱你中国");
    }
}
```

### 6.4.2 方式二（推荐）

**代码示例**：

```java
@WebServlet(name = "ResponseIOServlet", value = "/responseIOServlet")
public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // setContentType() 会同时设置服务器和客户端都使用UTF-8字符集，还设置了响应头
        // 此方法一定要再获取流对象之前调用才有效
        resp.setContentType("text/html; charset-UTF-8");
        // 要求：往客户端回传 字符串 数据
        PrintWriter writer = resp.getWriter();
        writer.write("我爱你中国");
    }
}
```



## 6.5 请求重定向

**请求重定向**，是指客户端给服务器发送请求，然后服务器告诉客户端，访问新的地址。这个过程叫请求重定向。

**应用场景**：之前地址被废弃。

![image-20210622171140975](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622171140975.png)

### 6.5.1 方式一

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/RequestAIPServlet/Response1" method="get">
        <input type="submit">
    </form>
</body>
</html>
```

```java
@WebServlet(name = "Response1", value = "/Response1")
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("Response1 被访问了！");
        // 设置响应状态码，表示重定向，（已搬迁）
        response.setStatus(302);
        // 设置响应头，说明新的访问地址
        response.setHeader("Location", "http://localhost:8080/RequestAIPServlet/Response2");
    }
}
```

```java
@WebServlet(name = "Response2", value = "/Response2")
public class Response2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("Response2 被访问了！");
    }
}
```

**特点**：

- 浏览器地址栏会发生变化；
- 两次请求；
- 不共享Request域中的数据；
- 不能访问WEB-INF下的资源；
- 可以访问工程外的资源；



### 6.5.2 方式二（推荐使用）

```java
@WebServlet(name = "Response1", value = "/Response1")
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.sendRedirect("http://localhost:8080");
    }
}
```

