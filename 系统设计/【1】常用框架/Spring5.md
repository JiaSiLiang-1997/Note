# 一 Spring概念

## 1.1 简介

Spring框架是由于软件开发。Spring使用的是基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅仅限于服务器端的开发。从简单性、可测试性和松耦合性角度而言，绝大部分Java应用都可以从Spring中受益。

**Spring是一个轻量级控制反转（IOC）和面向切面（AOP）的容器框架。**

**官网地址：**https://spring.io/projects/spring-framework#overview

**下载地址**：https://repo.spring.io/release/org/springframework/spring/

**Github地址**：https://github.com/spring-projects/spring-framework

**导包**：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.6</version>
</dependency>
```

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.6</version>
</dependency>
```

## 1.2 目的

解决企业应用开发的复杂性，使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架。

 早年两套的框架：

- SSH：Struct2 + Spring + Hibernate
- SSM：Spring MVC + Spring + Mybatis

## 1.3 组成

Spring框架是一个分层结构，由7个模块组成。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210425210411479.png" alt="image-20210425210411479" style="zoom:80%;" />

Spring有两个核心部分：IOC 和 Aop

- IOC：控制反转，把**创建对象**的过程交给Spring进行管理；
- AOP：面向切面，不修改源代码的情况下，进行功能增强；

## 1.4 特点

1. **方便解耦，简化开发**

   通过Spring提供的IOC容器，可以将对象之间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合。有了Spring，用户不必再为单实例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。

2. **AOP编程的支持**

   通过Spring提供AOP功能，方便进行面向切面的编程，许多不容易用传统OOP实现的功能可以通过AOP轻松应付。

3. **声明式事务的支持**

   在Spring中，可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理，提高开发效率和质量。

4. **方便程序的测试**

   可以用非容器以来的编程方式进行几乎所有的测试工作，在Spring里，测试不再是昂贵的操作，而是随手可做的事情。例如：Spring对Junit4支持，可以通过注解方便的测试Spring程序。

5. **方便集成各种优秀框架**

   Spring不排斥各种优秀的开源框架，相反，Spring可以降低

6. **降低Java EE API的使用难度**

   Spring对很多难用的Java EE API（如：JDBC、JavaMail、远程调用等）提供了一个薄薄的封装层，通过Spring的简易封装，这些Java EE API的使用难度大为降低。

## 1.5 优点

- Spring是一个开源的免费的框架（容器）；
- Spring是一个**轻量级的**、**非入侵式**的框架；
- 控制反转（IOC）、面向切面（AOP）编程；（**重点**）
- 支持事务的处理，对框架整合的支持；

## 1.6 拓展

现代化的Java开发：就是基于Spring的开发。

![image-20210425210641576](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210425210641576.png)

- **Spring Boot**：
  - 一个快速开发的脚手架；
  - 基于Spring Boot可以快速的开发单个微服务；
  - 约定大于配置；
- **Spring Cloud**：
  - Spring Cloud是基于Spring Boot实现的；

现在大多数公司都在使用Spring Boot进行快速开发，学习Spring Boot的前提，需要完全掌握Spring及Spring MVC。

**Spring的弊端**：发展太久之后，违背了原来的理念。配置十分繁琐，人称："配置地狱"。

## 1.7 总结

Spring就是一个轻量级的控制反转（IOC）和面向切面（AOP）编程的框架。



# 二 IOC容器

## 2.1 IOC理论推导

**之前的业务逻辑**：

1. `UserDao`接口
2. `UserDaoImpl`实现类
3. `UserService`业务接口
4. `UserServiceImple`业务实现类

```java
/*
	UserDao接口
*/
public interface UserDao {
    void getUser();
}
```

```java
/*
	UserDaoImpl实现类
*/
public class UserDaoImpl implements UserDao {
    public void getUser() {
        System.out.println("默认获取用户得数据");
    }
}
```

```java
/*
	UserService业务接口
*/
public interface UserService {
    getUser();
}
```

```java
/*
	UserServiceImple业务实现类
*/
public class UserServiceImpl implements UserService {
    private UserDao userDao = new UserDaoImpl();
    
    public void getUser() {
        userDao.getUser();
    }
}
```

```java
/*
	测试类
*/
public class MyTest {
    public static void main(String[] args) {
        // 用户实际调用的是业务层，dao层不需要接触！
        UserService userService = new UserDaoImpl();
        userService.getUser();
        /*
        	输出：默认获取用户的数据
        */
        
    }
}
```

**增加需求**：

```java
public class UserDaoMysqlImpl implements UserDao {
    System.out.println("Mysql获取用户数据");
}
```

```java
/*
	测试类：新需求来了之后，需要修改测试类!!!
*/
public class MyTest {
    public static void main(String[] args) {
        // 修改！！！
        UserService userService = new UserDaoMysqlImpl();
        userService.getUser();
        /*
        	输出：Mysql获取用户的数据
        */
    }
}
```

在之前的业务中，用户的需求可能会影响原来的代码，需要根据用户的需求去修改原代码。如果程序代码量十分大，修改一次的成本代价十分昂贵。

**解决方案**：

使用了一个`Set`接口实现，发生了革命性的变换。

```java
/*
	UserServiceImple业务实现类
*/
public class UserServiceImpl implements UserService {
    private UserDao userDao;
    
    // 使用set进行动态实现值得注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    
    public void getUser() {
        userDao.getUser();
    }
}
```

