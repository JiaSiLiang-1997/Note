# 一 Java Web的概念

## 1.1 Java Web

Java Web是指，所有通过Java语言编写可以通过游览器访问的程序的总称，叫JavaWeb。

Java Web是基于请求与响应来开发的。

## 1.2 请求

请求是指客户端给服务器发送数据，叫请求**Request**。

## 1.3 响应

响应是指服务器给客户端回传数据，叫响应**Response**。

## 1.4 请求与响应的关系

请求和响应是成对出现的，有请求就有响应。

![image-20210622163041281](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622163041281.png)

# 二 Web资源的分类

web资源按实现的技术和呈现的效果不同，又分为静态资源和动态资源两种。

- 静态资源：HTML、CSS、JS、TXT、MP4、JPG等
- 动态资源：JSP页面、Servlet程序等



# 三 常用的Web服务器

Tomcat：由Apache组织握供的一种Web服务器，提供对Jsp和Sernlet的支持。它是一种轻量级的JavaWeb容器（服务器），也是当前应用最广的JavaWeb服务器(免费)。

Jboss：是一个遵从JawaEE规范的、开放源代码的、纯Java的EB服务器，它支持所有的JawaEE规范（免费）。

Glassish：由Oracle公司开发的一款JavaWeb服务器，是一款强健的商业服务器，达到产品级质量（应用很少）。

Resin：是CAUCHO公司的产品，是一个非常流行的服务器，对serlet和JISP提供了良好的支持，性能也比较优良，resin自身采用JAVA语言开发（收费，应用比较多）。

WebLogic：是Oradle公司的产品，是目前应用最广泛的Web服务器，支持JavaEE规范，而且不断的完善以适应新的开发要求，适合大型项目（收费，用的不多，适合大公司）。



# 四 Tomcat服务器和Servlet版本的对应关系

当前企业常用的版本：`7.*`或者`8.*`

| Tomcat版本 | Servlet/JSP版本 | JavaEE版本 | 运行环境 |
| :--------: | :-------------: | :--------: | :------: |
|    4.1     |     2.3/1.2     |    1.3     |  JDK1.3  |
|    5.0     |     2.4/2.0     |    1.4     |  JDK1.4  |
|  5.5/6.0   |     2.5/2.1     |    5.0     |  JDK5.0  |
|    7.0     |     3.0/2.2     |    6.0     |  JDK6.0  |
|    8.0     |     3.1/2.3     |    7.0     |  JDK7.0  |

Servlet程序从2.5版本是现在市面上使用最多的版本（XML配置）

到了Servlet3.0之后，就是注解版本的Servlet使用。



# 五 Tomcat的使用

## 5.1 安装

找到需要用的Tomcat版本对应的zip压缩包，解压到需要安装的目录即可。

## 5.2 目录介绍

- bin：专门用于存放Tomcat服务器的可执行程序；
- conf：专门用于存放Tomcat服务器的配置文件；
- lib：专门用于存放Tomcat服务器的jar包；
- logs：专门用于存放Tomcat服务器运行时输出的日记信息；
- temp：专门用于存放Tomcat运行时产生的临时数据；
- webapps：专门用于存放部署的web工程；
- work：是Tomcat工作时的目录，用来存放Tomcat运行时JSP翻译为Servlet的源码，和Session钝化的目录。

## 5.3 启动Tomcat服务器

### 5.3.1 方式一

找到Tomcat目录下的bin目录下的startup.bat文件，双击，就可以启动Tomcat服务器。

如何测试Tomcat服务器启动成功？

打开游览器，在游览器地址栏中输入以下地址测试：

- http://localhost:8080
- http://127.0.01:8080
- http://真实IP:8080

当出现如下界面，说明Tomcat启动成功：

![image-20210622170421251](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622170421251.png)

常见的启动失败的情况有，双击startup.bat文件，就会出现一个小黑窗口一闪而过。

这个时候，失败的原因基本上都是因为没有配置好JAVA_HOME环境变量。



### 5.3.2 方式二

1. 打开命令行
2. cd 到 Tomcat 的 bin 目录下
3. 输入命令：catalina run



## 5.4 停止Tomcat服务器

1. 点击Tomcat服务器窗口的 X 关闭按钮；
2. 把 Tomcat 服务器窗口置为当前窗口，然后按快捷键 Ctrl + C；
3. 找到Tomcat的 bin 目录下的 shutdown.bat 双击，就可以停止 Tomcat服务器；（推荐）



## 5.5 修改Tomcat的端口号

- Mysql默认的端口号是：3306

- Tomcat默认的端口号是：8080

找到Tomcat目录下的conf目录，找到server.xml配置文件。

![image-20210524194458555](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210524194458555.png)

然后修改port属性为需要的端口号（1~65535）。

**注意**：

- 1~1000一般为系统占用；
- 修改完端口号，一定要重启Tomcat服务器才能生效；

- HTTP协议默认的端口号是：80；



## 5.6 部署Web工程到Tomcat中

### 5.6.1 方式一

只需要把Web工程的目录拷贝到Tomcat的webapps目录下即可。

http://localhost:8080 访问到的就是webapps目录。



### 5.6.2 方式二

找到Tomcat下的conf\Catalina\localhost\ ，创建一个配置文件(xml)。

XML部署示例：

```xml
<!--
	Context	表示一个工程上下文
	path	表示工程的访问路径/
	docBase	表示你的工程目录在哪里
-->
<Context path"/web03" docBase="E:\XXX\XXX\XXX">
```



## 5.7 访问方式区别对比

**手拖HTML页面到游览器**和**在游览器中输入 http://ip:端口号/工程名/**访问的区别

- 当手拖HTML页面到游览器中访问时，使用的协议是**file协议**，告诉游览器直接读取file:协议后面的路径，解析展示在游览器上即可。

- 当通过 http://ip:端口号/工程名/资源名 访问时，使用的协议是**HTTP协议**

![image-20210524201410948](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210524201410948.png)



## 5.8 ROOT工程的访问，默认index.html页面的访问

当在游览器地址栏中输入访问地址如下：

http://ip:port/ 	===>	没有工程名的时候，默认访问的是ROOT工程。

当在游览器地址栏中输入访问地址如下：

http://ip:port/工程名/	===>	没有资源名的时候，默认访问的是 index.html 页面。



# 六 IDEA整合Tomcat服务器

File | Settings | Build, Excution, Deployment | Application Servers



# 七 IDEA中动态Web工程的操作

## 7.1 IDEA中创建动态Web工程

1. 创建一个新模块：

![image-20210524202825172](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210524202825172.png)

2. 选择Java Enterprise，填好配置信息，如Application server、Project template：

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210524203055524.png" alt="image-20210524203055524" style="zoom:67%;" />

3. 直到Finish按钮，创建好的工程如下：

![image-20210524203840867](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210524203840867.png)



## 7.2 目录介绍

![image-20210524204741095](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210524204741095.png)



## 7.3 给动态web工程添加额外jar包

略



## 7.4 在IDEA中部署并运行

