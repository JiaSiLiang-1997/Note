课程学习环境：

- JDK 1.8；
- Mysql 5.7；
- maven 3.6.1；
- IDEA；



回顾：

- JDBC；
- Mysql；
- Java基础；
- Maven；
- Junit；



框架：配置文件的；

最好的学习方式：看官网文档；



# 一 简介

## 1.1 什么是Mybatis

MyBatis 本是apache的一个[开源项目](https://baike.baidu.com/item/开源项目/3406069)iBatis, 2010年这个[项目](https://baike.baidu.com/item/项目/477803)由apache software foundation 迁移到了[google code](https://baike.baidu.com/item/google code/2346604)，并且改名为MyBatis 。**2013年11月迁移到[Github](https://baike.baidu.com/item/Github/10145341)。**

![image-20210711220148125](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210711220148125.png)

- MyBatis 是一款优秀的**持久层框架**；

- 它支持自定义 SQL、存储过程以及高级映射；

- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。

- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



**如何获得Mybatis？**

- maven仓库；

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```

- github：[[Releases · mybatis/mybatis-3 (github.com)](https://github.com/mybatis/mybatis-3/releases)](https://github.com/mybatis/mybatis-3)
- 中文文档：[mybatis – MyBatis 3 | 简介](https://mybatis.org/mybatis-3/zh/index.html)



## 1.2 持久化

**什么是持久层？**

数据持久化：

- 持久化就是将程序的数据在持久状态和瞬时状态转换的过程；
- 内存：**断电即失**；
- 数据区（JDBC）、IO文件持久化；
- 生活：冷藏、罐头；



**为什么需要持久化？**

- 有一些对象，不能让他丢掉。

- 内存太贵了；



## 1.3 持久层

Dao层、Service层、Controller层

- 完成持久化工作的代码块；
- 层界限十分明显；



## 1.4 为什么需要Mybatis?

- 帮助程序员将数据存入到数据库中；

- 方便；
- 传统的JDBC代码太复杂。【简化】【框架】【自动化】
- 不用Mybatis也可以，更容易上手，**技术没有高低之分**；

**优点：**

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
- 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
- 提供映射标签，支持对象与数据库的orm字段关系映射
- 提供对象关系映射标签，支持对象关系组建维护
- 提供xml标签，支持编写动态sql。

最重要的一点：**使用的人多！**



# 二 第一个Mybatis程序

思路：搭建环境  ==>  导入Mybatis  ==>  编写代码  ==>  测试

## 2.1 搭建环境

**第一步：搭建数据库**

```sql
CREATE DATABASE `mybatis`;

USE `mybatis`;

CREATE TABLE `user`(
	`id` INT(20) NOT NULL PRIMARY KEY,
    `name` CHAR(30) NOT DEFAULT NULL,
    `pwd` CHAR(30) NOT DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user`(`id`, `name`, `pwd`) VALUES
(1, '张三', '123456'),
(2, '李四', '123456'),
(3, '王五', '123456')
```

**第二步：新建项目**

1. 新建一个普通的maven项目；
2. 删除src目录

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--  父工程  -->
    <groupId>com.kuang</groupId>
    <artifactId>Mybatis-study</artifactId>
    <version>1.0-SNAPSHOT</version>


    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <!--  导入依赖  -->
    <dependencies>
        <!--  mysql驱动  -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <!--  mybatis  -->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <!--  junit  -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

    </dependencies>

</project>
```

## 2.2 创建一个模块

- 编写mybatis核心配置文件；

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--  configuration：核心配置文件  -->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--  每一个Mapper.xml 都需要在Mybatis核心配置文件中注册  -->
    <mappers>
        <mapper class="com.kuang.dao.UserDao" />
    </mappers>
</configuration>
```

- 编写mybatis工具类；

```java
// sqlSessionFactory  -->  sqlSession
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            // 第一步：使用Mybatis获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /*
        既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
        SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
     */
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

## 2.3 编写代码

- 实体类

```java
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(String name, String pwd) {
        this.name = name;
        this.pwd = pwd;
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

- Dao接口

```java
public interface UserDao {
    List<User> geUserList();
}
```

- 接口实现类（由原来的UserDaoImpl类转换成一个Mapper配置文件）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kuang.dao.UserDao">
    <select id="geUserList" resultType="com.kuang.pojo.User">
        select * from user;
    </select>
</mapper>
```

## 2.4 测试

**问题一：**

![image-20210712170103763](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210712170103763.png)

MapperRegistry是什么？

每一个Mapper.xml 都需要在Mybatis核心配置文件中注册

```xml
<mappers>
    <mapper resource="com/kuang/dao/UserDao.xml" />
</mappers>
```



**问题二：**

![image-20210712170533419](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210712170533419.png)

maven由于他的约定大于配置，我们之后可以能遇到我们写的配置文件，无法被导出或者生效的问题，解决方案:

```xml
<!--  在build中配置resources，来防止我们资源导出失败的问题  -->
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

 **测试**：

```java
public class MyTest {
    @Test
    public void test1() {
        // 第一步：获得SqlSession对象
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            // 方式一：getMapper
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            List<User> users = mapper.getUserList();

            // 方式二：不推荐使用
            // List<User> users = sqlSession.selectList("com.kuang.dao.UserDao.getUserList");

            for (User user : users) {
                System.out.println(user);
            }
        }
    }
}
```

**可能会遇到的问题**：

1. 配置文件没有注册；
2. 绑定接口错误；
3. 方法名不对；
4. 返回类型不对；
5. Maven导出资源问题；

## 2.5 Mybastis中重要的类

**SqlSessionFactoryBuilder**

这个类可以被实例化、使用和丢弃，**一旦创建了 SqlSessionFactory，就不再需要它了。** 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。 你可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但最好还是不要一直保留着它，以保证所有的 XML 解析资源可以被释放给更重要的事情。

**SqlSessionFactory**

**SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例。 使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，多次重建 SqlSessionFactory 被视为一种代码“坏习惯”。因此 SqlSessionFactory 的最佳作用域是应用作用域。 有很多方法可以做到，**最简单的就是使用单例模式或者静态单例模式。**

**SqlSession**

每个线程都应该有它自己的 SqlSession 实例。**SqlSession 的实例不是线程安全的，因此是不能被共享的，**所以它的最佳的作用域是请求或方法作用域。 绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。 如果你现在正在使用一种 Web 框架，考虑将 SqlSession 放在一个和 HTTP 请求相似的作用域中。 **换句话说，每次收到 HTTP 请求，就可以打开一个 SqlSession，**返回一个响应后，就关闭它。 这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。 下面的示例就是一个确保 SqlSession 关闭的标准模式：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  // 你的应用逻辑代码
}
```



# 三 CRUD

**namespace**中的包名要和 Dao/Mapper 接口的包名一致。

**步骤**：

1. 编写接口；
2. 编写对应的Mapper中的Sql语句；
3. 测试

**注意事项**：

- 使用参数进行增删改查操作时，对象中的属性，可以直接取出；
- 增删改操作，必须使用`commit()`方法进行事务提交；

## 3.1 SELECT

选择，查询语句；

- id：就是对应的namespace中的方法名；
- resultType：Sql语句执行的返回值；
- parameterType：

**代码示例**：

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>
    
    <select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
        SELECT * FROM mybatis.user WHERE id = #{id}
    </select>
</mapper>
```

## 3.2 INSERT

**代码示例**：

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <!--  对象中的属性，可以直接取出来  -->
    <insert id="addUser" parameterType="com.kuang.pojo.User">
        INSERT INTO mybatis.user (id, name, pwd) VALUES (#{id}, #{name}, #{pwd})
    </insert>
</mapper>
```

## 3.3 UPDATE

**代码示例**：

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <update id="updateUserById" parameterType="com.kuang.pojo.User">
        UPDATE mybatis.user SET name=#{name}, pwd=#{pwd} WHERE id=#{id}
    </update>
</mapper>
```

## 3.4 DELETE

**代码示例**：

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <delete id="deleteUserById" parameterType="int">
        DELETE FROM mybatis.user WHERE id=#{id}
    </delete>
</mapper>
```

## 3.5 测试

```java
public interface UserMapper {
    // 查询全部用户
    List<User> getUserList();

    // 根据ID查询用户
    User getUserById(int id);

    // 插入一个用户
    int addUser(User user);

    // 修改用户
    int updateUserById(User user);

    // 删除用户
    int deleteUserById(int id);
}
```

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>
    
    <select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
        SELECT * FROM mybatis.user WHERE id = #{id}
    </select>

    <!--  对象中的属性，可以直接取出来  -->
    <insert id="addUser" parameterType="com.kuang.pojo.User">
        INSERT INTO mybatis.user (id, name, pwd) VALUES (#{id}, #{name}, #{pwd})
    </insert>

    <update id="updateUserById" parameterType="com.kuang.pojo.User">
        UPDATE mybatis.user SET name=#{name}, pwd=#{pwd} WHERE id=#{id}
    </update>
    
    <delete id="deleteUserById" parameterType="int">
        DELETE FROM mybatis.user WHERE id=#{id}
    </delete>
</mapper>
```

```java
public class MyTest {
    @Test
    public void selectAll() {
        // 第一步：获得SqlSession对象
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            // 方式一：getMapper
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            List<User> users = mapper.getUserList();

            // 方式二：不推荐使用
            // List<User> users = sqlSession.selectList("com.kuang.dao.UserMapper.getUserList");

            for (User user : users) {
                System.out.println(user);
            }
        }
    }

    @Test
    public void selectById() {
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            // 方式一：getMapper
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            User user = mapper.getUserById(2);
            System.out.println(user);
        }
    }

    @Test
    public void insertUser() {
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            User user = new User(4, "贾六", "123456");
            int result = mapper.addUser(user);

            if (result > 0) {
                System.out.println("插入成功");
            }
            // 注意：增删改操作，需要提交事务
            sqlSession.commit();
        }
    }

    @Test
    public void updateUser() {
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            User user = new User(4, "刘四", "123456");
            int result = mapper.updateUserById(user);
            if (result > 0) {
                System.out.println("修改用户信息成功");
            }
            sqlSession.commit();
        }
    }

    @Test
    public void deleteUser() {
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);

            int result = mapper.deleteUserById(4);

            if (result > 0) {
                System.out.println("删除用户成功");
            }
            sqlSession.commit();
        }
    }

}
```

## 3.6 错误排查

- namespace绑定Mapper接口，`namespace="com.kuang.Mapper.UserMapper">`

- 标签不要匹配错；
- Mapper.xml注册，使用resource绑定mapper，需要使用路径(/)；
- Mybatis核心配置文件必须符合规范；
- NullPointerException，没有注册到资源；
- 输出的XML文件中存在中文乱码问题；
- Maven资源没有导出问题；



## 3.7 万能Map

假设，实体类或者数据库中的表、字段、或者参数过多，应当考虑使用Map。

Map传递参数，直接在sql中取出key即可；【parameterType="map"】

对象传递参数，直接在sql中取对象的属性即可；【parameterType="Object"】

只有一个基本类型参数的情况在，可以直接在sql中取到；

多个参数用Map，或者注解。

**代码示例**：

```java
public interface UserMapper {
    // 万能Map
    int addUser2(Map<String, Object> map);
    
    User getUserById2(Map<String, Object> map);
}
```

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kuang.Mapper.UserMapper">
    <!-- 传递Map的key  -->
    <insert id="addUser2" parameterType="map">
        INSERT INTO mybatis.user (id, name, pwd) VALUES (#{userId}, #{userName}, #{userPwd})
    </insert>
    
    <select id="getUserById2" parameterType="map" resultType="com.kuang.pojo.User">
        SELECT * FROM mybatis.user WHERE id = #{id} AND name = #{name}
    </select>
</mapper>
```

```java
@Test
public void addUser2() {
    try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("userId", 5);
        map.put("userName", "赵久");
        map.put("userPwd", "123456");

        int result = mapper.addUser2(map);

        if (result == 0) {
            System.out.println("插入用户成功");
        }
        sqlSession.commit();

    }
}

@Test
public void selectById2() {
    try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("id", 5);
        map.put("name", "赵久");
        map.put("pwd", "123456");
        User user = mapper.getUserById2(map);

        System.out.println(user);

        sqlSession.commit();
    }
}
```

## 3.8 模糊查询

1. Java代码执行的时候，传递通配符`%%`；

```java
public interface UserMapper {
    List<User> getUserLike(String value);
}
```

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <select id="getUserLike" resultType="com.kuang.pojo.User">
        SELECT * FROM mybatis.user WHERE NAME LIKE #{value}
    </select>
</mapper>
```

```java
@Test
public void selectLike() {
    try(SqlSession sqlSession = MybatisUtils.getSqlSession()) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        List<User> users = mapper.getUserLike("%李%");
        for (User user : users) {
            System.out.println(user);
        }
    }
}
```

2. 在sql拼接中使用通配符；【存在SQL注入的问题】

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <select id="getUserLike" resultType="com.kuang.pojo.User">
        SELECT * FROM mybatis.user WHERE NAME LIKE "%"#{value}"%"
    </select>
</mapper>
```



# 四 配置解析

## 4.1 核心配置文件

- mybatis-config.xml
- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。
- Mybatis默认的事务管理器是JDBC，连接池：POOLED

```tex
  configuration（配置）
      properties（属性）
      settings（设置）
      typeAliases（类型别名）
      typeHandlers（类型处理器）——不需要了解
      objectFactory（对象工厂）——不需要了解
      plugins（插件）——不需要了解
      environments（环境配置）
          environment（环境变量）
              transactionManager（事务管理器）
              dataSource（数据源）
      databaseIdProvider（数据库厂商标识）
      mappers（映射器）
```

## 4.2 环境配置（environments）

- MyBatis 可以配置成适应多种环境；

- **可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

```xml
<!--  configuration：核心配置文件  -->
<configuration>
    <!--  default属性设置默认的环境  -->
    <environments default="test"> 
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
        
        <environment id="test">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

**注意事项**：

- 默认使用的环境 ID（比如：default="development"）。
- 每个 environment 元素定义的环境 ID（比如：id="development"）。
- 事务管理器的配置（比如：type="JDBC"）。
- 数据源的配置（比如：type="POOLED"）。

默认环境和环境 ID 顾名思义。 环境可以随意命名，但务必保证默认的环境 ID 要匹配其中一个环境 ID。

### 4.2.1事务管理器（transactionManager）

在 MyBatis 中有两种类型的事务管理器（也就是 type="[JDBC|MANAGED]"）：

- JDBC – 这个配置直接使用了 JDBC 的提交和回滚设施，它依赖从数据源获得的连接来管理事务作用域。

- MANAGED – 这个配置几乎没做什么。它从不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 默认情况下它会关闭连接。然而一些容器并不希望连接被关闭，因此需要将 closeConnection 属性设置为 false 来阻止默认的关闭行为。例如:

```xml
<transactionManager type="MANAGED">
    <property name="closeConnection" value="false"/>
</transactionManager>
```

**注意**：如果使用 Spring + MyBatis，则没有必要配置事务管理器，因为 Spring 模块会使用自带的管理器来覆盖前面的配置。

### 4.2.2 数据源（dataSource）

功能：连接数据库（dbcp、c3p0、druid）

dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

- 大多数 MyBatis 应用程序会按示例中的例子来配置数据源。虽然数据源配置是可选的，但如果要启用延迟加载特性，就必须配置数据源。

有三种内建的数据源类型（也就是 type="[**UNPOOLED**|**POOLED**|**JNDI**]"）：

**UNPOOLED**– **这个数据源的实现会每次请求时打开和关闭连接**。

**POOLED**– 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。 这种处理方式很流行，能使并发 Web 应用快速响应请求。

以下属性用来配置 POOLED 的数据源：

- `driver` – 这是 JDBC 驱动的 Java 类全限定名（并不是 JDBC 驱动中可能包含的数据源类）。
- `url` – 这是数据库的 JDBC URL 地址。
- `username` – 登录数据库的用户名。
- `password` – 登录数据库的密码。
- `defaultTransactionIsolationLevel` – 默认的连接事务隔离级别。
- `defaultNetworkTimeout` – 等待数据库操作完成的默认网络超时时间（单位：毫秒）。查看 `java.sql.Connection#setNetworkTimeout()` 的 API 文档以获取更多信息。

- `poolMaximumActiveConnections` – 在任意时间可存在的活动（正在使用）连接数量，默认值：10
- `poolMaximumIdleConnections` – 任意时间可能存在的空闲连接数。
- `poolMaximumCheckoutTime` – 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫秒（即 20 秒）
- `poolTimeToWait` – 这是一个底层设置，如果获取连接花费了相当长的时间，连接池会打印状态日志并重新尝试获取一个连接（避免在误配置的情况下一直失败且不打印日志），默认值：20000 毫秒（即 20 秒）。
- `poolMaximumLocalBadConnectionTolerance` – 这是一个关于坏连接容忍度的底层设置， 作用于每一个尝试从缓存池获取连接的线程。 如果这个线程获取到的是一个坏的连接，那么这个数据源允许这个线程尝试重新获取一个新的连接，但是这个重新尝试的次数不应该超过 `poolMaximumIdleConnections` 与 `poolMaximumLocalBadConnectionTolerance` 之和。 默认值：3（新增于 3.4.5）
- `poolPingQuery` – 发送到数据库的侦测查询，用来检验连接是否正常工作并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动出错时返回恰当的错误消息。
- `poolPingEnabled` – 是否启用侦测查询。若开启，需要设置 `poolPingQuery` 属性为一个可执行的 SQL 语句（最好是一个速度非常快的 SQL 语句），默认值：false。
- `poolPingConnectionsNotUsedFor` – 配置 poolPingQuery 的频率。可以被设置为和数据库连接超时时间一样，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 为 true 时适用）。

**JNDI** – 这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的数据源引用。



## 4.3 属性（properties）

可以通过properties属性来实现引用配置文件。

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。【db.properties】

第一步：编写一个配置文件db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8
```

第二步：在核心配置文件中引入

**注意事项**：

- 在xml中，所有的标签都可以规定其顺序。

![image-20210713153511994](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713153511994.png)

```xml
<configuration>
    <!--  引入外部配置文件  -->
    <properties resource="db.properties">
        <!--  内部配置属性  -->
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </properties>
</configuration>
```

- 可以直接引入外部配置文件；
- 可以在其中增加一些属性配置；
- 如果两个文件有相同字段，优先使用外部配置文件中的属性；



## 4.4 类型别名（typeAliases）

- 类型别名可为 Java 类型设置一个缩写名字；
- 它仅用于 XML 配置，意在降低冗余的全限定类名书写；
- 如果实体类较多的情况下，建议使用方式二；
- 如果使用方式二，只能通过注解起别名（在实体类上增加注解`@Alias*()`）；

```xml
<!--  方式一：给实体类起别名  -->
<typeAliases>
<typeAlias alias="User" type="com.kuang.pojo.User" />
</typeAliases>
<!--  方式二：扫描实体类的包  -->
<typeAliases>
<package name="com.kuang.pojo"/>
</typeAliases>
```

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <!--  resultType使用别名User，取代com.kuang.pojo.User  ->
    <select id="getUserList" resultType="User"> 
        select * from mybatis.user
    </select>
</mapper>
```

对于一些常见的Java类型，Mybatis提供了别名，如下：

|    别名    | 映射的类型 |
| :--------: | :--------: |
|   _byte    |    byte    |
|   _long    |    long    |
|   _short   |   short    |
|    _int    |    int     |
|  _integer  |    int     |
|  _double   |   double   |
|   _float   |   float    |
|  _boolean  |  boolean   |
|   string   |   String   |
|    byte    |    Byte    |
|    long    |    Long    |
|   short    |   Short    |
|    int     |  Integer   |
|  integer   |  Integer   |
|   double   |   Double   |
|   float    |   Float    |
|  boolean   |  Boolean   |
|    date    |    Date    |
|  decimal   | BigDecimal |
| bigdecimal | BigDecimal |
|   object   |   Object   |
|    map     |    Map     |
|  hashmap   |  HashMap   |
|    list    |    List    |
| arraylist  | ArrayList  |
| collection | Collection |
|  iterator  |  Iterator  |



## 4.5 设置（settings）

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 下表描述了设置中各项设置的含义、默认值等（全部字段见官网文档）。

| 设置名                       | 描述                                                         | 有效值                                                       | 默认值 |
| :--------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----- |
| **cacheEnabled**             | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true   |
| **lazyLoadingEnabled**       | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false                                                | false  |
| **useGeneratedKeys**         | 允许 JDBC 支持自动生成主键，需要数据库驱动支持。如果设置为 true，将强制使用自动生成主键。尽管一些数据库驱动不支持此特性，但仍可正常工作（如 Derby）。 | true \| false                                                | False  |
| **mapUnderscoreToCamelCase** | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 | true \| false                                                | False  |
|                              |                                                              | 任何字符串                                                   | 未设置 |
| **logImpl**                  | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |



## 4.6 其他配置

- typeHandlers（类型处理器）

- objectFactory（对象工厂）

- plugins（插件）

  - MyBatis Generator Core
  - mybatis-plus
  - 通用mapper



## 4.7 映射器（mappers）

MapperRegistry：注册绑定我们的Mapper文件；

提供四种方式进行映射：

**方式一：**【推荐使用】

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="com/kuang/Mapper/UserMapper.xml"/>
</mappers>
```

**方式二：**使用class文件绑定注册  

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="com.kuang.Mapper.UserMapper"/>
</mappers>
```

**方式三：**使用扫描包进行注入绑定

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

**注意事项**：

对于方式二和方式三，要满足下列要求：

- 接口和它的Mapper配置文件必须同名；
- 接口和它的Mapper配置文件必须在同一个包下；



## 4.8 生命周期和作用域

**生命周期**和**作用域（Scope）**是至关重要的，因为错误的使用会导致非常严重的**并发问题**。

![image-20210713170935498](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713170935498.png)

**SqlSessionFactoryBuilder**：

- 一旦创建了 SqlSessionFactory，就不再需要它了；
- 局部变量；

**SqlSessionFactory**：

- 可以想象成为：数据库连接池；
- 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**； 
- 因此，SqlSessionFactory的最佳作用域是**应用（全局）作用域**；
- 最简单的就是使用**单例模式**或者静态单例模式。

**SqlSession**：

- 连接到数据库连接池的一个请求；
- SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是**请求或方法作用域**；
- 用完之后赶紧关闭，否则资源被占用；【重要】

![image-20210713171644442](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713171644442.png)

这里面每一个Mapper都代表一个具体的业务。



# 五 解决属性名和字段名不一致的问题

## 5.1 存在问题

数据库中的字段

![image-20210713203830649](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713203830649.png)

新建一个项目，拷贝之前的，测试实体类字段不一致的情况。

**实体类**：

```java
public class User {
    private int id;
    private String name;
    private String password;
}
```

**出现问题**：

如果再使用之前的方式去查询，会得到以下结果：

![image-20210713205519097](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713205519097.png)

显然，password字段没有得到，分析其原因可知：

![image-20210713205651668](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713205651668.png)



**解决方案**：

- 起别名；

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <select id="getUserById" parameterType="int" resultType="User">
        SELECT id, name, pwd as password FROM mybatis.user WHERE id = #{id}
    </select>
</mapper>
```

## 5.2 resultMap

结果集映射

```tex
id	name	pwd
id 	name	password
```

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <resultMap id="UserMap" type="User">
        <!--  column数据库中的字段，properties实体类中的属性  -->
        <result column="id" property="id" />
        <result column="name" property="name" />
        <result column="pwd" property="password" />
    </resultMap>
    <select id="getUserById" resultMap="UserMap">
        SELECT * FROM mybatis.user WHERE id = #{id}
    </select>
</mapper>
```

- `resultMap` 元素是 MyBatis 中最重要最强大的元素。
- `resultMap`的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。
-  `ResultMap` 的优秀之处在于不需要显式配置 `ResultMap`，

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <resultMap id="UserMap" type="User">
        <result column="pwd" property="password" />
    </resultMap>
    <select id="getUserById" resultMap="UserMap">
        SELECT * FROM mybatis.user WHERE id = #{id}
    </select>
</mapper>
```

**注意**：在引用语句中，使用`resultMap` 属性，其值为`ResultMap`的`id`值。



# 六 日志

## 6.1 日志工厂

如果一个数据库操作，出现了异常，需要排错，日志就是最好的助手。

以前：sout、debug

现在：日志工厂

Mybatis中设置属性（settings）提供了日志工厂的功能。

![image-20210713213739299](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713213739299.png)

- SLF4J
- LOG4J【掌握】
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING
- STDOUT_LOGGING【掌握】
- NO_LOGGING

在Mybatis中，具体使用哪个日志实现，在设置属性中进行设置。



## 6.2 STDOUT_LOGGING

**STDOUT_LOGGING标准日志输出**：

在mybatis核心配置文件中，配置我们的日志

```xml
<!--  设置属性（日志）  -->
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

**输出如下**：

![image-20210713215929311](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713215929311.png)



## 6.3 LOG4J

- Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件，甚至是套接口服务器、[NT](https://baike.baidu.com/item/NT/3443842)的事件记录器、[UNIX](https://baike.baidu.com/item/UNIX) [Syslog](https://baike.baidu.com/item/Syslog)[守护进程](https://baike.baidu.com/item/守护进程/966835)等；
- 可以控制每一条日志的输出格式；
- 通过定义每一条日志信息的级别，能够更加细致地控制日志的生成过程。
- 通过一个[配置文件](https://baike.baidu.com/item/配置文件/286550)来灵活地进行配置，而不需要修改应用的代码。

**使用步骤**：

1. 先导入log4j的包(pom.xml)

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.12</version>
</dependency>
```

2. 建立log4j.properties配置文件，内容如下(数字为行号)：

```properties
#将等级XDEBUG的日志信息输出到console树file.这两个目的地，console file的定义在下面的代码
log4j.rootLogger=DEBUG, console, file
######控制台输出的相关设置######
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold = DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern = [%c]-%m%n
######文件输出的相关设置######
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File = ./log/kuang.log
log4j.appender.file.MaxFileSize = 10mb
log4j.appender.file.Threshold = DEBUG
log4j.appender.file.layout = org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern = [%p][%d{yy-MM- dd}][%c]%m%n
######日志输出级别######
log4j.logger.org.mybatis = DEBUG
log4j.logger.java.sql = DEBUG
log4j.logger.java.sql.Statement = DEBUG
log4j.logger.java.sql.ResultSet = DEBUG
log4j.logger.java.sql.PreparedStatement = DEBUG
```

3. 配置log4j为日志的实现

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

4. Log4j的使用，直接测试运行

![image-20210713233329858](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210713233329858.png)



**简单使用**：

1. 在要使用Log4j的类中，导入包

```java
import org.apache.log4j.Logger;
```

2. 日志对象，参数为当前类的class

```java
static Logger logger = Logger.getLogger(当前类.class);
```

3. 日志级别

```java
logger.info("info：进入了testLog4j");
logger.debug("debug：进入了testLog4j");
logger.error("error：进入了testLog4j");
```

![image-20210714000357159](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210714000357159.png)

**日志文件**：

```tex
[DEBUG][21-07- 13][org.apache.ibatis.logging.LogFactory]Logging initialized using 'class org.apache.ibatis.logging.log4j.Log4jImpl' adapter.
[DEBUG][21-07- 13][org.apache.ibatis.logging.LogFactory]Logging initialized using 'class org.apache.ibatis.logging.log4j.Log4jImpl' adapter.
[DEBUG][21-07- 13][org.apache.ibatis.datasource.pooled.PooledDataSource]PooledDataSource forcefully closed/removed all connections.
[DEBUG][21-07- 13][org.apache.ibatis.datasource.pooled.PooledDataSource]PooledDataSource forcefully closed/removed all connections.
[DEBUG][21-07- 13][org.apache.ibatis.datasource.pooled.PooledDataSource]PooledDataSource forcefully closed/removed all connections.
[DEBUG][21-07- 13][org.apache.ibatis.datasource.pooled.PooledDataSource]PooledDataSource forcefully closed/removed all connections.
[DEBUG][21-07- 13][org.apache.ibatis.transaction.jdbc.JdbcTransaction]Opening JDBC Connection
[DEBUG][21-07- 13][org.apache.ibatis.datasource.pooled.PooledDataSource]Created connection 1848415041.
[DEBUG][21-07- 13][org.apache.ibatis.transaction.jdbc.JdbcTransaction]Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@6e2c9341]
[DEBUG][21-07- 13][com.kuang.Mapper.UserMapper.getUserById]==>  Preparing: SELECT * FROM mybatis.user WHERE id = ? 
[DEBUG][21-07- 13][com.kuang.Mapper.UserMapper.getUserById]==> Parameters: 2(Integer)
[DEBUG][21-07- 13][com.kuang.Mapper.UserMapper.getUserById]<==      Total: 1
[DEBUG][21-07- 13][org.apache.ibatis.transaction.jdbc.JdbcTransaction]Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@6e2c9341]
[DEBUG][21-07- 13][org.apache.ibatis.transaction.jdbc.JdbcTransaction]Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@6e2c9341]
[DEBUG][21-07- 13][org.apache.ibatis.datasource.pooled.PooledDataSource]Returned connection 1848415041 to pool.
[INFO][21-07- 14][MyTest]info：进入了testLog4j
[DEBUG][21-07- 14][MyTest]debug：进入了testLog4j
[ERROR][21-07- 14][MyTest]error：进入了testLog4j
```



# 七 分页

**思考**：为什么需要分页？

- 减少数据的处理量；



## 7.1 LIMIT分页

**语法**：

```sql
SELECT * FROM [表名] LIMIT startIndex, pageSize;
SELECT * FROM [表名] LIMIT N; # [0, N]
```



## 7.2 Mybatis分页

其核心还是SQL，使用步骤：

1. 接口

```java
public interface UserMapper {
    // 根据ID查询用户
    User getUserById(int id);

    // 分页
    List<User> getUserByLimit(Map<String, Integer> map);
}
```

2. Mapper.xml

```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <resultMap id="UserMap" type="User">
        <!--  column数据库中的字段，properties实体类中的属性  -->
        <result column="pwd" property="password" />
    </resultMap>
    <!--  方式一：分页查询  -->
    <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
        SELECT * FROM mybatis.user LIMIT #{startIndex}, #{pageSize}
    </select>
</mapper>
```

3. 测试

```java
@Test
public void testSelectLimit() {
    try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Integer> map = new HashMap<>();
        map.put("startIndex", 0);
        map.put("pageSize", 2);
        List<User> users = mapper.getUserByLimit(map);
        for (User user : users) {
            System.out.println(user);
        }
    }
}
```



## 7.3 RowBounds分页

不再使用SQL实现分页。

**代码示例**：

1. 接口

```java
public interface UserMapper {
    List<User> getUserByRowBounds();
}
```

2. mapper.xml
```xml
<mapper namespace="com.kuang.Mapper.UserMapper">
    <resultMap id="UserMap" type="User">
        <!--  column数据库中的字段，properties实体类中的属性  -->
        <result column="pwd" property="password" />
    </resultMap>
    <!--  方式二：分页查询（RowBounds）  -->
    <select id="getUserByRowBounds" resultMap="UserMap">
        SELECT * FROM mybatis.user
    </select>