```java
/*
	测试类：新需求来了之后，需要修改测试类!!!
*/
public class MyTest {
    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        // 仅需修改setUserDao()的参数即可完成需求的修改，无需改变代码！
        ((UserServiceImpl) userService.setUserDao(new UserDaoMysqlImpl()));
        userService.getUser();
        /*
        	输出：Mysql获取用户的数据
        */
    }
}
```

**变化**：之前，程序是主动创建对象，控制权在编程人员。而使用了`set`注入后，程序不再具有主动性，而是变成了被动得接收对象。

这种思想，从本质上解决了问题， 程序员不用再管理对象的创建了。系统的耦合性大大降低，可以更加专注业务的实现。这就是**IOC的原型**。



## 2.2 IOC本质

![image-20210608210052193](D:\JAVA\【5】框架\Spring5\Spring.assets\image-20210608210052193.png)

**控制反转loC(Inversion of Control),是一种设计思想，DI(依赖注入)是实现loC的一种方法**，也
有人认为DI只是loC的另一种说法。没有IoC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中,对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是:获得依赖对象的方式反转了。

![image-20210608210221387](D:\JAVA\【5】框架\Spring5\Spring.assets\image-20210608210221387.png)

**loC是Spring框架的核心内容**，使用多种方式完美的实现了IoC，可以使用XML配置，也可以使用注
解，新版本的Spring也可以零配置实现loC。

Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从IoC容器中取出需要的对象。

![image-20210608210455077](D:\JAVA\【5】框架\Spring5\Spring.assets\image-20210608210455077.png)

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

**控制反转是一种通过描述(XML或注解)并通过第三方去生产或获取特定对象的方式。在Spring中**
**实现控制反转的是IoC容器，其实现方法是依赖注入(Dependency Injection,DI)。**



**思考问题？**

- Hello对象是谁创建的？

  Hello对象是由Spring创建的。

- Hello对象的属性是怎么设置的？

  Hello对象的属性是由Spring容器设置的。

这个过程就叫控制反转：
**控制**：谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，对象是由Spring来创建的。
**反转**：程序本身不创建对象，而变成被动的接收对象。
**依赖注入**：就是利用set方法来进行注入的。
IOC是一种编程思想，由主动的编程变成被动的接收。
可以通过`newClassPathXmlApplicationContext`去浏览一下底层源码。



**总结**：不用在程序中改动，要实现不同的操作，只需要在XML配置文件中进行修改，所谓的IOC，用一句话概括：**对象由Spring来创建，管理，装配**。



# 三 IOC代码示例

**代码示例一**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="mysqlImpl" class="com.kuang.dao.UserDaoMysqlImpl" />
    <bean id="oracleImpl" class="com.kuang.dao.UserDaoOracleImpl"/>
    <bean id="service" class="com.kuang.service.UserServiceImpl">
    <!--
        ref：引用Spring容器中创建好的对象
        value：基本的值，基本数据类型
    -->
        <property name="userDao" ref="oracleImpl" />
    </bean>
