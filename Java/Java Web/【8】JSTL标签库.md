# 一 JSTL标签库介绍

## 1.1 简洁

- JSTL标签库，全称是 JSP Standard Tag Library，JSP标准标签库，是一个不断完善的开放源代码的JSP标签库。

- EL表达式主要是为了替换JSP中的表达式脚本，而标签库则是为了替换代码脚本。这样使得整个JSP页面变得更加简洁。



## 1.2 组成

**JSTL由五个不同功能的标签库组成**：

|      功能范围      |                  URI                   | 前缀 |
| :----------------: | :------------------------------------: | :--: |
| 核心标签库（重点） |   http://java.sun.com/jsp/jstl/core    |  c   |
|       格式化       |    http://java.sun.com/jsp/jstl/fmt    | fmt  |
|        函数        | http://java.sun.com/jsp/jstl/functions |  fn  |
|  数据库（不使用）  |    http://java.sun.com/jsp/jstl/sql    | sql  |
|   XML（不使用）    |    http://java.sun.com/jsp/jstl/xml    |  x   |



## 1.3 引入

在JSP标签库中使用taglib指令引入标签库。

### 1.3.1 CORE标签库

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

### 1.3.2 XML标签库

```jsp
<%@ taglib prefix="x" uri="http://java.sun.com/jsp/jstl/xml" %>
```

### 1.3.3 FMT标签库

```jsp
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

### 1.3.4 SQL标签库

```jsp
<%@ taglib prefix="sql" uri="http://java.sun.com/jsp/jstl/sql" %>
```

### 1.3.5 FUNCTIONS标签库

```jsp
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

### 

## 1.4 使用步骤

1. 先导入JSTL标签库的jar包。

   - taglibs-standard-impl-1.2.5.jar
   - taglibs-standard-spec-1.2.5.jar

2. 使用taglib指令引入标签库。

   ```jsp
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   ```

   

## 二 CORE核心库的使用

## 2.1 <c:set />

**作用**：set标签可以向域中保存数据。

**代码示例**：

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <!--
        set标签可以向域中保存数据。
        1. scope属性设置保存到哪个域
            page表示PageContext域
            request表示Request域
            session表示Session域
            application表示ServletContext域
        2. var属性设置key是多少
        3. value属性设置value是多少
    -->
    保存之前：${ requestScope.abc } <br>
    <c:set scope="request" var="abc" value="123" />
    保存之后：${ requestScope.abc } <br>
</body>
</html>
```



## 2.2 <c:if />

**作用**：用来做if判断。

**代码示例**：

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <!--
        if标签用来做if判断
        test属性表示判断的条件（使用EL表达式输出）
    -->
    <c:if test="${ 12 == 12 }">
        <h1>条件成立</h1>
    </c:if>
</body>
</html>
```



## 2.3 < c:choose >、< c:when >、< c:otherwise >

**作用**：多路判断。

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
        < c:choose >、< c:when >、< c:otherwise >
        作用：多路判断。
        choose标签：开始选择判断
        when标签：每一种判断情况
            test属性表示判断的条件（使用EL表达式输出）
        otherwise标签：表示剩下的情况
    -->
    <%
        request.setAttribute("height", "178");
    %>
    身高：${ requestScope.height } <br>
    身高等级：
    <c:choose>
        <c:when test="${ requestScope.height > 180 }">A</c:when>
        <c:when test="${ requestScope.height > 175 }">B</c:when>
        <c:when test="${ requestScope.height > 170 }">C</c:when>
        <c:otherwise>D</c:otherwise>
    </c:choose>
</body>
</html>
```

**注意**：

- 标签里不能使用HTML注释，要使用JSP注释；
-  when标签的父标签一定要是choose标签；



## 2.4 <c:forEach />

**作用**：遍历输出使用。

### 2.4.1 遍历1到100

**代码示例**：

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
         < c:forEach / >
        作用：遍历输出。
        begin属性设置开始索引
        end属性设置结束索引
        var属性表示循环的变量(也是当前正在遍历的数据)
    --%>
    <%--1. 遍历1到10，输出--%>
    <c:forEach begin="1" end="10" var="i">
        ${ i }
    </c:forEach>
</body>
</html>
```



### 2.4.2 遍历Object数组

**代码示例**：

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
         < c:forEach / >
        作用：遍历输出。
        items表示遍历的数据源（遍历的集合）
        var表示当前遍历到的数据

        2. 遍历Object数组
    --%>
    <%
        request.setAttribute("arr", new String[]{"15283920849", "15283920848", "15283920847"});
    %>
    <c:forEach items="${requestScope.arr}" var="item">
        ${item}
    </c:forEach>
</body>
</html>
```



### 2.4.3 遍历Map集合

**代码示例**：

```jsp
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
         < c:forEach / >
        作用：遍历输出。
        items表示遍历的数据源（遍历的集合）
        var表示当前遍历到的数据

        3. 遍历Map集合
    --%>
    <%
        Map<String, Object> map = new HashMap<>();
        map.put("key1", "value1");
        map.put("key2", "value2");
        map.put("key3", "value3");
        request.setAttribute("map", map);
    %>
    <c:forEach items="${requestScope.map}" var="entry">
        键：${ entry.key }, 值：${ entry.value } <br>
    </c:forEach>
</body>
</html>
```



### 2.4.4 遍历List集合

**需求**：List中存放Student类，有属性：编号，用户名，密码，年龄，电话信息。

**代码示例**：

```jsp
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="java.util.List" %>
<%@ page import="com.example.JSTL.Student" %>
<%@ page import="java.util.ArrayList" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
         < c:forEach / >
        作用：遍历输出。
        items表示遍历的数据源（遍历的集合）
        var表示当前遍历到的数据
        begin表示遍历的开始索引值
        end表示结束的索引值
        step表示遍历的步长值
        varStatue表示当前遍历到的数据的状态
        3. 遍历List集合
    --%>
    <%
        ArrayList<Student> students = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            students.add(new Student(
                    i + 1,
                    "username_" + i,
                    "jsl123456",
                    20 + i,
                    "1528392084" + i
            ));
        }
        request.setAttribute("stus", students);
    %>
    <table border="1">
        <tr>
            <th>编号</th>
            <th>用户名</th>
            <th>密码</th>
            <th>年龄</th>
            <th>电话</th>
        </tr>
    <c:forEach items="${requestScope.stus}" var="stu">
        <tr>
            <td>${stu.id}</td>
            <td>${stu.username}</td>
            <td>${stu.password}</td>
            <td>${stu.age}</td>
            <td>${stu.phoneNumber}</td>
        </tr>
    </c:forEach>
    </table>
</body>
</html>
```

**LoopTagStatus接口**：

![image-20210623170409849](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210623170409849.png)

