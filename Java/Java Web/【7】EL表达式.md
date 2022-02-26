# 一 EL表达式介绍

## 1.1 定义

EL表达式的全称：Expression Language，是表达式语言。



## 1.2 作用

EL表达式主要是代替JSP页面中的表达式脚本在JSP页面中进行数据的输出。EL表达式在输出数据的时候，要比JSP的表达式脚本要简介很多。



**格式**：

```jsp
${表达式}
```



**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        request.setAttribute("key", "值");
    %>
    表达式脚本输出key的值是：<%=request.getAttribute("key")%><br/>
    EL表达式输出key的值是：${key}
</body>
</html>
```



**注意**：EL表达式在输出null值得时候，输出得是空串。JSP表达式脚本输出null值得时候，输出的是null字符串。



# 二 EL表达式搜索域数据的顺序

EL表达式主要是在JSP页面中输出**域对象**中的数据。

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        // 四个域中都保存了相同的key
        pageContext.setAttribute("key", "pageContext");
        request.setAttribute("key", "request");
        session.setAttribute("key", "session");
        application.setAttribute("key", "application");
    %>
    ${key}
</body>
</html>
```

**结论**：

**pageContext**  ==>  **request**  ==>  **session**  ==>  **application**

**注意**：

- **session**：存在一次会话中；
- **application**：存在项目工程一个生命周期内；



# 三 EL表达式输出Bean属性

**需求**：Person类中的普通属性、数组属性、List集合属性、Map集合属性。

**代码示例**：

```jsp
<%@ page import="com.example.EL_JSTL.Person" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %><%--
  Created by IntelliJ IDEA.
  User: Susanoo
  Date: 2021/6/1
  Time: 10:49
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
      Person person = new Person();
      person.setName("张三");
      person.setPhones(new String[]{"15283920849", "15283920848", "15283920847"});
      ArrayList<String> cities = new ArrayList<>();
      cities.add("北京");
      cities.add("上海");
      cities.add("深圳");
      person.setCities(cities);
      Map<String, Object> stringObjectMap = new HashMap<>();
      stringObjectMap.put("key1", "value1");
      stringObjectMap.put("key2", "value2");
      stringObjectMap.put("key3", "value3");
      person.setMap(stringObjectMap);

      pageContext.setAttribute("person", person);
    %>

    输出Person：${person} <br>
    输出Person中的name属性：${person.name} <br>
    输出Person中的phones数组属性值：${person.phones[0]} <br>
    输出Person中的cities集合中的元素值：${person.cities} <br>
    输出Person中的cities集合中的个别元素值：${person.cities[0]} <br>
    输出Person中的map集合的元素值：${person.map} <br>
    输出Person中的map集合中的某个key的元素值：${person.map.get("key1")} <br>
</body>
</html>
```

**注意**：在获取Bean属性时，是通过该属性对应`get()`方法获取的，如果该属性没有`get()`方法，则无法在JSP页面中获取到数据。



# 四 EL表达式——运算

**语法**：

```jsp
${运算表达式}
```

## 4.1 关系运算

| 关系运算符 |   说明   |               范例                | 结果  |
| :--------: | :------: | :-------------------------------: | :---: |
|  == 或 eq  |   等于   |  `${ 5 == 5 }` 或 `$ { 5 eq 5 }`  | true  |
|  != 或 ne  |  不等于  |  `${ 5 != 5 }` 或 `$ { 5 ne 5 }`  | false |
|  < 或 it   |   小于   |  `${ 3 < 5 }` 或 `$ { 5 lt 5 }`   | true  |
|  > 或 gt   |   大于   | `${ 2 > 10 }` 或 `$ { 2 gt 10 }`  | false |
|  <= 或 le  | 小于等于 | `${ 5 <= 12 }` 或 `$ { 5 le 12 }` | true  |
|  >= 或 ge  | 大于等于 |  `${ 3 >= 5 }` 或 `$ { 3 ge 5 }`  | false |

## 4.2 逻辑运算

| 逻辑运算符 |   说明   |                         范例                          | 结果  |
| :--------: | :------: | :---------------------------------------------------: | :---: |
| && 或 and  |  与运算  | `${12 == 12 && 12 < 11}` 或 `${12 == 12 and 12 < 11}` | false |
| \|\| 或 or |  或运算  | `${12 == 12 || 12 < 11}` 或 `${12 == 12 or 12 < 11}`  | true  |
|  ! 或 not  | 取反运算 |              `${!true}` 或 `${not true}`              | false |

## 4.3 算数运算

| 算数运算符 | 说明 |                 范例                 | 结果 |
| :--------: | :--: | :----------------------------------: | :--: |
|     +      | 加法 |            `${ 12 + 18 }`            |  30  |
|     -      | 减法 |            `${ 18 - 8 }`             |  10  |
|     +      | 乘法 |            `${ 12 * 12 }`            | 144  |
|  / 或 div  | 除法 | `${ 144 / 12 }` 或 `${ 144 div 12 }` |  12  |
|  % 或 mod  | 取模 | `${ 144 % 12 }` 或 `${ 144 mod 12 }` |  4   |



## 4.4 empty运算

empty运算可以判断一个数据是否为空，如果为空，则输出true，不为空输出false。