</beans>
```

```java
/*
	测试类
*/
public class MyTest0 {
    public static void main(String[] args) {
        // 获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        // 我们的对象现在都在Spring中管理了，要使用，直接去里面取出来就可以。
        UserServiceImpl service = (UserServiceImpl) context.getBean("service");
        service.getUser();
    }
}
```



**代码示例二**：

```java
/*
	Hello类
*/
public class Hello {
    private String name;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void show() {
        System.out.println("Hello, " + name);
    }
    @Override
    public String toString() {
        return "Hello{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--
        使用Spring来创建对象，在Spring这些都称为Bean
        类型 变量名 = new 类型();
        id = 变量名
        Bean = 对象   new Hello();
        class = new 的对象
        property 相当于给对象中的属性设置一个值
    -->
    <bean id="hello" class="com.kuang.pojo.Hello">
        <property name="name" value="spring" />
    </bean>
</beans>
```

```java
/*
	测试类
*/
public class MyTest {
    public static void main(String[] args) {
        // 获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        // 我们的对象现在都在Spring中管理了，要使用，直接去里面取出来就可以。
        Hello hello = (Hello)context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```



# 四 IOC创建对象的方式

- 使用无参构造创建对象（默认）。
- 假设我们要使用有参构造创建对象
  - 下标赋值；
  - 参数类型匹配；（不建议使用！）
  - 直接通过参数名来设置；

**总结**:

- 在配置文件加载的时候，容器中管理的对象就已经初始化了。

## 4.1 无参构造

**代码示例**：

```java
public class User {
    private String name;
    public User() {
        System.out.println("User的无参构造被调用了！");
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void show() {
        System.out.println("name = " + name);
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.kuang.pojo.User" >
        <property name="name" value="张三" />
    </bean>
</beans>
```

```java
public class MyTest {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User)context.getBean("user");
        user.show();
    }
}
```

## 4.2 有参构造

**代码示例**：

```xml
<!--  方式一：下标赋值  -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.kuang.pojo.User" >
        <constructor-arg index="0" value="张三" />
    </bean>
</beans>
```

```xml
<!--  方式二：参数类型匹配  -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.kuang.pojo.User" >
        <constructor-arg type="java.lang.String" value="李四" />
    </bean>
</beans>
```

```xml
<!--  方式三：直接通过参数名来设置  -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.kuang.pojo.User" >
        <constructor-arg name="name" value="王五"/>
    </bean>
</beans>
```



# 五 Spring配置

## 5.1 alias别名

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.kuang.pojo.User" >
        <constructor-arg name="name" value="王五"/>
    </bean>
    <!--  如果添加了别名，我们也可以使用别名获取到对象  -->
    <alias name="user" alias="user2" />
</beans>
```



## 5.2 Bean的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	<!--
		id：bean的唯一标识符，相当于【对象名】
		class：bean对象所对应的【全限定名】（包名 + 类名）
		name：别名，通过别名也可以获取到对象，并且可以取多个别名。例如：name="user2,user3 user4"
		
	-->
    <bean id="user" class="com.kuang.pojo.User" >
        <constructor-arg name="name" value="王五"/>
    </bean>
</beans>
```



## 5.3 import导入

一般用于团队开发使用，可以将多个配置文件，导入合并为一个。

假设，现在项目中有多个人开发，这三个人负责不同的类开发，不同的类需要注册在不同的bean中，可以利用import将所有人的beans.xml合并为一个总的XML文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	<import resource="beans1.xml">
	<import resource="beans2.xml">
	<import resource="beans3.xml">
</beans>
```

使用的时候，直接使用总的配置就可以了。



# 六 DI依赖注入

## 6.1 构造器注入

前面已有，略。



## 6.2 Set方式注入【重点】

**依赖注入**：Set注入；

- **依赖**：bean对象的创建依赖于容器；

- **注入**：bean对象中的所有属性，由容器来注入；



**环境搭建**：

-  复杂类型；

```java
public class Address {
    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

- 真实测试对象；

```java
public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbys;
    private Map<String, String> cards;
    private Set<String> games;
    private String wife;
    private Properties info;
}
```

- beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="address" class="com.kuang.pojo.Address">
        <property name="address" value="四川省广元市" />
    </bean>

    <bean id="student" class="com.kuang.pojo.Student" >
        <!--  第一种：普通值注入，直接使用value  -->
        <property name="name" value="张三" />

        <!--  第二种：Bean注入，ref  -->
        <property name="address" ref="address" />

        <!--  第三种：数组注入，-->
        <property name="books">
            <array>
                <value>Java编程思想</value>
                <value>算法导论</value>
                <value>高性能MySQL</value>
                <value>Java核心技术</value>
            </array>
        </property>

        <!--  第四种：List注入，-->
        <property name="hobbys">
            <list>
                <value>听歌</value>
                <value>写代码</value>
                <value>看电影</value>
            </list>
        </property>

        <!--  第五种：Map注入，-->
        <property name="cards">
            <map>
                <entry key="身份证" value="510823199703294295" />
                <entry key="学号" value="1202021517" />
            </map>
        </property>

        <!--  第六种：Set注入，-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>CS</value>
                <value>BOB</value>
            </set>
        </property>

        <!--  第七种：NULL注入，-->
        <property name="wife" >
            <null />
        </property>

        <!--  第八种：Properties注入，-->
        <property name="info">
            <props>
                <prop key="driver">20190525</prop>
                <prop key="url">张三</prop>
                <prop key="username">root</prop>
                <prop key="password">123456</prop>
            </props>
        </property>
    </bean>
</beans>
```

- 测试类

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student stu = (Student) context.getBean("student");
        System.out.println(stu.toString());
    }
}
```



## 6.3 拓展方式注入

**注意**：p命名和c命名空间不能直接使用，需要导入XML。

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

## 6.3.1 p命名空间

**对应Set注入**

```java
public class User {
    private String name;
    private int age;
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
    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--  p命名空间注入，可以直接注入属性的值：property  -->
    <bean id="user" class="com.kuang.pojo.User" p:name="张三" p:age="18" />
</beans>
```

```java
public class UserTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = context.getBean("user", User.class);
        user.toString();
    }
}
```



## 6.3.2 c命名空间

**对应构造器注入**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	<!--  c命名空间注入，通过构造器注入：construct  -->
    <bean id="user2" class="com.kuang.pojo.User" c:name="李四" c:age="19" />
</beans>
```



## 6.4 bean的作用域

|     作用域      |                             描述                             |
| :-------------: | :----------------------------------------------------------: |
|  **singleton**  | 在spring IoC容器仅存在一个`Bean`实例，`Bean`以单例方式存在，`Bean`作用域范围的默认值。 |
|  **prototype**  | 每次从容器中调用`Bean`时，都返回一个新的实例，即每次调用`getBean()`时，相当于执行`newXxxBean()`。 |
|   **request**   | 每次HTTP请求都会创建一个新的`Bean`，该作用域仅适用于web的Spring WebApplicationContext环境。 |
|   **session**   | 同一个HTTP `Session`共享一个`Bean`，不同`Session`使用不同的`Bean`。该作用域仅适用于web的Spring `WebApplicationContext`环境。 |
| **application** | 限定一个`Bean`的作用域为`ServletContext`的生命周期。该作用域仅适用于web的Spring `WebApplicationContext`环境。 |
|  **websocket**  | 若一个`bean`的作用域为`websocket`，则只作用于一次`websocket`通信，若连接被释放，则`bean`自然也会被销毁。 |

### 6.4.1 singleton（单例模式）【重点】

![image-20210610212032205](D:\JAVA\【5】框架\Spring5\Spring.assets\image-20210610212032205.png)

**特点**：单例模式分为**饿汉模式**和**懒汉模式**

|   模式   |                             描述                             |
| :------: | :----------------------------------------------------------: |
| 饿汉模式 | spring singleton的缺省是饿汉模式：启动容器时（即实例化容器时），为所有spring配置文件中定义的bean都生成一个实例 |
| 懒汉模式 |    在第一个请求时才生成一个实例，以后的请求都调用这个实例    |

spring singleton设置为懒汉模式：

```xml
<beansdefault-lazy-init="true">
```

**代码示例**：

```java
public class User {
    private String name;
    private int age;

    public User() {
    }
    public User(String name, int age) {
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
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--  p命名空间注入，可以直接注入属性的值：property  -->
    <bean id="user" class="com.kuang.pojo.User" p:name="张三" p:age="18" scope="singleton" />
</beans>
```

```java
public class UserTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("studentbeans.xml");
        User user1 = context.getBean("user", User.class);
        User user2 = context.getBean("user", User.class);
        System.out.println(user1 == user2);
        System.out.println(user1);
        System.out.println(user2);
    }
}
```

**输出**：

```tex
true
com.kuang.pojo.User@5c30a9b0
com.kuang.pojo.User@5c30a9b0
```



### 6.4.2 prototype多例模式

**特点**：每次从容器中`get()`的时候，就会产生一个新的对象。

![image-20210610215040086](D:\JAVA\【5】框架\Spring5\Spring.assets\image-20210610215040086.png)

**代码示例**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--  p命名空间注入，可以直接注入属性的值：property  -->
    <bean id="user" class="com.kuang.pojo.User" p:name="张三" p:age="18" scope="prototype" />
</beans>
```

**输出**：

```tex
false
com.kuang.pojo.User@1ddf84b8
com.kuang.pojo.User@1139b2f3
```



# 七 Bean的自动装配

- 自动装配是Spring满足bean依赖的一种方式。
- Spring会在上下文中自动寻找，并自动给bean装配属性。



在Spring中有三种自动装配的方式：

-  在XML中显示的配置；
- 在Java中显示的配置；
- 隐式的自动装配Bean；【重点】



## 7.1 测试

**环境搭建**：一个人有两个宠物；



## 7.2 ByName自动装配

**代码示例**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="cat" class="com.kuang.pojo.Cat" />
    <bean id="dog" class="com.kuang.pojo.Dog" />
    <!--
        byName：会自动在容器上下文中查找，和自己对象set()方法后面的值对应的 bean-id       
    -->
    <bean id="people" class="com.kuang.pojo.People" autowire="byName">
        <property name="name" value="张三" />
    </bean>
</beans>
```



## 7.3 ByType自动装配

**代码示例**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="cat" class="com.kuang.pojo.Cat" />
    <bean id="dog111" class="com.kuang.pojo.Dog" />
    <!--
        byType：会自动在容器上下文中查找，和自己对象属性类型相同的 bean
		注意：必须保证类型全局唯一
		使用byType时，可以不对bean设置id
    -->
    <bean id="people" class="com.kuang.pojo.People" autowire="byType">
        <property name="name" value="张三" />
    </bean>
</beans>
```



**总结**：

- byName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的`set()`方法的值一致。
- byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致。

`

## 7.4 使用注解实现自动装配

JDK1.5支持的注解，Spring2.5就支持注解了。

要使用注解须知：

1. 导入约束；（`context`约束）

2. 配置注解的支持：`<context:annotation-config/>`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ```

### 7.4.1 @Autowired

**注意**：

- 直接在属性上使用，也可以在`set()`方法上使用。
- 使用了`@Autowired`后，可以不再需要`set()`方法。前提是自动装配的属性在IOC（Spring）容器中存在，且符合名字ByName。
- 如果**@Autowired**自动装配的环境比较复杂，自动装配无法通过一个注解**@Autowired**完成的时候，可以使用**@Qualifier(value="xxx")**去配置**@Autowired**的使用，指定一个唯一的bean对象注入。
- Java中提供的**@Resource(name = "xxx")**注解也可以完成自动装配，但必须保证byName或byType中的一个，name可以缺省。

**代码示例**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config />
    <bean id="cat111" class="com.kuang.pojo.Cat" />
    <bean id="dog" class="com.kuang.pojo.Dog" />
    <bean id="people" class="com.kuang.pojo.People" />
</beans>
```

```java
package com.kuang.pojo;

import org.springframework.beans.factory.annotation.Autowired;

public class People {
    // 如果显示定义了Autowired的required属性为fasle，说明这个对象可以为null，否则不允许为空。
    @Autowired(required = false)
    private Dog dog;
    @Autowired
    @Qualifier(value = "cat111")
    private Cat cat;
    private String name;

	.......
        
}
```

**补充**：

```java
@Nullable	字段标记了这个注解，说明这个字段可以为null;
```

```java
public @interface Autowired {
    boolean required() default true;
}
```

**小节**：

- **@Autowired**和**@Resource**都是用来自动装配的，都可以放在属性字段上。
- **@Autowired**通过byType的方式实现。【常用】
- **@Resource**默认通过byName的方式实现，如果找不到名字，则通过byType实现，如果两个都找不到的情况下，就报错。【常用】
- 执行顺序不同：**@Autowired**通过byType的方式实现，**@Resource**默认通过byName的方式实现。



# 八 使用注解开发

在Spring4之后，要使用注解开发，必须要保证aop的包导入了。

![image-20210624200929081](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210624200929081.png)

使用注解需要导入context约束，增加注解的支持。



## 8.1 bean

**@Component**：组件，放在类上，说明这个类被Spring管理了，就是bean。



## 8.2 属性如何注入

**代码示例**：

```java
@Component
public class User {
    /*
        @Value组件 等价于 <property name="name" value="kuangsheng" />
    */

    @Value("kuangsheng")
    public String name;
}
```

**注意**：





## 8.3 衍生的注解

**@Componet**有几个衍生注解，在web开发中，会按照MVC三层架构分层。

- dao【**@Repository**】

- service【**@Service**】
- controller【**@Controller**】

这四个注解功能都是一样的，都是代表将某个类注册到Spring中，装配Bean。



## 8.4 自动装配配置

- **@Autowired**：自动装配通过类型，名字。如果**@Autowired**不能唯一自动装配上属性，则需要通过**@Qualifier(value="xxx")**；
- **@Nullable**：字段标记了这个注解，说明这个字段可以为null；
- **@Resource**：自动装配通过名字、类型；
- **@Componet**：放在类上，说明这个类被Spring管理了，就是bean；



## 8.5 作用域

**代码示例**：

```java
@Component
@Scope("singleton")
public class User {
    @Value("kuangsheng")
    public String name;
}

```





## 8.6 小节

XML与注解：

- XML更加万能，适用于任何场合，维护简单方便；
- **注解**不是自己的类使用不了，维护相对复杂；

XML与注解的最佳实践：

- XML用来管理Bean；

- 注解只负责完成属性的注入；

- 在使用的过程中，只需要注意一个问题，必须要让注解生效，就需要开启注解的支持；