</mapper>
```

3. 测试
```java
@Test
public void testSelectRowBounds() {
    try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        // RowBounds实现分页    offset：页数   limit：每页显示行数
        RowBounds rowBounds = new RowBounds(1, 2);

        // 通过Java代码层面实现分页
        List<User> users = sqlSession.selectList("com.kuang.Mapper.UserMapper.getUserByRowBounds", null, rowBounds);

        for (User user : users) {
            System.out.println(user);
        }
    }
}
```



## 7.4 分页插件

了解即可

![image-20210714012804564](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210714012804564.png)

**使用方法**：

1. **引入分页插件**，使用Maven：在pom.xml中添加如下依赖：

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>最新版本</version>
</dependency>
```

2. **配置拦截器插件**，在Mybatis核心配置文件中进行配置。

```xml
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
        <property name="param1" value="value1"/>
    </plugin>
</plugins>
```

3. 使用

```java
//第一种，RowBounds方式的调用
List<Country> list = sqlSession.selectList("x.y.selectIf", null, new RowBounds(0, 10));

//第二种，Mapper接口方式的调用，推荐这种使用方式。
PageHelper.startPage(1, 10);
List<Country> list = countryMapper.selectIf(1);

//第三种，Mapper接口方式的调用，推荐这种使用方式。
PageHelper.offsetPage(1, 10);
List<Country> list = countryMapper.selectIf(1);

//第四种，参数方法调用
//存在以下 Mapper 接口方法，你不需要在 xml 处理后两个参数
public interface CountryMapper {
    List<Country> selectByPageNumSize(
            @Param("user") User user,
            @Param("pageNum") int pageNum,
            @Param("pageSize") int pageSize);
}
//配置supportMethodsArguments=true
//在代码中直接调用：
List<Country> list = countryMapper.selectByPageNumSize(user, 1, 10);

//第五种，参数对象
//如果 pageNum 和 pageSize 存在于 User 对象中，只要参数有值，也会被分页
//有如下 User 对象
public class User {
    //其他fields
    //下面两个参数名和 params 配置的名字一致
    private Integer pageNum;
    private Integer pageSize;
}
//存在以下 Mapper 接口方法，你不需要在 xml 处理后两个参数
public interface CountryMapper {
    List<Country> selectByPageNumSize(User user);
}
//当 user 中的 pageNum!= null && pageSize!= null 时，会自动分页
List<Country> list = countryMapper.selectByPageNumSize(user);

//第六种，ISelect 接口方式
//jdk6,7用法，创建接口
Page<Country> page = PageHelper.startPage(1, 10).doSelectPage(new ISelect() {
    @Override
    public void doSelect() {
        countryMapper.selectGroupBy();
    }
});
//jdk8 lambda用法
Page<Country> page = PageHelper.startPage(1, 10).doSelectPage(()-> countryMapper.selectGroupBy());

//也可以直接返回PageInfo，注意doSelectPageInfo方法和doSelectPage
pageInfo = PageHelper.startPage(1, 10).doSelectPageInfo(new ISelect() {
    @Override
    public void doSelect() {
        countryMapper.selectGroupBy();
    }
});
//对应的lambda用法
pageInfo = PageHelper.startPage(1, 10).doSelectPageInfo(() -> countryMapper.selectGroupBy());

//count查询，返回一个查询语句的count数
long total = PageHelper.count(new ISelect() {
    @Override
    public void doSelect() {
        countryMapper.selectLike(country);
    }
});
//lambda
total = PageHelper.count(()->countryMapper.selectLike(country));
```