以下几种情况为空：

- 值为null值的时候，为空；
- 值为空串的时候，为空；
- 值是Object类型数组，长度为0的时候，为空；
- List集合，元素个数为0的时候，为空；
- Map集合，元素个数为0的时候，为空；



## 4.5 三元运算

**格式**：

```jsp
表达式1 ？ 表达式2 : 表达式3
```

**说明**：如果表达式1的值为真，返回表达式2的值，如果表达式1的值为假，返回表达式3的值。

**代码示例**：

```jsp
${ 12 != 12 ? "真" : "假" }
```



## 4.6 "."点运算和"[]"中括号运算符

"."点运算，可以输出Bean对象中某个属性的值。

"[]"中括号运算，可以输出有序集合中某个元素的值。并且"[]"中括号运算，还可以输出Map集合中key里含有特殊字符的key的值。

**代码示例**：

```jsp
${ map['a.a.a'] }
${ map['b+b+b'] }
${ map["c-c-c"] }
```



# 五 EL表达式的11个隐含对象

EL表达式中11个隐含对象，是EL表达式中自己定义的，可以直接使用。

|       变量       |         类型          |                      作用                      |
| :--------------: | :-------------------: | :--------------------------------------------: |
|   pageContext    |    PageContextImpl    |            获取JSP中的九大内置对象             |
|    pageScope     |  Map<String, Object>  |           获取pageContext域中的数据            |
|   requestScope   |  Map<String, Object>  |             获取Request域中的数据              |
|   sessionScope   |  Map<String, Object>  |             获取Session域中的数据              |
| applicationScope |  Map<String, Object>  |          获取ServletContext域中的数据          |
|      param       |  Map<String, String>  |                获取请求参数的值                |
|   paramValues    | Map<String, String[]> |              获取多个请求参数的值              |
|      header      |  Map<String, String>  |                获取请求头的信息                |
|   headerValues   | Map<String, String[]> |              获取多个请求头的信息              |
|      cookie      |  Map<String, Cookie>  |            获取当前请求的Cookie信息            |
|    initParam     |  Map<String, String>  | 获取web.xml中的**`<context-param>`**上下文参数 |



## 5.1 EL获取四个特定域中的属性

- pageScope：pageContext域
- requestScope：Request域
- sessionScope：Session域
- applicationScope：ServletContext域

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--  向pageContext域中存放数据  --%>
    <%
        pageContext.setAttribute("key1", "pageContext1");
        pageContext.setAttribute("key2", "pageContext2");
        request.setAttribute("key2", "request");
        session.setAttribute("key2", "session");
        application.setAttribute("key2", "application");
    %>
    <%--  获取pageContext域中的数据  --%>
    ${ pageScope.key2 }
</body>
</html>
```



## 5.2 pageContext对象的使用

- 协议；
- 服务器IP；
- 服务器端口；
- 获取工程路径；
- 获取请求方法；
- 获取客户端IP地址；
- 获取会话的ID编号；



### 5.3 EL表达式其他隐含对象的使用

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
        注意：在EL表达式中，点运算符获取属性数据，是通过该属性对应的get()方法获取的。
    --%>
    - 协议；${ pageContext.request.scheme }，<%=request.getScheme()%> <br>
    - 服务器IP；${ pageContext.request.serverName }，<%=request.getServerName()%> <br>
    - 服务器端口；${ pageContext.request.serverPort }，<%=request.getServerPort()%> <br>
    - 获取工程路径；${ pageContext.request.contextPath }，<%=request.getContextPath()%> <br>
    - 获取请求方法；${ pageContext.request.method }，<%=request.getMethod()%> <br>
    - 获取客户端IP地址；${ pageContext.request.remoteHost }，<%=request.getRemoteHost()%> <br>
    - 获取会话的ID编号；${ pageContext.session.id }，<%=session.getId()%> <br>
</body>
</html>
```



## 5.4 param对象&&paramValues对象

**代码示例**：

```tex
// 请求地址
http://localhost:8080/EL_JSTL_war_exploded/other_obj.jsp?username=123&&password=123&&hobby=java&&hobby=cpp
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    输出请求参数：${ param } <br>
    输出请求参数username的值：${ param.username } <br>
    输出请求参数password的值：${ param.password } <br>
    输出请求参数username的值：${ paramValues.username[0] } <br>
    输出请求参数hobby的值：${ paramValues.hobby[0] } <br>
    输出请求参数hobby的值：${ paramValues.hobby[1] } <br>
</body>
</html>
```



## 5.5 header对象&&headerValues对象

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    输出请求头【User-Agent】的值：${ header["User-Agent"] } <br>
    输出请求头【Connection】的值：${ header["Connection"] } <br>
    输出请求头【User-Agent】的值：${ headerValues["User-Agent"][0] } <br>
</body>
</html>
```



## 5.6 cookie对象

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    获取Cookie的名称：${ cookie.JSESSIONID.name } <br>
    获取Cookie的值：${ cookie.JSESSIONID.value } <br>
</body>
</html>
```



## 5.7 initParam对象

**代码示例**：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    获取context-param上下文参数：${ initParam } <br>
</body>
</html>
```