  ```xml
  <!--  指定要扫描的包，这个包下的注解就会生效  -->
  <context:component-scan base-package="com.kuang" />
  <context:annotation-config/>
  ```



# 九 使用Java的方式配置Spring

完全不使用Spring的XML配置，全权交给Java来做。

JavaConfig是Spring的一个子项目，在Spring4之后，成为了核心功能。

![image-20210625173407810](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210625173407810.png)

**代码示例**：

```java
/*
	实体类：
*/
@Component // 这里这个注解的意思，就是说明这个类被Spring接管了，注册到了容器中。
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("张三") // 属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```java
/*
	配置类：
*/
/*
	这也也会被Spring容器托管，注册到容器中，因为它本来就是一个@Componet
	@Configuration就是一个配置类，就会之前看到的beans.xml一样
*/
@Configuration
@ComponentScan("com.kuang.pojo")
@Import(MyConfig2.class) // 引入其他配置类，等价于XML合并
public class MyConfig {
    /*
    	注册一个bean，就相当于我们之前写的一个bean标签
    	这个方法的名字，就相当于bean标签中的id属性
    	这个方法的返回值，就相当于bean标签中的class属性
    */
    @Bean
    public User getUser() {
        return new User(); // 就是返回要注入到bean的对象
    }
}
```

```java
/*
	测试类：
*/
public class Test {
    public static void main(String[] args) {
        // 如果完全使用了配置类方式，就只能通过AnnotationConfig 上下文获取容器，通过配置类的class对象加载。
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User user = context.getBean("getUser", User.class);
        System.out.println(user.getName());
    }
}
```

**注意**：这种纯Java的配置方式，在SpringBoot中随处可见。



# 十 代理模式

为什么要学习代理模式？因为这就是SpringAOP的底层。【SpringAOP 和 SpringMVC】

代理模式的分类：

- 静态代理：
- 动态代理：

**什么代理模式？**

![image-20210625192447537](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210625192447537.png)







## 10.1 静态代理

**优点**：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务；
- 公共业务就交给了代理角色，实现了业务的分工；
- 公共业务发生扩展的时候，方便集中管理；



**缺点**：

- 一个真实角色就会产生一个代理角色，代码量增加，开发效率会变低；



**角色分析**：

- 抽象角色：一般会使用接口或者抽象类来解决；
- 真实角色：被代理的角色；
- 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作。
- 客户：访问代理对象的人；



**代码示例**：

```java
/**
 * 租房（抽象角色）
 */
public interface Rent {
    /**
     * 租房
     */
    public abstract void rent();
}
```

```java
/**
 * 房东（真实角色：被代理角色）
 */
public class Host implements Rent {
    @Override
    public void rent() {
        System.out.println("房东要出租房子！");
    }
}
```

```java
/**
 * 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作。
 */
public class Proxy implements Rent {
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    @Override
    public void rent() {
        System.out.println();
        host.rent();
        getFree();
    }