# 八 使用注解开发

## 8.1 面向接口编程

- 大家之前都学过面向对象编程，也学习过接口，但在真正的开发中，很多时候我们会选择面向接口编程
  **根本原因：解耦，可拓展，提高复用，分层开发中，上层不用管具体的实现，大家都遵守共同的标准，使得开发变得容易，规范性更好**；

- 在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的，对系统设计人员来讲就不那么重要了；
- 而各个对象之间的协作关系则成为系统设计的关键。小到不同类之间的通信，大到各模块之间的交互，在系统设
  计之初都是要着重考虑的，这也是系统设计的主要工作内容。面向接口编程就是指按照这种思想来编程；

**关于接口的理解**：

- 接口从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离；

- 接口的本身反映了系统设计人员对系统的抽象理解；

- 接口应有两类：

  - 第一类是对一个个体的抽象，它可对应为一个抽象体（abstract class）;

  - 第二类是对一个个体某一方面的抽象， 即形成一个抽象面（interface） ;

- 一个体有可能有多个抽象面。抽象体与抽象面是有区别的；

**三个面向区别**：

- 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法；
  
- 面向过程是指，我们考虑问题时，以一个具体的流程（事务过程）为单位，考虑它的实现；
- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题.更多的体现就是对系统整体的
  架构；



