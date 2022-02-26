# 一 Cookie

## 1.1 定义

- Cookie翻译过来就是饼干的意思；
- Cookie是服务器通知客户端保存键值对的一种技术；
- 客户端有了Cookie后，每次请求都发送给服务器；
- 每个Cookie的大小不能超过4kb；

## 1.2 Cookie的创建

![image-20210623170653057](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170653057.png)

## 1.3 Cookie的获取

![image-20210623170707625](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170707625.png)

## 1.4 Cookie的修改

### 1.4.1 方案一

1. 先创建一个要修改的同名的Cookie对象；
2. 在构造器，同时赋予新的Cookie值；
3. 调用`response.addCookie(Cookie)`；

### 1.4.2 方案二

1. 先查找到需要修改的Cookie对象；
2. 调用`setValue()`方法赋于新的Cookie值；
3. 调用`response.addCookie()`通知客户端保存修改；



## 1.5 Cookie的生命控制

Cookie的生命控制指的是如何管理Cookie什么时候被销毁（删除）。

![image-20210623170724095](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170724095.png)

## 1.6 Cookie有效路径Path的设置

Cookie的Path属性可以有效的过滤那些Cookie可以发送给服务器，那些不发。

path属性是通过请求的地址来进行有效的过滤。

**示例**：

- CookieA	path=/工程路径

- CookieB    path=/工程路径/abc

  当请求地址如下：

  ​		http://ip:port/工程路径/a.html

  ​		CookieA 发送

  ​		CookieB 不发送

  当请求地址如下：

  ​		http://ip:port/工程路径/abc/a.html

  ​		CookieA 发送

  ​		CookieB 发送



# 二 Session

## 2.1 定义

- Session就是一个接口（HttpSession）。
- Session就是会话，用来维护一个客户端和服务器之间关联的一种技术。
- 每个客户端都有自己的一个Seesion会话。
- Seesion会话中，经常用来保存用户登录之后的信息。



## 2.2 Session的创建和获取

创建和获取Seesion，它们的API都是`request.getSession()`：

- 第一次调用：创建Session会话；
- 之后调用：获取前面创建好的Session会话对象；



`isNew()`：判断Session是否是刚创建的。

- true：表示刚创建；
- false：表示获取之前创建；

**注意**：每个会话都有一个身份证号，也就是ID值，而且唯一。`getId()`得到Session的会话ID值。



## 2.3 Session域数据的存取

略



## 2.4 Session生命周期控制

![image-20210623170739038](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170739038.png)



## 2.4 游览器和Session之间关联的技术内幕

![image-20210623170753162](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170753162.png)