    /**
     * 看房
     */
    public void seeHouse() {
        System.out.println("中介带你看房！");
    }

    /**
     * 收取中介费用
     */
    public void getFree() {
        System.out.println("中介收取费用！");
    }

    /**
     * 合同
     */
    public void sign() {
        System.out.println("签订合同！");
    }
}
```

```java
/**
 * 客户：访问代理对象的人
 */
public class Client {
    public static void main(String[] args) {
        // 房东要租房子了
        Host host = new Host();
        // 中介 代理 房东，代理角色一般会有一些附属操作
        Proxy proxy = new Proxy(host);
        // 不用面对房东，直接找中介租房即可
        proxy.rent();
    }
}
```



## 10.2 加深理解

**需求**：在不修改原有代码的情况下，给用户扩展日志记录的功能，只需要给其加一个第三方代理即可实现。

**代码示例**：

```java
/**
 * 用户操作（抽象）
 */
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void query();
}
```

```java
/**
 * 真实对象
 */
public class UserServiceImpl implements UserService {
    @Override
    public void add() {
        System.out.printf("增加了一个用户");
    }

    @Override
    public void delete() {
        System.out.printf("删除了一个用户");
    }

    @Override
    public void update() {
        System.out.printf("修改了一个用户");
    }

    @Override
    public void query() {
        System.out.printf("查询了一个用户");
    }
}
```

```java
public class UserServiceProxy implements UserService{
    private UserService userService;

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public void add() {
        log("add()");
        userService.add();
    }