## 8.2 注解开发

- 本质：反射机制实现；
- 底层：动态代理；

![image-20210715010113995](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210715010113995.png)

**代码示例**：

1. 实体类

```java
public class User {
    private int id;
    private String name;
    private String password;

    public User() {
    }

    public User(int id, String name, String password) {
        this.id = id;
        this.name = name;
        this.password = password;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

2. 接口

```java
public interface UserMapper {
    @Select("SELECT * FROM user")
    List<User> getUsers();
}
```

3. Mybatis核心配置文件（绑定接口）

```xml
<!--  configuration：核心配置文件  -->
<configuration>
    <!--  引入外部配置文件  -->
    <properties resource="db.properties" />

    <!--  方式一：直接起别名  -->
    <typeAliases>
        <typeAlias alias="User" type="com.kuang.pojo.User" />
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--  绑定接口  -->
    <mappers>
        <mapper class="com.kuang.Mapper.UserMapper" />
    </mappers>

</configuration>
```

4. 测试

```java
 @Test
public void getUsersTest() {
    try(SqlSession sqlSession = MybatisUtils.getSqlSession()) {
        // 底层主要应用反射
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> users = mapper.getUsers();
        for (User user : users) {
            System.out.println(user);
        }
    }
}
```

5. 结果

![image-20210714020609765](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210714020609765.png)

**存在问题**：当数据库字段名和实体类属性名不一致时，无法获取。

对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很复杂的操作，最好用 XML 来映射语句。



## 8.3 Mybatis详细执行流程

![image-20210715005724901](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210715005724901.png)



## 8.4 CRUD

1. 可以在工具类创建的时候实现自动提交事务。

```java
public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession(true); // true：自动提交开启
    }
