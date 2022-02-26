## 一 什么是Filter过滤器

- Filter过滤器是Java Web的三大组件之一，三大组件分别是：Servlet程序、Listener监听器、Filter过滤器。
- Filter过滤器是Java EE的规范，也就是接口。
- Filter过滤器的作用是：**拦截请求**、**过滤响应**。



拦截请求常见的应用场景：

- 权限检查；
- 日记操作；
- 事务管理；



# 二 Filter的初体验

**需求**：web工程下，有一个admin目录，这个admin目录下的所有资源（HTML页面、jpg图片、jsp文件等等）都必须是用户登录之后才允许访问。

![image-20210623170823602](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170823602.png)

**Filter拦截器的使用步骤**：

1. 编写一个类去实现`Filter`接口；
2. 实现过滤方法`doFilter()`；
3. 到web.xml中去配置Filter的拦截路径。



## 三 Filter的生命周期

Filter的生命周期包含几个方法：

- 构造器方法；（web工程启动时）
- `init()`初始化方法；（web工程启动时）
- `doFilter()`过滤方法；（拦截到请求，就会执行）
- `destroy()`销毁方法；（web工程停止时）



# 四 FilterConfig类

FilterConfig类是Filter过滤器的配置文件类。Tomcat每次创建Filter的时候，也会同时创建一个FileConfig类，这里包含了Filter配置文件的配置信息。

FilterConfig类的作用是获取Filter过滤器的配置内容。

- 获取Filter的名称`filter-name`的内容；
- 获取Filter中配置的`init-param`初始化参数；
- 获取`ServletContext`对象；



# 五 FilterChain过滤器链

FilterChain过滤器链：就是多个过滤器如何在一起工作。

![image-20210623170839818](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170839818.png)

**注意**：在多个Filter过滤器执行的时候，它们执行的优先顺序是由他们在web.xml中从上到下配置的顺序决定。

**特点**：

- 所有Filter和目标资源默认都执行在同一个线程中。
- 多个Filter共同执行的时候，它们都使用同一个Request对象。



# 六 Filter的拦截路径

## 6.1 精确匹配

```xml
<url-pattern>/target.jsp</url-pattern>
```

以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/target.jsp

## 6.2 目录匹配

```xml
<url-pattern>/admin/*</url-pattern>
```

以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/admin/*

### 6.3 后缀名匹配

```xml
<url-pattern>*.html</url-pattern>
```

以上配置的路径，表示请求地址必须以**.html**结尾。

### 