    @Override
    public void delete() {
        log("delete()");
        userService.delete();
    }

    @Override
    public void update() {
        log("update()");
        userService.update();
    }

    @Override
    public void query() {
        log("query()");
        userService.query();
    }

    // 日志方法
    public void log(String msg) {
        System.out.println("使用了 " + msg + " 方法");
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        UserServiceImpl userService = new UserServiceImpl();
        UserServiceProxy userServiceProxy = new UserServiceProxy();
        userServiceProxy.setUserService(userService);
        userServiceProxy.add();
    }
}
```



**初识AOP**

![image-20210705161704812](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210705161704812.png)









## 10.3 动态代理

**简介**：

- 动态代理和静态代理角色一样；

- 动态代理和静态类是动态生成的，不是我们预先写好的；

- 动态代理分为两大类：

  - 基于接口的动态代理；
    - 基于接口——JDK动态代理[]
  - 基于类的动态代理；
    - 基于类——cglib

  - Java字节码实现——javasist



**预备知识**：

- Proxy：代理，Proxy提供了创建动态代理类和实例的静态方法，它也是由这些方法创建的所有动态代理类的超类。

  ![image-20210705164650150](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210705164650150.png)

  ![image-20210705164742889](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210705164742889.png)

- InvocationHandler：调用处理程序（接口，位于反射包下），每个代理实例都有一个关联的调用处理程序。当在代理实例上调用方法时，方法调用将被编码并分派到其调用处理程序的`invoke()`方法。

  `Object invoke(Object proxy, 方法 method, Obejct[] args) throws Throwable`

  **说明**：

  ​		处理代理实例上的方法调用并返回结果。当在与之关联的代理实例上调用方法时，将在调用处理程序中调用此方法。

  **参数**：

  - proxy - 调用该方法的代理。
  - method - 所述方法对应于调用代理实例上的接口方法的实例。 方法对象的声明类将是该方法声明的接口，它可以是代理类继承该方法的代理接口的超级接口。
  - args - 包含的方法调用传递代理实例的参数值的对象的阵列，或nul1如果接方法没有参数。 原始类型的参数包含在适当的原始包装器类的实例中，例如`java.lang.Integer`或`java.lang.Boolean`。

  **结果**：

  ​		从代理实例上的方法调用返回的值。如果接口方法的声 明返回类型是原始类型，则此方法返回的值必须是对应的基本包装类的实例;否则，它必须是可声明返回类型的类型。如果 此方法返回的值是null和接口方法的返回类型是基本类型，那么**NullPointerException**将由 代理实例的方法调用抛出。如上所述， 如果此方法返回的值，否则不会与接口方法的声明的返回类型兼容，一个**ClassCastException**将代理实例的方法调用将抛出。

**代码实例**：

```java
/**
 * 用户操作（抽象）
 */
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void query();
}
```

```java
/**
 * 真实对象
 */
public class UserServiceImpl implements UserService {
    @Override
    public void add() {
        System.out.printf("增加了一个用户");
    }

    @Override
    public void delete() {
        System.out.printf("删除了一个用户");
    }

    @Override
    public void update() {
        System.out.printf("修改了一个用户");
    }

    @Override
    public void query() {
        System.out.printf("查询了一个用户");
    }
}
```

```java
/**
 * 代理调用处理程序
 */
public class ProxyInvocationHandler implements InvocationHandler {

    /**
     * 第一步：被代理的接口
     */
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

    /**
     * 第二步：生成得到代理类
     */
    public Object getProxy() {
        return Proxy.newProxyInstance( this.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                this
        );
    }