```

2. 接口，增加注解

```java
public interface UserMapper {
    @Select("SELECT * FROM user")
    List<User> getUsers();

    // 方法存在多个参数，所有参数前面必须加上@Param("id")注解
    @Select("SELECT * FROM user WHERE id = #{id} AND name = #{name}")
    User getUserByID(@Param("id") int id, @Param("name") String name);

    @Insert("INSERT INTO user(id, name, pwd) VALUES(#{id}, #{name}, #{password})")
    int addUser(User user);

    @Update("UPDATE user SET name = #{name}, pwd = #{password} WHERE id = #{id}")
    int updateInfo(User user);

    @Delete("DELETE FROM user WHERE id = #{id}")
    int deleteUser(@Param("id") int id);
}
```

3. 测试类

```java
public class MyTest {
    @Test
    public void getUsersTest() {
        try(SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            List<User> users = mapper.getUsers();

            for (User user : users) {
                System.out.println(user);
            }
        }
    }

    @Test
    public void getUsersTest2() {
        try (SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            User user = mapper.getUserByID(2, "李四");
            System.out.println(user);
        }
    }

    @Test
    public void addUserTest() {
        try(SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            int result = mapper.addUser(new User(6, "PGone", "123456"));
            if (result > 0) {
                System.out.println("插入成功");
            }
        }
    }