    /**
     * 第三步：处理代理实例，并返回结果
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        // 动态代理的本质，就是使用反射机制实现
        Object result = method.invoke(target, args);
        return result;
    }

    /**
     * 第四步：附加功能（拓展）
     */
    public void log(String msg) {
        System.out.println("执行了" + msg + "方法！");
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        // 真实角色
        UserServiceImpl userService = new UserServiceImpl();
        // 代理角色，不存在
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        // 设置要代理的对象
        pih.setTarget(userService);
        // 动态生成代理类
        UserService proxy = (UserService) pih.getProxy();
        proxy.add();
    }
}
```

**优点**：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务；
- 公共业务就交给了代理角色，实现了业务的分工；
- 公共业务发生扩展的时候，方便集中管理；
- **一个动态代理类代理的是一个接口，一般就是对应的一类业务；**
- **一个动态代理类可以代理多个类，只要是实现了同一个接口即可；**



# 十一 AOP

## 11.1 AOP定义

AOP (Aspect Oriented Programming)意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性,同时提高了开发的效率。

![image-20210708215812598](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210708215812598.png)



## 11.2 AOP在Spring中的作用

**提供声明式事务；允许用户自定义切面**

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志、安全、缓存、事务等等......
- 切面(ASPECT)：横切关注点被模块化的特殊对象。即，它是一个类。
- 通知(Advice)：切面必须要完成的工作。即，它是类中的一个方法。
- 目标(Target)：被通知对象。
- 代理(Proxy)：向目标对象应用通知之后创建的对象。
- 切入点(PointCut)：切面通知执行的"地点"的定义。
- 连接点(UointPoint)：与切入点匹配的执行点。

![image-20210708220347248](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210708220347248.png)

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Adivce：

|   通知类型   |        连接点        |                     实现接口                     |
| :----------: | :------------------: | :----------------------------------------------: |
|   前置通知   |      方法方法前      |    org.springframework.aop.MethodBeforeAdvice    |
|   后置通知   |        方法后        |   org.springframework.aop.AfterReturningAdvice   |
|   环绕通知   |       方法前后       |   org.aopalliance.intercept.MethodInterceptor    |
| 异常抛出通知 |     方法抛出异常     |       org.springframework.aop.ThrowsAdvice       |
|   引介通知   | 类中增加新的方法属性 | org.springframework .aop.IntroductionInterceptor |

即AOP在不改变原有代码的情况下，去增加新的功能



## 11.3 使用Spring实现AOP

【重点】使用AOP织入，需要导入一个依赖包！

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

### 11.3.1 方式一：使用Spring的API接口

【主要是Spring API接口实现】

**代码示例**：

```java
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void query();
}
```

```java
public class UserServiceImpl implements UserService{
    @Override
    public void add() { System.out.println("增加了一个用户！"); }
    @Override
    public void delete() {
        System.out.println("删除了一个用户！");
    }

    @Override
    public void update() {
        System.out.println("更新了一个用户！");
    }

    @Override
    public void query() {
        System.out.println("查询了一个用户！");
    }
}
```

```java
public class Log implements MethodBeforeAdvice {
    /**
     *
     * @param method 要执行的目标对象的方法
     * @param args 参数
     * @param target 目标对象
     * @throws Throwable
     */
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "方法被执行了！");
    }
}
```

```java
public class AfterLog implements AfterReturningAdvice {
    /**
     *
     * @param returnValue 返回值
     * @param method 要实行的目标对象的方法
     * @param args 参数
     * @param target 目标对象
     * @throws Throwable
     */
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了" + method.getName() + "方法，返回结果为：" + returnValue);
    }
}
```

```xml
<!--  注册bean  -->
<bean id="userService" class="com.kuang.service.UserServiceImpl" />
<bean id="log" class="com.kuang.log.Log" />
<bean id="afterLog" class="com.kuang.log.AfterLog" />

<!--  方式一：使用原生Spring API接口  -->

<!--  配置AOP：需要导入AOP约束  -->
<aop:config>
    <!--
            切入点：
            expression：表达式，execution(要执行的位置，********)
        -->
    <aop:pointcut id="pointcut" expression="execution(* com.kuang.service.UserServiceImpl.*(..))"/>
    <!--执行环绕增加-->
    <aop:advisor advice-ref="log" pointcut-ref="pointcut" />
    <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut" />
</aop:config>
```

```java
public class MyTest {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        // 注：动态代理，代理的是接口
        UserService userService = (UserService) context.getBean("userService");

        userService.query();
    }
}
```

### 11.3.2 方式二：自定义类实现AOP

【主要是切面定义】

**代码示例**：

```java
public class DiyPointCut {
    public void before() {
        System.out.println("=====方法执行前=====");
    }

    public void after() {
        System.out.println("=====方法执行后=====");
    }
}
```

```xml
<!--  方式二：自定义类  -->
<bean id="diy" class="com.kuang.diy.DiyPointCut" />

<aop:config>
    <!--  自定义切面，ref要引用的类  -->
    <aop:aspect ref="diy">
        <!--  切入点  -->
        <aop:pointcut id="pointcut" expression="execution(* com.kuang.service.UserServiceImpl.*(..))"/>
        <!--  通知  -->
        <aop:before method="before" pointcut-ref="pointcut"/>
        <aop:after method="after" pointcut-ref="pointcut" />
    </aop:aspect>
</aop:config>
```

### 11.3.3 方式三：注解实现

**代码示例**：

```java
/**
 * 方式三:使用注解方式实现AOP
 */
@Aspect // 标注这个类是一个切面
public class AnnotationPointCut {
    @Before("execution(* com.kuang.service.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("=====方法执行前=====");
    }

    @After("execution(* com.kuang.service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("=====方法执行后=====");
    }

    // 在环绕增强中, 可以给定一个参数, 代表要获取处理切入的点
    @Around("execution(* com.kuang.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

//        Signature signature = jp.getSignature(); // 获得签名
//        System.out.println("signature: " + signature);

        // 执行方法
        Object proceed = jp.proceed();

        System.out.println("环绕后");

//        System.out.println(proceed);
    }
}
```

```xml
<!--  方式三：注解方式实现AOP  -->
<bean id="annotationPointCut" class="com.kuang.diy.AnnotationPointCut" />
<!--  开启注解支持  JDK(默认:proxy-target-class="false")	cglib(proxy-target-class="true")-->
<aop:aspectj-autoproxy />
```



# 十二 整合Mybatis

**步骤**：

1. 导入相关jar包
   - junit
   - mybatis
   - mysql数据库
   - spring相关的
   - aop织入
   - mybatis-spring【new】

2. 编写配置文件
3. 测试

**导包示例**： 

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.24</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.6</version>
    </dependency>

    <!--  Spring操作数据库的话, 还需要一个Spring-jdbc-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.3.6</version>
    </dependency>

    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.6</version>
    </dependency>
</dependencies>
```