    @Test
    public void updateUserTest() {
        try(SqlSession sqlSession = MybatisUtils.getSqlSession()){
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            int result = mapper.updateInfo(new User(2, "殷鹏", "123456"));
            if (result > 0) {
                System.out.println("修改信息成功");
            }
        }
    }

    @Test
    public void deleteUserTest() {
        try(SqlSession sqlSession = MybatisUtils.getSqlSession()) {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);

            int result = mapper.deleteUser(7);
            if (result > 0) {
                System.out.println("删除成功");
            }
        }
    }
}
```

**注意事项**：

- 必须要将接口注册绑定到核心配置文件中；



## 8.5 @Param()注解

- 基本类型的参数或者String类型，需要加上；
- 引用类型不需要加；
- 如果只有一个基本类型，可以忽略，但建议都加上；
- 在SQL中引用就是这里的@Param("uid")中设定的属性名；



# 九 Lombok

```tex
Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.
```

**使用步骤**：

1. 在IDEA中安装Lombok插件；
2. 在项目中导入Lombok的jar包；

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>
```

3. 使用注解

```tex
@Getter and @Setter【掌握】
@FieldNameConstants
@ToString【掌握】
@EqualsAndHashCode【掌握】
@AllArgsConstructor【掌握】, @RequiredArgsConstructor and @NoArgsConstructor【掌握】
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
@Data【掌握】
@Builder
@SuperBuilder
@Singular
@Delegate
@Value
@Accessors
@Wither
@With
@SneakyThrows
@val
@var
experimental @var
@UtilityClass
```

## 9.1 @Data

在实体类上面加上@Data注解，会自动帮实体类加上以下方法：

![image-20210722144720888](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210722144720888.png)

## 9.2 @AllArgsConstructor

在实体类上面加上@AllArgsConstructor注解，会自动帮实体类加上全参构造：

![image-20210722144836191](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210722144836191.png)



## 9.3 @NoArgsConstructor

在实体类上面加上@NoArgsConstructor注解，会自动帮实体类加上无参构造：

![image-20210722144958831](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210722144958831.png)



# 十 复杂查询

**场景**

![image-20210722145555138](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210722145555138.png)

- 多个学生，对应一个老师；
- 对于学生而言，**关联**，多个学生关联一个老师【多对一】；
- 对于老师而言，**集合**，一个老师有很多学生【一对多】；

![image-20210722152707702](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210722152707702.png)

**SQL**

```sql
CREATE TABLE `teacher` (
  `id` int(10) NOT NULL,
  `name` char(30) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

INSERT INTO `mybatis`.`teacher` (`id`, `name`) VALUES ('1', '李老师');

CREATE TABLE `student` (
  `id` int(10) NOT NULL,
  `name` varchar(30) NOT NULL,
  `tid` int(10) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `fktid` (`tid`),
  CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

INSERT INTO `mybatis`.`student` ( `id`, `name`, `tid`) VALUES ('1', '小明','1');
INSERT INTO `mybatis`.`student` ( `id`, `name`, `tid`) VALUES ('2', '小红','1');
INSERT INTO `mybatis`.`student` ( `id`, `name`, `tid`) VALUES ('3', '小张','1');
INSERT INTO `mybatis`.`student` ( `id`, `name`, `tid`) VALUES ('4', '小李','1');
INSERT INTO `mybatis`.`student` ( `id`, `name`, `tid`) VALUES ('5', '小王','1');
```

## 10.1 测试环境搭建

1. 导入lombok；
2. 新建实体类Teacher、Student；
3. 新建Mapper接口；
4. 新建Mapper.xml配置文件；
5. 在核心配置文件中绑定注册我们的Mapper接口或者文件；
6. 测试查询是否能够成功；

## 10.2 多对一

**需求**：查询所有学生的信息；

**结果**：

![image-20210722174445429](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210722174445429.png)

### 10.2.1 方式一：按照查询嵌套处理

**核心代码**：

```xml
<mapper namespace="com.kuang.Mapper.StudentMapper">
    <!--
        思路：
            1.查询所有的学生信息
            2.根据查询出的学生的tid，寻找对应的老师  子查询
    -->
    <select id="getStudent" resultMap="StudentTeacher" >
        SELECT * FROM student
    </select>
    <resultMap id="StudentTeacher" type="Student" >
        <!--  简单属性  -->
        <id property="id" column="id" />
        <id property="name" column="name" />
        <!--  复杂属性，需要单独处理  -->
        <!--  对象：association -->
        <!--  集合：collection  -->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>

    </resultMap>

    <select id="getTeacher" resultType="Teacher" >
        SELECT * FROM teacher WHERE id = #{id}
    </select>
</mapper>
```

### 10.2.2 方式二：按照结果嵌套处理

**核心代码**：

```xml
<mapper namespace="com.kuang.Mapper.StudentMapper">
    <!--
        思路：
            1.查询所有的学生信息
            2.根据查询出的学生的tid，寻找对应的老师
    -->
    <select id="getStudent" resultMap="StudentMap" >
        SELECT s.id sid, s.name sname, t.name tname
        FROM student s, teacher t
        WHERE s.tid = t.id
    </select>
    <resultMap id="StudentMap" type="Student">
        <result property="id" column="sid" />
        <result property="name" column="sname" />
        <association property="teacher" javaType="Teacher">
            <result property="name" column="tname" />
        </association>
    </resultMap>
</mapper>
```

### 10.2.3 MySql关联查询

- 子查询（对应：按照查询嵌套处理）；
- 联表查询（对应：按照结果嵌套处理）；



## 10.3 一对多处理

比如：一个老师拥有多个学生，对于老师而言，就是一对多的关系

### 10.3.1 环境搭建

1. 实体类

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;

}
```

```java
@Data
public class Teacher {
    private int id;
    private String name;

    // 一个老师拥有多个学生
    private List<Student> student;
}
```



### 10.3.2 方式一：按结果嵌套处理

**核心代码**：

```xml
<mapper namespace="com.kuang.Mapper.TeacherMapper">
    <select id="getTeacher" resultMap="TeacherStudent">
        SELECT s.id sid, s.name sname, t.name tname, t.id tid
        FROM student s, teacher t
        WHERE s.tid = t.id AND t.id = #{tid}
    </select>

    <resultMap id="TeacherStudent" type="Teacher">
        <id property="id" column="tid" />
        <id property="name" column="tname" />
        <!--
            javaType：指定属性的类型
            集合中的泛型信息，使用ofType获取
        -->
        <collection property="student" ofType="Student">
            <id property="id" column="sid" />
            <id property="name" column="sname" />
            <id property="tid" column="tid" />
        </collection>

    </resultMap>
</mapper>
```



### 10.3.3 方式二：按查询嵌套处理

**核心代码**：

```xml
<mapper namespace="com.kuang.Mapper.TeacherMapper">
    <select id="getTeacher" resultMap="TeacherStudent">
        SELECT * FROM teacher WHERE id = #{tid}
    </select>
    <resultMap id="TeacherStudent" type="Teacher" >
        <id property="id" column="id" />
        <id property="name" column="name" />
        <collection property="student" column="id" ofType="Student" select="getStudent" />
    </resultMap>
    <select id="getStudent" resultType="Student" >
        SELECT * FROM student WHERE tid = #{id}
    </select>