## 12.1 回忆Mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试

**代码示例**：

​		**1.编写实体类**

```java
// 第一步：编写实体类
@Data
public class User {
    private int id;
    private String name;
    private String pwd;
}
```

​		**2.编写核心配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC
        "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--  configuration核心配置文件  -->
<configuration>
    <typeAliases>
        <package name="com.kuang.pojo"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper class="com.kuang.mapper.UserMapper" />
    </mappers>
</configuration>
```

​		**3.编写接口**

```java
public interface UserMapper {
    public List<User> selectUser();
}
```

​		**4.编写Mapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC
        "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--  configuration核心配置文件  -->
<mapper namespace="com.kuang.mapper.UserMapper">
    <select id="selectUser" resultType="user">
        select * from mybatis.user;
    </select>
</mapper>
```

​		**5.测试**

```java
public class MyTest {
    @Test
    public void test() throws IOException {
        String resources = "mybatis-config.xml";
        InputStream in = Resources.getResourceAsStream(resources);

        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> users = mapper.selectUser();

        for (User user : users) {
            System.out.println(user);
        }
    }
}
```



## 12.2 Mybatis-spring

MyBatis-Spring 会帮助你将 MyBatis 代码无缝地整合到 Spring 中。它将允许 MyBatis 参与到 Spring 的事务管理之中，创建映射器 mapper 和 `SqlSession` 并注入到 bean 中，以及将 Mybatis 的异常转换为 Spring 的 `DataAccessException`。 最终，可以做到应用代码不依赖于 MyBatis，Spring 或 MyBatis-Spring。

**代码示例**：

​		**1.编写数据源配置、SqlSessionFactory、SqlSessionTemplate**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--  DataSource：使用Spring的数据源替换Mybatis的配置(c3p0、dbcp、druid)  -->
    <!--  这里使用Spring提供的JDBC：org.springframework.jdbc.datasource  -->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
        <property name="username" value="root" />
        <property name="password" value="123456" />
    </bean>


    <!--  sqlSessionFactory  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource" />
        <!--  绑定Mybatis配置文件  -->
        <property name="configLocation" value="mybatis-config.xml" />
    </bean>

    <!--  SqlSessionTemplate：就是我们使用的SqlSession  -->
    <bean id="sqlSesion" class="org.mybatis.spring.SqlSessionTemplate">
    <!--  只能使用构造器注入sqlSessionFactory，没有set()方法  -->
        <constructor-arg index="0" ref="sqlSessionFactory" />
    </bean>
</beans>
```

​		**2.配置userMapper对象**

​		这一步可以给对应实体类进行实例化。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml" />

    <bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean" >
        <property name="mapperInterface" value="com.kuang.mapper.UserMapper" />
        <property name="sqlSessionTemplate" ref="sqlSesion" />
    </bean>
</beans>
```

​		**3.测试**

```java
public class MyTest {
    @Test
    public void spring_mybatis_test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
        List<User> users = userMapper.selectUser();

        for (User user : users) {
            System.out.println(user);
        }
    }
}
```



# 十三 声明式事务

## 13.1 事务

- 把一组业务当成一个业务来做，要么都成功，要么都失败；
- 事务在项目开发中，十分的重要，涉及到数据的一致性问题；
- 确保完整性和一致性；



事务的ACID原则：

- 原子性；
- 一致性；
- 隔离性：多个业务可能操作同一个资源，防止数据损坏；
- 持久性：事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写到存储器中；



## 13.2 Spring中的事务管理

- 声明式事务：AOP；
- 编程式事务：需要在代码中，进行事务的管理；

**代码示例**：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/tx
       https://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       https://www.springframework.org/schema/aop/spring-aop.xsd">


    <!--  DataSource：使用Spring的数据源替换Mybatis的配置(c3p0、dbcp、druid)  -->
    <!--  这里使用Spring提供的JDBC：org.springframework.jdbc.datasource  -->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
        <property name="username" value="root" />
        <property name="password" value="123456" />
    </bean>

    <!--  sqlSessionFactory  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource" />
        <!--  绑定Mybatis配置文件  -->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
    </bean>

    <!--  配置声明式事务  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg ref="datasource" />
    </bean>

    <!--  结合AOP实现事务的织入  -->
    <!--  配置事务通知  -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--  给哪些方法配置事务  -->
        <!--  配置事务的传播特性：propagation  -->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!--  配置事务切入  -->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.kuang.mapper.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut" />
    </aop:config>


    <bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean" >
        <property name="mapperInterface" value="com.kuang.mapper.UserMapper" />
        <property name="sqlSessionFactory" ref="sqlSessionFactory" />
    </bean>

</beans>
```

**思考**：

为什么需要事务？

- 如果不配置事务，可能存在数据提交不一致的情况；
- 如果不在Spring中配置声明式事务，就需要在代码中手动配置事务；
- 事务在项目的开发中十分重要，涉及到数据的一致性和完整性；













