</mapper>
```



## 10.4 小结

**区别**：

- 关联——association【多对一】
- 集合——collection【一对多】
- javaType  &  ofType
  - javaType用来指定实体类中属性的类型；
  - ofType用来指定映射到List或者集合中的pojo类型，泛型中的约束类型；



**注意事项**：

- 保证SQL的可读性，尽量保证通俗易懂；
- 注意一对多和多对一中，属性名和字段的问题；
- 如果问题不好排查错误，可以使用日志，建议使用Log4j；



**面试高频**：

- MySql引擎
- InnoDB底层原理
- 索引
- 索引优化





# 十一 动态SQL

**动态SQL就是指根据不同的条件生成不同的SQL语句，根据不同条件拼接 SQL 语句**。

在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

## 11.1 搭建环境

**SQL**：

```sql
CREATE TABLE `mybatis`.`blog` (  
  `id` VARCHAR(50) NOT NULL COMMENT '博客id',
  `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
  `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
  `create_time` DATETIME NOT NULL COMMENT '创建时间',
  `views` INT(30) NOT NULL COMMENT '游览量'
) ENGINE=INNODB CHARSET=utf8;
```

**创建一个基础工程**：

1. 导包
2. 编写配置文件
3. 编写实体类

```java
@Data
public class Blog {
    private int id;
    private String title;
    private String author;
    private Data creatTime; // 属性名和字段名不一致，可以核心配置文件中开启驼峰命名转换
    private int views;
}
```

4. 编写实体类对应的Mapper接口和Mapper.xml配置文件

## 11.2 if

**需求**：如果不传入title、author参数就查询全部数据，否则就加上判读。

**代码示例**：

```xml
<select id="queryBlogIF" parameterType="map" resultType="Blog">
    SELECT * FROM blog
    WHERE 1 = 1
    <if test="title != null">
        AND title = #{title}
    </if>
    <if test="author != null">
        AND author = #{author}
    </if>
</select>
```

## 11.3 trim (where, set)

**代码示例**：

```xml
<select id="queryBlogIF" parameterType="map" resultType="Blog">
    SELECT * FROM blog
    <where>
        <if test="title != null">
            title = #{title}
        </if>
        <if test="author != null">
            AND author = #{author}
        </if>
    </where>
</select>
```

```xml
<insert id="updateBlog" parameterType="map">
    UPDATE blog
    <set>
        <if test="titile != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    WHERE id = #{id}
</insert>
```

**所谓的动态SQL，本质还是SQL语句，只是可以在SQL层面，去执行一个逻辑代码。**

## 11.4 choose (when, otherwise)

**代码示例**：

```xml
<select id="queryBlogChoose" parameterType="map" resultType="Blog">
    SELECT * FROM blog
    <where>
        <choose>
            <when test="title != null">
                title = #{title}
            </when>
            <when test="author != null">
                AND author = #{author}
            </when>
            <otherwise>
                AND views = #{views}
            </otherwise>
        </choose>
    </where>
</select>
```



## 11.5 SQL片段

在开发中，SQL的拼接很常见，有很对拼接的sql具有重复性高的特点，这时最好**把重复的sql抽取出来，作为公用的sql片段。**

**代码示例**：

```xml
<sql id="if-title-author">
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        AND author = #{author}
    </if>
</sql>

<select id="queryBlogIf" parameterType="map" resultType="Blog">
    SELECT * FROM blog
    <where>
        <include refid="if-title-author"></include>
    </where>
</select>
```

**注意事项**：

- 最好基于单表来定义SQL片段；
- 不要存在where标签；



## 11.6 foreach

**代码示例**：

```xml
<!--  SELECT * FROM user WHERE 1=1 AND (id=1 or id=2 or id=3)  -->
<!--  map中存在一个集合ids  -->
<select id="queryBlogForeach" parameterType="map" resultType="Blog">
    SELECT * FROM blog
    <where>
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id = #{id}
        </foreach>
    </where>
</select>
```

**动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了**

建议：

- 先在Mysql中写出完整的SQL，再对应的去修改称为我们的动态SQL实现通用即可；



# 十二 缓存

## 12.1 简介

1. 什么是缓存【Cache】?
   - 存在内存中的临时数据。
   - 将用户经常查询的数据放在缓存(内存)中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查
     询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

2. 为什么使用缓存?
   - 减少和数据库的交互次数，减少系统开销，提高系统效率。
3. 什么样的数据能使用缓存?
   - 经常查询并且不经常改变的数据。【可以使用缓存】



## 12.2 Mybatis缓存

- MyBatis包含一个非常强大的查询缓存特性，**它可以非常方便地定制和配置缓存。**缓存可以极大的提升查询效
  率。
- MyBatis系统中默认定义了两级缓存: 一级缓存和二级缓存
  - 默认情况下，只有一级缓存开启。 (SqlSession级别的缓存， 也称为本地缓存)
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
  - 为了提高扩展性，MyBatis定义了 缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存



## 12.3 一级缓存

测试步骤：

1. 开启日志；

2. 测试在一个Session中查询两次相同的记录；

3. 查看日志输出；

   效果如下：

![image-20210723211108075](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210723211108075.png)

**缓存失效的情况**：

1. 查询不同的东西；
2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存；
3. 查询不同的Mapper.xml；
4. 手动清理缓存；



## 12.4 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存；
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；
- 工作机制：
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
  - 新的会话查询信息，就可以从二级缓存中获取内容；
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中；

**使用**：

要启用全局的二级缓存，只需要在你的SQL映射文件中添加一行：

```xml
<cache/>
```

还可以定制一些功能，如下：

```xml
<cache
    eviction="FIFO"
    flushInterval="60000"
    size="512"
    readOnly="true"
```

这个配置创建了一个FIFO缓存，每隔60秒刷新，最多可以存储结果对象或列表的512个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的嗲用着

**清楚策略**：

- `LRU`—最近最少使用：移除最长时间不被使用的对象；
- `FIFO`—先进先出：按对象进入缓存的顺序来移除它们；
- `SOFT`—软引用：基于垃圾回收器状态和软引用规则移除对象；
- `WEAK`—弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象；



## 12.5 缓存原理

![image-20210726160208293](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210726160208293.png)



## 12.6 自定义缓存—encache

Encache是一种广泛使用的开源Java分布式缓存，主要面向通用缓存。



**使用步骤**：

1. 导包

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis.caches</groupId>
        <artifactId>mybatis-ehcache</artifactId>
        <version>1.1.0</version>
    </dependency>
</dependencies>
```

2. 使用自定义缓存或者第三方缓存（Mapper.xml），来覆盖默认的缓存行为

```xml
<cache type="com.domain.something.MyCache"/> <!--使用自定义缓存-->
<cache type="org.mybatis.caches.ehcache.EhcacheCache" /> <!--使用第三方缓存EhCache-->
```



**自定义缓存**：

```java
public class MyCache implements Cache {
    @Override
    public String getId() {
        return null;
    }

    @Override
    public void putObject(Object key, Object value) {

    }

    @Override
    public Object getObject(Object key) {
        return null;
    }

    @Override
    public Object removeObject(Object key) {
        return null;
    }

    @Override
    public void clear() {

    }

    @Override
    public int getSize() {
        return 0;
    }
}
```

自定义缓存的重点在于，要实现Cache接口，并实现其方法。





















