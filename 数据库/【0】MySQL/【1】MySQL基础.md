# 一 概述

## 1.1 数据库的优点

- 实现数据持久化
- 使用完整的管理系统统一管理，易于查询。



## 1.2 相关概念

### 1.2.1 DB

**数据库（database）**：存储数据的"仓库"，它保存了一系列有组织的数据；

### 1.2.2 DBMS

**数据库管理系统（Database Management System）**：数据库是通过**DBMS**创建和操作的容器；

常见的数据库管理系统：MySQL、Oracle、DB2、SqlServer等；

**DBMS分为两类**：

- 基于共享文件系统的**DBMS**（`Access`）
- 基于客户机——服务器的**DBMS***（`MySQL`、`Oracle`、`SqlServer`）

### 1.2.3 SQL

**结构化查询语言（Structure Query Language）**：专门用于与数据库通信的语言；

**SQL的优点**：

1. 不是某个特定数据库供应商专有的语言，几乎所有DBMS都支持SQL；

2. 简单易学；
3. 是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作；



## 1.3 数据库的特点

1. 将数据放到表中，表再放到库中；

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210426152150971.png" alt="image-20210426152150971" style="zoom:50%;" />

2. 一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己，表名具有唯一性；

3. 表具有一些特性，这些特性定义了数据在表中如何存储，类似Java中"类"的设计；

4. 表由列组成，也称为字段。所有表都是由一个或多个列组成的，每一列类似Java中的"属性"；
5. 表中的数据是按行存储的，每一行类似于Java中的"对象"‘；



# 二 DQL语言

## 2.1 基础查询（★）

### 2.1.1 语法

```sql
SELECT 查询列表 FROM 表名;
```

### 2.1.2 特点

1. 查询列表可以是：**表中字段**、**常量值**、**表达式**、**函数**；

2. 查询的结果是一个虚拟的表格；

### 2.1.3 查询单个字段

**查询单个字段**：

```sql
SELECT 字段 FROM 表名;
```

**查询多个字段**：

```sql
SELECT 字段1, 字段2, 字段3, ... FROM 表名;
```

​	**注意**：

1. 查询结果中，字段的顺序，与查询语句中字段的顺序一致。

**查询所有字段**：

```sql
SELECT * FROM 表名;
```

```sql
SELECT 字段1, 字段2, ..., 字段n FROM 表名;
```

​	**注意**：

1. 使用`*`时，查询结果中，字段的顺序，与创建表时字段的顺序一致。
2. 不使用`*`时，查询结果中，字段的顺序，与查询语句中字段的顺序一致。

### 2.1.4 查询常量值

```sql
SELECT 100; # 查询数字
SELECT 'john'; # 查询字符串
```

**注意**：

1. 在SQL语言中，没有字符串和字符的区分。

### 2.1.5 查询表达式

```sql
SELECT 100%99;
```

### 2.1.6 查询函数

```sql
SELECT VERSION();
```

### 2.1.7 起别名

**方式一**：

```sql
SELECT 100%99 AS 结果;
```

```sql
SELECT 字段1 AS 别名1, 字段2 AS 别名2 FROM 表名;
```

​	**好处**：

1. 便于理解；
2. 多表查询时，如果查询的字段有重名的情况，使用别名可以区分开来；

**方式二**：

```sql
SELECT 字段1 别名1, 字段2, 别名2 FROM 表名;
```

**特殊案例**：

如果别名中有特殊字符，如空格、#等等，需要将别名加上双引号。

```sql
SELECT 字段1 AS "别 名 1", 字段2 AS "别 名 2" FROM 表名;
```

### 2.1.8 去重

案例：查询员工表中涉及到的所有的部门编号。

```sql
SELECT DISTINCT 字段1 FROM 表名;
```

### 2.1.9 +号的作用

案例：查询员工名和姓连接成一个字段，并显示为 姓名。

```sql
SELECT 字段1 + 字段2 AS 新字段名 FROM 表名; # 结果：0
```

**注意**：在MySQL中的+号，**仅有一个功能，就是运算**。

- 当两个操作数都为**数值型**时，则做加法运算。

- 当一个为**字符型**，另一个为**数值型**时，会试图将**字符型**转换成**数值型**。

  如果转换成功，则继续进行加法运算。

  如果转换失败，则将字符型数值转换成0。

- 当其中一个为`null`时， 结果必定为`null`。

### 2.1.10 CONCAT实现连接

案例：查询员工名和姓连接成一个字段，并显示为 姓名。

```sql
SELECT CONCAT(字段1, 字段2) AS 新字段名 FROM 表名; # 结果：0
```



## 2.2 条件查询（★）

### 2.2.1 语法

```sql
SELECT 查询列表 FROM 表名 WHERE 筛选条件;
```

**注意**：

1. 筛选条件的结果只有`true`和`false`两种；

### 2.2.2 分类

- 按条件表达式筛选
  - 条件运算符：`>`、`<`、`=`、`<>`、`>=`、`<=`

- 按逻辑表达式筛选
  - 逻辑运算符：`&&`、`||`、`!`、`and`、`or`、`not`
    - `&&`和`and`：如果两个条件都为`true`，结果为`true`，否则为`false`；
    - `||`和`or`：只有有一个条件为`true`，结果为`true`，否则为`false`；
    - `!`和`not`：如果连接的条件本身为false，结果为`true`，反之为`false`；

- 模糊查询
  - `LIKE`
  - `BETWEEN AND`
  - `IN`：判断某字段的值是否属于`IN`列表中的某一项；
  - `IS NULL`

### 2.2.3 条件表达式查询

```sql
# 按【条件表达式】筛选
# 案例1：查询工资 > 12000的员工信息
SELECT * FROM 表名 WHERE 工资字段 > 12000;
# 案例2：查询部门编号不等于90号的员工名和部门编号
SELECT 员工名, 部门编号 FROM 表名 WHERE 部门编号 <> 90;
```

### 2.2.4 逻辑表达式查询

```sql
# 按【逻辑表达式】筛选
# 案例1：工资在10000到20000之间的员工名、工资、奖金
SELECT 员工名, 工资, 奖金 FROM 表名 WHERE 工资 >= 10000 AND 工资 <= 20000;
# 案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息
SELECT * FROM 表名 WHERE 部门编号 < 90 OR 部门编号 > 110 OR 工资 > 15000;
SELECT * FROM 表名 WHERE NOT(部门编号 >= 90 AND 部门编号 <= 110) OR 工资 > 15000;
```

### 2.2.5 模糊查询

**LIKE**：

```sql
# 【模糊查询】筛选—— LIKE
# 案例1：查询员工名中包含字符a的员工信息
SELECT * FROM 表名 WHERE 员工名 LIKE '%a%';
# 案例2：查询员工名中第3个字符为e，第5个字符为a的员工名和工资
SELECT 员工名, 工资 FROM 表名 WHERE 员工名 LIKE '__e_a%';
# 【特殊情况】案例3：查询员工名中第二个字符为_的员工名
SELECT 员工名 FROM 表名 WHERE 员工名 LIKE '-\-%';
SELECT 员工名 FROM 表名 WHERE 员工名 LIKE '_$_%' ESCAPE '$';
```

**注意：**

1. 这里`%`为通配符，代表**任意多个字符**，包含0个字符；
2. `_`表示**任意单个字符**；
3. `LIKE` 一般与通配符搭配使用；
4. 如果在模糊查询条件中，有`%`和`_`，则可以使用转义字符`\`；



**BETWEEN AND**：

```sql
# 【模糊查询】筛选 —— BETWEEN AND
# 案例1：查询员工编号在100到120之间的员工信息
SELECT * FROM 表名 WHERE 员工编号 BETWEEN 100 AND 120;
```

**注意**：

1. 使用`BETWEEN AND` 可以提高语句的简洁度；
2.  `BETWEEN AND` 是包含临界值的；
3. 临界值不能调换顺序；



**IN**：

```sql
# 【模糊查询】筛选 —— IN
# 案例1：查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT 员工名, 工种编号 FROM 表名 WHERE 工种编号 IN (IT_PROG, AD_VP, AD_PRES)
```

**注意**：

1. 使用`IN` 提高语句简洁度；
2. `IN` 列表的值类型必须一致或兼容；
3. `IN` 列表的值是**不支持**通配符；



**IS NULL**：

```sql
# 【模糊查询】筛选 —— IS NULL
# 案例1：查询没有奖金的员工名和奖金率
SELECT 员工名, 奖金率 FROM 表名 WHERE 奖金率 IS NULL;
# 案例2：查询有奖金的员工名和奖金率
SELECT 员工名, 奖金率 FROM 表名 WHERE 奖金率 IS NOT NULL;
```

**注意**：

1. `IS NULL`或`IS NOT NULL` 可以判断`NULL`值，而`=`和`<>`不能进行判断。



### 2.2.6 安全等于

安全等于：`<=>` 可用于判断普通类型的值，也可用于判断`NULL`值，但其可读性较低。

```sql
# 案例1：查询工资为12000的员工信息
SELECT * FROM 表名 WHERE 工资 <=> 12000;
```



## 2.3 排序查询（★）

### 2.3.1 语法

```sql
SELECT 查询列表 FROM 表名 (WHERE 筛选条件) ORDER BY 排序列表 (ASC | DESC);
```

### 2.3.2 案例

```sql
# 案例1：查询员工信息，要求工资从高到低排序
SELECT * FROM 表名 ORDER BY 工资 DESC;

# 案例2【添加筛选条件】：查询部门编号>=90的员工信息，按入职时间的先后进行排序
SELECT * FROM 表名 WHERE 部门编号 >= 90 ORDER BY 入职时间 (ASC);

# 案例3【按表达式排序】：按年薪的高低显示员工的信息和年薪
SELECT *, 工资*12*(1 + IFNULL(奖金率, 0)) 年薪 FROM 表名 ORDER BY 工资*12*(1 + IFNULL(奖金率, 0)) DESC;

# 案例4【案例3简化】：
SELECT *, 工资*12*(1 + IFNULL(奖金率, 0)) 年薪 FROM 表名 ORDER BY 年薪 DESC;

# 案例5【按函数排序】：按姓名的长度显示员工的姓名和工资
SELECT LENGTH(员工姓名) 字节长度, 员工姓名, 工资 FROM 表名 ORDER BY 字节长度 DESC;

# 案例6【多字段排序】：查询员工信息，要求先按工资升序，再按员工编号降序
SELECT * FROM 表名 ORDER BY 工资 ASC, 员工编号 DESC;
```

### 2.3.3 总结

- `ASC` 为升序，`DESC` 为降序，当不写该字段时，默认的是升序；
- `ORDER BY` 子句中可以支持单个字段、多个字段、表达式、函数、别名；
- `ORDER BY`子句一般是放在查询语句的最后面，但`LIMIT` 子句除外；



## 2.4 常见函数（★）

**概念**：类似于Java中的方法，将一组逻辑语句封装在方法体中，对外暴露方法名；

**好处**：

- 隐藏了实现细节；
- 提供了代码的重用性；

**调用**：

```sql
SELECT 函数名(实参列表) (FROM 表名);
```

**特点**：

1. 叫什么（函数名）
2. 干什么（函数功能）

**分类**：

- 单行函数：`CONCAT()`、`LENGTH()`、`IFNULL()`等
- 分组函数：
  - 功能：做统计使用，又称为统计函数、聚合函数、组函数。

### 2.4.1 字符函数

**一、`LENGTH()`：**统计字符类型的值的长度（**字节**）。

```sql
SELECT LENGTH('JOHN'); # 输出：4
SELECT LENGTH('张三丰hahaha'); # 输出：15
```

​	**注意**：根据使用的字符集不同，其一个中文的字节数并不相同。

**二、`CONCAT()`：**拼接字符串。

```sql
SELECT CONCAT(字段1, '_', 字段2) FROM 表名;
```

**三、`UPPER()、LOWER`：**将字母进行大小写转换。

```sql
SELECT UPPER('john'); # 输出：JOHN
SELECT LOWER('JOHN'); # 输出：john
```

```sql
# 案例1：将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(姓), LOWER(名)) 姓名 FROM 表名;
```

​	**注意**：一个函数的返回值可以当作另一个函数的参数。

**四、`SUBSTR()、SUBSTRING()`：**截取字符串。

```sql
# 截取从指定索引处后面的所有字符
SELECT SUBSTR('李莫愁爱上了路展元', 7) # 输出：路展元
# 截取从指定索引处指定字符长度的字符
SELECT SUBSTR('李莫愁爱上了路展元', 1, 3) # 输出：李莫愁
```

```sql
# 案例2：姓名中首字符大写，其他字符小写然后用_拼接，显示出来
SELECT CONCAT(UPPER(SUBSTR(姓名, 1, 1)), '_', LOWER(SUBSTR(姓, 2))) out_put FROM 表名;
```

**注意**：**索引从1开始**。

**五、`INSTR()`：**查找子串在主串中的起始索引位置，索引从1开始。如果主串中不包含字串，则返回0。

```sql
SELECT INSTR('杨不悔爱上了殷梨亭', '殷梨亭') AS out_put; # 输出：7
```

**六、`TRIM()`：**去掉前后空格或指定子串。

```sql
SELECT TRIM('     张翠山      ') AS out_put; # 输出：张翠山
```

```sql
SELECT TRIM('a' FROM 'aaaa张aaaaa翠aaaaa山aaaaa') AS out_put; # 输出：张aaaaa翠aaaaa山
```

**七、`LPAD()`：**用指定的字符实现左填充指定长度。

```sql
SELECT LPAD('殷素素', 10, '*') AS out_put; # 输出：*******殷素素
```

**八、`RPAD()`：**用指定的字符实现右填充指定长度。

```sql
SELECT RPAD('殷素素', 12, 'ab') AS out_put; # 输出：殷素素ababababa
```

**九、`REPLACE()`：**替换。

```sql
SELECT REPLACE('张无忌爱上了周芷若', '周芷若', '赵敏') AS out_put; # 输出：张无忌爱上了赵敏
```

### 2.4.2 数学函数

**一、`ROUND()`：**四舍五入，两种重载。

```sql
SELECT ROUND(1.65); # 输出：2
```

```sql
SELECT ROUND(1.567, 2); # 输出：1.57
```

**二、`CEIL()`：**向上取整，返回`>=`该参数的最小整数。

```sql
SELECT CEIL(1.20); # 输出：2
```

**三、`FLOOR()`：**向下取整，返回`<=`该参数的最大整数。

```sql
SELECT FLOOR(9.99); # 输出：9
```

**四、`TRUNCATE()`：**截断，保留小数点后几位。

```sql
SELECT TRUNCATE(1.65, 1); # 输出：1.6
```

**五、`MOD()`：**取余。

```sql
SELECT MOD(10, 3); # 输出：1
```

### 2.4.3 日期函数

**一、`NOW()`：**返回当前系统日期 + 时间。

```sql
SELECT NOW(); # 输出：2017-09-29 11:12:38
```

**二、`CURDATE()`：**返回当前系统日期，不包含时间。

```sql
SELECT CURDATE(); # 输出：2017-09-29
```

**三、`CURTIME()`：**返回当前时间，不包含日期。

```sql
SELECT CURTIME(); # 输出：11:13:35
```

**四、`YEAR()`、`MONTH()`、`MONTHNAME()`、`DAY()`、`HOUR()`：**获取指定的部分，年、月、日、小时、分钟、秒。

```sql
SELECT YEAR(NOW()); # 输出：2017
```

**五、`STR_TO_DATE`：**将日期格式的字符转换成指定格式的日期。

```sql
SELECT STR_TO_DATE('9-13-1999', '%m-%d-%Y'); # 输出：1999-09-13
```

**六、`DATE_FORMAT`：**将日期转换成字符。

```sql
SELECT DATE_FORMAT('2018/6/6', '%Y年%m月%d日'); # 输出：2018年06月06日
```

| 序号 | 格式符 |            功能             |
| :--: | :----: | :-------------------------: |
|  1   |   %Y   |         四位的年份          |
|  2   |   %y   |          2位的年份          |
|  3   |   %m   | 月份（01，02，...，11，12） |
|  4   |   %c   |  月份（1，2，...，11，12）  |
|  5   |   %d   |      日（01，02，...）      |
|  6   |   %H   |      小时（24小时制）       |
|  7   |   %h   |      小时（12小时制）       |
|  8   |   %i   |   分钟（00，01，...，59）   |
|  9   |   %s   |    秒（00，01，...，59）    |

### 2.4.4 其他函数

**一、`VERSION()`：**查看版本号。

**二、`DATABASE()`：**查看当前库。

**三、`USER()`：**查看当前用户。

### 2.4.5 流程控制函数

**一、`IF()`**：`if else`的效果。

```sql
SELECT IF(10 > 5, '大', '小');
```

**二、`CASE()`：**

使用一：`switch case`的效果

```sql
CASE 要判断的字段或表达式
WHEN 常量1 THEN 要显示的值1或语句1;
WHEN 常量2 THEN 要显示的值2或语句2;
...
ELSE 要显示的值n或语句n;
END
```

```sql
/*
	案例：查询员工的工资，要求
	部门号=30，显示的工资为1.1倍
	部门号=40，显示的工资为1.2倍
	部门号=50，显示的工资为1.3倍
	其他部门，显示的工资为原工资
*/
SELECT 工资, 部门编号,
CASE 部门编号
WHEN 30 THEN 工资 * 1.1
WHEN 40 THEN 工资 * 1.2
WHEN 50 THEN 工资 * 1.3
ELSE 工资
END AS 新工资
FROM 表名;
```

 使用二：多重`if`的效果

```sql
CASE
WHEN 条件1 THEN 要显示的值1或语句1;
WHEN 条件2 THEN 要显示的值2或语句2;
...
ELSE 要显示的值n或语句n;
END
```

```sql
/*
	案例：查询员工的工资的情况
	如果工资 > 20000，显示A级别；
	如果工资 > 15000，显示B级别；
	如果工资 > 10000，显示C级别；
	否则，显示D级别；
*/
SELECT 工资,
CASE
WHEN 工资 > 20000 THEN 'A'
WHEN 工资 > 15000 THEN 'B'
WHEN 工资 > 10000 THEN 'C'
ELSE 'D'
END AS 工资级别
FROM 表名;
```

### 2.4.6 分组函数

**概述**：用作统计使用，又称为聚合函数或统计函数或组函数。

**分类**：`SUM()`求和、`AVG()`平均值、`MAX()`最大值、`MIN()`最小值、`COUNT()`计算个数。

**简单使用**：

```sql
SELECT SUM(工资) FROM 表名;
SELECT AVG(工资) FROM 表名;
SELECT MIN(工资) FROM 表名;
SELECT MAX(工资) FROM 表名;
SELECT COUNT(工资) FROM 表名;
SELECT SUM(工资) 工资和, AVG(工资) 平均工资, MIN(工资) 最小工资 FROM 表名;
```

**使用特点**：

- 参数支持的类型：
  - `SUM()`、`AVG()`：数值型；
  - `MAX()`、`MIN()`、`COUNT()`：任何类型；

- 所有分组函数都会忽略`NULL`值；

- 可以和`DISTINCT`关键字搭配实现去重的运算；

  ```sql
  SELECT SUM(DISTINCT 工资), SUM(工资) FROM 表名;
  ```

  ```sql
  # 案例：计算有多少种工资
  SELECT COUNT(DISTINCT 工资), COUNT(工资) FROM 表名;
  ```

- `COUNT()`函数的详细介绍：

  ```sql
  # 统计总函数
  SELECT COUNT(工资) FROM 表名;
  SELECT COUNT(*) FROM 表名;
  SELECT COUNT(1) FROM 表名;
  ```

  - `MYISAM`存储引擎下，`COUNT(*)`的效率高；
  - `INNODB`存储引擎下，`COUNT(*)`和`COUNT(1)`的效率 差不多，但比`COUNT(字段)`要高一些；
  - `COUNT(*)`一般被用作统计行数；

- 和分组函数一同查询的字段要求是`GROUP BY`后的字段；

  ```sql
  SELECT AVG(工资)、员工编号 FROM 表名; # 无意义！！！
  ```



## 2.5 分组查询（★）

### 2.5.1 概述

**引入**：查询每个部门的平均工资。

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210505151520257.png" alt="image-20210505151520257" style="zoom: 67%;" />

**分组数据**：`GROUP BY`子句语法，可以使用`GROUP BY`子句将表中的数据分成若干组。

```sql
SELECT column, group_function(column)
FROM table
[WHERE condition]
[GROUP BY group_by_expression]
[ORDER BY column];
```

**注意**：

- `WHERE`一定要放在`FROM`后面；
- 查询列表比较特殊，要求是分组函数和`GROUP BY`后出现的字段；

### 2.5.2 使用案例

```sql
# 案例1：查询每个工种的最高工资
SELECT MAX(工资), 工种 FROM 表名 GROUP BY 工种;
```

```sql
# 案例2：查询每个位置上的部门个数
SELECT COUNT(*), 位置号 FROM 表名 GROUP BY 位置号;
```

### 2.5.3 添加分组前筛选

```sql
# 案例1：查询邮箱中包含a字符的，每个部门的平均工资。
SELECT AVG(工资), 部门编号
FROM 表名
WHERE 邮箱 LIKE '%a%'
GROUP BY 部门编号;
```

```sql
# 案例2：查询有奖金的每个领导手下员工的最高工资
SELECT MAX(工资), 领导
FROM 表名
WHERE 奖金 IS NOT NULL
GROUP BY 领导;
```

### 2.5.4 添加分组后筛选

```sql
# 案例1：查询那个部门的员工个数 > 2
# STEP1：查询每个部门的员工个数
SELECT COUNT(*), 部门标号
FROM 表名
GROUP BY 部门编号;

# STEP2：根据STEP1的结果进行筛选，查询哪个部门的员工个数 > 2
SELECT COUNT(*), 部门标号
FROM 表名
GROUP BY 部门编号
HAVING COUNT(*) > 2;
```

```sql
# 案例2：查询每个工种有奖金的员工的最高工资 > 12000的工种编号和最高工资
# STEP1：查询每个工种有奖金的员工的最高工资
SELECT MAX(工资), 工种
FROM 表名
WHERE 奖金率 IS NOT NULL
GROUP BY 工种;

# STEP2：根据STEP1的结果继续筛选，最高工资 > 12000
SELECT MAX(工资), 工种
FROM 表名
WHERE 奖金率 IS NOT NULL
GROUP BY 工种
HAVING MAX(工资) > 12000;
```

```sql
# 案例3：查询领导编号 > 102 每个领导手下的最低工资 > 5000 的领导编号是哪个，以及其最低工资
# STEP1：查询每个领导手下员工的最低工资
SELECT MIN(工资), 领导编号
FROM 表名
GROUP BY 领导编号;

# STEP2：添加筛选条件：编号 > 102
SELECT MIN(工资), 领导编号
FROM 表名
WHERE 领导编号 > 102
GROUP BY 领导编号;

# STEP3：最低工资 > 5000
SELECT MIN(工资), 领导编号
FROM 表名
WHERE 领导编号 > 102
GROUP BY 领导编号
HAVING MIN(工资) > 5000;
```

### 2.5.5 总结

- 分组查询中的筛选条件可以分为两类：

  |            |     数据源     |        位置         |   关键字   |
  | :--------: | :------------: | :-----------------: | :--------: |
  | 分组前筛选 |     原始表     | **GROUP BY** 子句前 | **WHERE**  |
  | 分组后筛选 | 分组后的结果集 | **GROUP BY** 子句后 | **HAVING** |

  - 分组函数做条件肯定是放在`HAVING`子句中；

  - 能用分组前筛选的，就优先考虑使用分组前筛选；

- `GROUP BY`子句支持单个字段分组、多个字段分组（多个字段之间用逗号隔开没有顺序要求），表达式或函数（用的较少）；
- 也可以添加排序（排序放在整个分组查询的最后）；

### 2.5.6 按表达式或函数分组

```sql
# 案例：按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数 > 5的有哪些
# STEP1：查询每个长度的员工个数
SELECT COUNT(*), LENGTH(员工姓名) len_name
FROM 表名;
GROUP BY LENGTH(员工姓名);
# STEP2：添加筛选条件
SELECT COUNT(*), LENGTH(员工姓名) len_name
FROM 表名;
GROUP BY LENGTH(员工姓名)
HAVING COUNT(*) > 5;
```

**注意**：可以使用别名。

### 2.5.7 按多字段分组

```sql
# 案例：查询每个部门每个工种的员工的平均工资
SELECT AVG(工资), 部门, 工种
FROM 表名
GROUP BY 部门, 工种;
```

### 2.5.8 添加排序

```sql
# 案例：查询每个部门每个工种的员工的平均工资，并且按平均工资的高低显示
SELECT AVG(工资), 部门, 工种
FROM 表名
GROUP BY 部门, 工种
[HAVING AVG(工资) > 10000]
ORDER BY AVG(工资) DESC;
```



## 2.6 连接查询（★）

**含义**：又称为多表查询，当查询的字段来自于多个表时，就会用到连接查询。

### 2.6.1 笛卡尔乘机

**语法**：`SELECT name, boyName FROM beauty, boys;`

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210505162559013.png" alt="image-20210505162559013" style="zoom:67%;" />

**笛卡尔集的错误情况**：

`SELECT COUNT(*) FROM beauty;`：假设输出12行；

`SELECT COUNT(*) FROM boys;`：假设输出4行；

最终结果：12 * 4 = 48 行；

### 2.6.2 分类

- 按年代分类：
  - sql92标准：仅仅支持内连接；
  - sql99标准【推荐】： 支持 内连接 + 外连接（左外 + 右外） + 交叉连接

- 按功能分类：

  - 内连接：
    - 等值连接
    - 非等值连接
    - 自连接

  - 外连接
    - 左外连接
    - 右外连接
    - 全外连接
  - 交叉连接

### 2.6.3 等值连接

**一、sql92标准**

1. **等值连接**：

   ```sql
   # 案例1：查询女神名和对应的男神名
   SELECT NAME, boyName
   FROM boys, beauty
   WHERE beauty.boyfriend_id = boys.id;
   ```

   ```sql
   # 案例2：查询员工名和对应的部门名
   SELECT 员工名, 部门名
   FROM 员工表, 部门表
   WHERE 员工表.部门编号 = 部门表.部门编号
   ```

   ```sql
   # 案例3：查询员工名、工种号、工种名
   # 为表起别名
   SELECT 员工名, e.工种号, 工种名
   FROM 员工表 e, 工种表 j
   WHERE e.工种号 = j.工种号
   ```

- 为表起别名的好处：

  - 提高语句的简洁度；

  - 区分多个重名的字段；

  - 注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定。

- 两个表的顺序时可以调换的；

- 可以添加筛选；

  ```sql
  # 案例1：查询有奖金的员工名、部门名
  SELECT 员工名, 部门名
  FROM 员工表 e, 部门表 d
  WHERE e.部门编号 = d.部门编号
  AND e.奖金率 IS NOT NULL;
  ```

  ```sql
  # 案例2：查询城市名中第二个字符为o的部门名和城市名
  SELECT 部门名, 城市名
  FROM 部门表 d, 城市表 l
  WHERE d.城市编号 = l.城市编号
  AND 城市名 LIKE '_o%';
  ```

- 可以添加分组；

  ```sql
  # 案例1：查询每个城市的部门个数
  SELECT COUNT(*) 个数, 城市名
  FROM 部门表 d, 城市表 l
  WHERE d.城市编号 = l.城市编号
  GROUP BY 城市名;
  ```

  ```sql
  # 案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
  SELECT 部门名, d.领导编号, MIN(工资) 最低工资
  FROM 部门表 d, 员工表 e
  WHERE d.部门编号 = e.部门编号
  AND 奖金率 IS NOT NULL
  GROUP BY 部门名, d.领导编号
  ```

- 可以添加排序；

  ```sql
  # 案例1：查询每个工种的工种名和员工的个数，并且按员工个数降序
  SELECT 工种名, COUNT(*)
  FROM 员工表 e, 工种表 j
  WHERE e.工种编号 = j.工种编号
  GROUP BY 工种名
  ORDER BY COUNT(*) DESC;
  ```

- 三表连接；

  ```sql
  # 案例1：查询员工名、部门名和所在的城市
  SELECT 员工名, 部门名, 城市
  FROM 员工表 e, 部门表 d, 城市表 l
  WHERE e.部门编号 = d.部门编号
  AND d.城市编号 = l.城市编号;
  ```

- 总结：
  - 多表等值连接的结果为多表的交集部分；
  - n表连接，至少需要n-1个连接条件；
  - 多表的顺序没有要求；
  - 一般需要为表起别名；
  - 可以搭配前面介绍的所有子句使用，比如排序、分组、筛选；

2. **非等值连接**：

```sql
# 案例1：查询员工的工资和工资级别
SELECT 工资, 工资等级
FROM 员工表 e, 工资等级表 g
WHERE 工资 BETWEEN g.最低工资 AND g.最高工资
[AND 筛选条件]
```

3. **自连接**：

```sql
# 案例1：查询 员工名 和 上级的名称
SELECT e.员工编号, e.员工名, m.员工编号, m.员工名
FROM 员工表 e, 员工表 m
WHERE e.领导编号;
```

**二、sql99标准**

**语法**：

```sql
SELECT 查询列表
FROM 表1 别名 【连接类型】
JOIN 表2 别名
ON 连接条件
[WHERE 筛选条件]
[GROUP BY 分组]
[HAVING 筛选条件]
[ORDER BY 排序列表]
```

**连接类型**：

- 内连接：`INNER`
- 外连接：
  - 左外连接：`LEFT`【`OUTER`】
  - 右外连接：`RIGHT`【`OUTER`】
  - 全外连接：`FULL`【`OUTER`】

- 交叉连接：`CROSS`

### 2.6.4 内连接

**语法**：

```sql
SELECT 查询列表
FROM 表1 别名
INNER JOIN 表2 别名
ON 连接条件
```

**分类**：

- 等值连接
- 非等值连接
- 自连接

**特点：**

- 添加排序、分组、筛选；
- `INNER`可以省略；
- 筛选条件放在`WHERE`后面，连接条件放在`ON`后面，提高分离性，便于阅读；
- `INNER JOIN`连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集；

**一、等值连接**：

```sql
# 案例1：查询员工名、部门名
SELECT 员工名, 部门名
FROM 员工表 e
INNER JOIN 部门表 d
ON e.部门编号 = d.部门编号;
```

```sql
# 案例2：查询名字中包含e的员工名和工种名【添加筛选】
SELECT 员工名, 工种名
FROM 员工表 e
INNER JOIN 工种表 j
ON e.工种编号 = j.工种编号
WHERE e.员工名 LIKE '%e%';
```

```sql
# 案例3：查询部门个数 > 3 的城市名和部门个数【添加分组 + 筛选】
SELECT 城市名, COUNT(*) 部门个数
FROM 部门表 d
INNER JOIN 城市表 l
ON d.城市编号 = l.城市编号
GROUP BY 城市名
HAVING COUNT(*) > 3;
```

```sql
# 案例4：查询哪个部门的员工个数 > 3的部门名和员工个数，并按个数降序【添加排序】
SELECT 部门名, COUNT(*) 员工个数
FROM 部门表 d
INNER JOIN 员工表 e
ON d.部门编号 = e.部门编号
GROUP BY 部门名
HAVING COUNT(*) > 3
ORDER BY 员工个数 DESC;
```

```sql
# 案例5：查询员工名、部门名、工种名，并按部门名排序【三表连接】
SELECT 员工名, 部门名, 工种名
FROM 员工表 e
INNER JOIN 部门表 d ON e.部门编号 = d.部门编号
INNER JOIN 工种表 j ON e.工种编号 = j.工种编号
ORDER BY 部门名 DESC;
```

**二、非等值连接**：

```sql
# 案例1：查询员工的工资级别
SELECT 工资, 工资等级
FROM 员工表 e
JOIN 工资等级表 g
ON e.工资 BETWEEN g.最低工资 AND g.最高工资;
```

```sql
# 案例2：查询工资级别的个数 > 20，并且按工资级别降序
SELECT COUNT(*), 工资等级
FROM 员工表 e
JOIN 工资等级表 g
ON e.工资 BETWEEN g.最低工资 AND g.最高工资;
HAVING COUNT(*) > 20
ORDER BY 工资级别 DESC;
```

**三、自连接**：

```sql
# 案例1：查询员工的名字、上级的名字
SELECT e.姓名 m.姓名
FROM 员工表 e
JOIN 员工表 m
ON e.领导编号 = m.员工编号;
```

```sql
# 案例1：查询姓名中包含字符k员工的名字、上级的名字
SELECT e.姓名 m.姓名
FROM 员工表 e
JOIN 员工表 m
ON e.领导编号 = m.员工编号
WHERE e.姓名 LIKE '%k%';
```

### 2.6.5 外连接

**应用场景**：用于查询一个表中有，另一个表没有的记录。

**引入**：查询男朋友 不在男生表的女神名

<img src="https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210506221902998.png" alt="image-20210506221902998" style="zoom: 50%;" />

```sql
# 【左】外连接 实现
SELECT b.name, bo.*
FROM 女神表 b
LEFT OUTER JOIN 男生表 bo
ON b.男朋友编号 = bo.男生编号
WHERE bo.男生编号 IS NULL;

# 【右】外连接 实现
SELECT b.name, bo.*
FROM 男生表 bo
RIGHT OUTER JOIN 女神表 b
ON b.男朋友编号 = bo.男生编号
WHERE bo.男生编号 IS NULL;
```

**特点**：

- 外连接的查询结果为主表中的所有记录
  - 如果从表中有和它匹配的，则显示匹配的值；
  - 如果从表中没有和它匹配的，则显示`NULL`；
  -  外连接查询结果 = 内连接结果 + 主表中有而从表没有的记录
  - 全外连接查询结果 = 内连接结果 + 主表中有而从表没有的记录 + 从表中右而主表中没有的记录

- 左外连接，`LEFT JOIN`左边的是主表；

  右外连接，`RIGHT JOIN`右边的是主表；

- 左外和右外交换两个表的顺序，可以实现同样的效果；

```sql
# 案例1：查询哪个部门没有员工
# 【左】外连接 实现
SELECT d.*, e.员工编号
FROM 部门表 d
LEFT OUTER JOIN 员工表 e
ON d.部门编号 = e.部门编号
WHERE e.员工编号 IS NULL;

# 【右】外连接 实现
SELECT d.*, e.员工编号
FROM 员工表 e
RIGHT OUTER JOIN 部门表d
ON d.部门编号 = e.部门编号
WHERE e.员工编号 IS NULL;
```

**全外连接语法**：

```sql
SELECT b.*, bo.*
FROM 女神表 b
FULL OUTER JOIN 男生表 bo
ON b.男朋友编号 = bo.男生编号;
```

### 2.6.6 交叉连接

效果为笛卡尔乘积

**语法**：

```sql
SELECT b.*, bo.*
FROM 女神表 b
CROSS JOIN 男生表 bo;
```

### 2.6.7 总结

- sql92和sql99
  - 功能：sql99支持的较多；
  - 可读性：sql99实现连接条件和筛选条件的分离，可读性较高；

- 连接效果分类：

![image-20210507095022547](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210507095022547.png)

![image-20210507095330361](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210507095330361.png)



## 2.7 子查询

### 2.7.1 含义

出现在其他语句（增、删、改）中的`SELECT`语句，称为子查询或内查询。

外部的查询语句，称为主查询或外查询。

```sql
SELECT 员工姓名
FROM 员工表
WHERE 部门编号 IN (
	SELECT 部门编号
    FROM 部门表
    WHERE 城市编号 = 1700
);
```

### 2.7.2 分类

- 按子查询出现的位置：
  - `SELECT`后面：
    - 仅仅支持**标量子查询**
  - `FROM`后面：
    - 支持**表子查询**
  - `WHERE`或`HAVING`后面（★）：
    - **标量子查询**（★）
    - **列子查询**（★）
    - **行子查询**（用的较少）
  - `EXISTS`后面（相关子查询）
    - **表子查询**

- 按结果集的行列数不同：
  - **标量子查询**（结果集只有一行一列）
  - **列子查询**（结果集只有一列多行）
  - **行子查询**（结果集有多行多列）
  - **表子查询**（结果集一般为多行多列）

### 2.7.3 WHERE、HAVING后

**一、标量子查询（单行子查询）**：

```sql
# 案例1：谁的工资比Abel高？
# STEP1：查询Abel的工资
SELECT 工资
FROM 员工表
WHERE 姓名 = 'Abel';
# STEP2：查询员工的信息，满足 工资 > STEP1 的结果
SELECT *
FROM 员工表
WHERE 工资 > (
	SELECT 工资
	FROM 员工表
	WHERE 姓名 = 'Abel'
);
```

```sql
# 案例2：返回工种号与141号员工相同，工资比143号员工多的员工的姓名、工种号、工资
# STEP1：查询141号员工的工种号
SELECT 工种号
FROM 员工表
WHERE 员工编号 = 141;
# STEP2：查询143号员工的工资
SELECT 工资
FROM 员工表
WHERE 员工编号 = 143;
# STEP3：查询工种号满足STEP1、工资满足STEP2的员工的姓名、工种号、工资
SELECT 员工姓名, 工种号, 工资
FROM 员工表
WHERE 工种号 = (
	SELECT 工种号
	FROM 员工表
	WHERE 员工编号 = 141
) AND 工资 > (
	SELECT 工资
	FROM 员工表
	WHERE 员工编号 = 143
);
```

```sql
# 案例3：返回公司工资最少的员工的姓名、工种号和工资【使用分组函数】
# STEP1：查询公司的 最低工资
SELECT MIN(工资)
FROM 员工表;
# STEP2：查询员工的姓名、工种号、工资，要求工资 = STEP1的结果
SELECT 员工姓名, 工种号, 工资
FROM 员工表
WHERE 工资 = (
	SELECT MIN(工资)
	FROM 员工表
);
```

```sql
# 案例4：查询最低工资>50号部门最低工资的部门编号和最低工资
# STEP1：查询50号部门的最低工资
SELECT MIN(工资)
FROM 员工表
WHERE 部门编号 = 50;
# STEP2：查询每个部门的最低工资
SELECT 部门编号, MIN(工资) 最低工资
FROM 员工表
GROUP BY 部门编号;
# STEP3：筛选最低工资 > STEP1结果 的集合
SELECT 部门编号, MIN(工资) 最低工资
FROM 员工表
GROUP BY 部门编号
HAVING 最低工资 > (
	SELECT MIN(工资)
	FROM 员工表
	WHERE 部门编号 = 50
);
```

**二、列子查询（多行子查询）**：

|       操作符        |              含义              |
| :-----------------: | :----------------------------: |
| **IN** / **NOT IN** | 等于列表中的**任意一个**（★）  |
| **ANY** / **SOME**  | 和子查询返回的**某一个**值比较 |
|       **ALL**       |  和子查询返回的**所有**值比较  |

```sql
# 案例1：返回城市编号是1400或1700的部门中的所有员工姓名
# step1：查询城市编号是1400或1700的部门编号
SELECT DISTINCT 部门编号
FROM 部门表
WHERE 城市编号 IN (1400, 1700);
# STEP2：查询城市编号满足STEP1的所有员工姓名
SELECT 员工姓名
FROM 员工表
WHERE 城市编号 IN (
	SELECT DISTINCT 部门编号
	FROM 部门表
	WHERE 城市编号 IN (1400, 1700)
);
```

```sql
# 案例2：返回其他工种中比工种号为'IT_PROG'工种任一工资低的员工的员工号、姓名、工种号以及工资
# STEP1：查询工种号为'IT_PROG'部门任一工资
SELECT DISTINCT 工资
FROM 员工表
WHERE 工种号 = 'IT_PROG';
# STEP2：查询员工号、姓名、工种号以及工资，工资 < ANY (STEP1)
SELECT 员工号, 姓名, 工种号, 工资
FROM 员工表
WHERE 工资 < ANY (
	SELECT DISTINCT 工资
	FROM 员工表
	WHERE 工种号 = 'IT_PROG'
) AND 工种 <> 'IT_PROG';
# STEP2：其他方式实现
SELECT 员工号, 姓名, 工种号, 工资
FROM 员工表
WHERE 工资 < (
	SELECT MAX(工资)
	FROM 员工表
	WHERE 工种号 = 'IT_PROG'
) AND 工种 <> 'IT_PROG';
```

```sql
# 案例3：返回其他部门中比工种号为'IT_PROG'部门所有工资都低的员工 的员工号、姓名、工种号以及工资
SELECT 员工号, 姓名, 工种号, 工资
FROM 员工表
WHERE 工资 < ALL (
	SELECT DISTINCT 工资
	FROM 员工表
	WHERE 工种号 = 'IT_PROG'
) AND 工种 <> 'IT_PROG';

# 其他方式实现
SELECT 员工号, 姓名, 工种号, 工资
FROM 员工表
WHERE 工资 < (
	SELECT MIN(工资)
	FROM 员工表
	WHERE 工种号 = 'IT_PROG'
) AND 工种 <> 'IT_PROG';
```

**三、行子查询（多列多行）**：

```sql
# 案例1：查询员工编号最小并且工资最高的员工信息
# 原来的方式实现
# STEP1：查询最小的员工编号
SELECT MIN(员工编号)
FROM 员工表;
# STEP2：查询最高的工资
SELECT MAX(工资)
FROM 员工表;
# STEP3：查询员工编号最小并且工资最高的员工信息
SELECT *
FROM 员工表
WHERE 员工编号 = (
	SELECT MIN(员工编号)
	FROM 员工表
) AND 工资 = (
	SELECT MAX(工资)
	FROM 员工表
);

# 使用行子查询代替
SELECT *
FROM 员工表
WHERE (员工编号, 工资) = (
	SELECT MIN(员工编号), MAX(工资)
    FROM 员工表
);
```

**特点**：

- 子查询都放在小括号内；

- 子查询一般放在条件的右侧；

- 标量子查询，一般搭配着单行操作符（`>`、`<`、`>=`、`<=`、`=`、`<>`）使用；

  列子查询，一般搭配着多行操作符（`IN`、`ANY/SOME`、`ALL`）使用；

- 子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果；

### 2.7.4 SELECT后面

```sql
# 案例1：查询每个部门的员工个数
SELECT d.*, (
	SELECT COUNT(*)
    FROM 员工表 e
    WHERE e.部门编号 = d.部门编号
) 个数
FROM 部门表 d;
```

```sql
# 案例2：查询员工号 = 102 的部门名
SELECT (
	SELECT 部门名
    FROM 部门表 d
    INNER JOIN 员工表 e
    ON d.部门编号 = e.部门编号
    WHERE e.员工编号 = 102 
) 部门名;
```

**注意**：

- `SELECT`后面的子查询都是一行一列，即仅仅支持标量子查询。

### 2.7.5 FROM后面

```sql
# 案例1：查询每个部门的平均工资的工资等级
# STEP1：查询每个部门的平均工资
SELECT AVG(工资), 部门编号
FROM 员工表
GROUP BY 部门编号;
# STEP2：连接STEP1的结果集和工资等级表，筛选条件平均工资 BETWEEN 最低工资 AND 最高工资
SELECT ag_dep.*, g.工资等级
FROM (
	SELECT AVG(工资) ag, 部门编号
	FROM 员工表
	GROUP BY 部门编号
) ag_dep
INNER JOIN 工资等级表 g
ON ag_dep.ag BETWEEN 最低工资 AND 最高工资;
```

**注意**：

- 将子查询结果充当一张表，要求必须起别名；

### 2.7.6 EXISTS后面（相关子查询）

**语法**：

```sql
EXISTS(完整的查询语句)
```

**结果**：

- 结果只有1或0；

```sql
# 案例1：查询有员工的部门名
SELECT 部门名
FROM 部门表 d
WHERE EXISTS (
	SELECT *
    FROM 员工表 e
    WHERE d.部门编号 = e.部门编号
);
# 使用IN的方式代替
SELECT 部门名
FROM 部门表 d
WHERE d.部门编号 IN (
	SELECT DISTINCT 部门编号
    FROM 员工表
);
```

```sql
# 案例2：查询没有女朋友的男神信息
# 使用IN的方式实现
SELECT bo.*
FROM 男生表
WHERE bo.男生编号 NOT IN (
	SELECT 男生编号
    FROM 女神表
);
# 使用EXISTS的方式实现
SELECT bo.*
FROM 男生表 bo
WHERE NOT EXISTS(
	SELECT 男朋友编号
    FROM 女神表 b
    WHERE bo.男生编号 = b.男朋友编号
)
```



## 2.8 分页查询（★）

**应用场景**：当要显示的数据，一页显示不全，需要分页提交sql请求。

**语法**：

```sql
SELECT 查询列表
FROM 表1
[TYPE JOIN 表2]
[ON 连接条件]
[WHERE 筛选条件]
[GROUP BY 分组字段]
[HAVING 分组后的筛选]
[ORDER BY 排序的字段]
LIMIT [offset], size;
# offset：要显示条目的起始索引（起始索引从0开始）,不写时，默认从0开始。
# size：要显示的条目个数
```

```sql
# 案例1：查询前五条员工信息
SELECT * FROM 员工表 LIMIT 0, 5;
SELECT * FROM 员工表 LIMIT 5;
```

```sql
# 案例2：查询第11条——第25条
SELECT * FROM 员工表 LIMIT 10, 15;
```

```sql
# 案例3：有奖金的员工信息，并且工资较高的前10名显示出来
SELECT * FROM 员工表 WHERE 奖金率 IS NOT NULL ORDER BY 工资 DESC LIMIT 10;
```

**特点**：

- `LIMIT`语句放在查询语句的最后；

- 公式：

  要显示的页数 page，每页的条目数size

  ```sql
  SELECT 查询列表
  FROM 表
  LIMIT (page - 1) * size, size;
  # size = 10
  # page	offset
  #  1	  0
  #  2      10
  #  3      20
  ```

  

## 2.9 联合查询

**UNION联合 合并**：将多条查询语句的结果合并成一个结果；

**引入**：

```sql
# 案例：查询部门编号 > 90 或邮箱包含a的员工信息
# 不使用联合查询的方式
SELECT * FROM 员工表 WHERE 邮箱 LIKE '%a%' OR 部门编号 > 90;
# 使用联合查询的方式
SELECT * FROM 员工表 WHERE 邮箱 LIKE '%a%'
UNION
SELECT * FROM 员工表 WHERE 部门编号 > 90;
```

**语法**：

```sql
查询语句1
UNION
查询语句2
UNION
...
```

**应用场景**：

- 要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时

```sql
# 案例：查询中国用户中男性的用户信息以及外国用户中男性的用户信息
SELECT id, cname, csex FROM t_ca WHERE csex = '男'
UNION
SELECT t_id, tName, tGender FROM t_ua WHERE tGender = 'male';
```

**特点（★）**：

- 要求多条查询语句的查询列数是一致的；
- 要求多条查询语句的查询的每一列的类型和顺序最好一致；
- `UNION`关键字默认去重，如果使用`UNION ALL`可以包含重复项；



# 三 DML语言

数据操作语言：插入`INSERT`、修改`UPDATE`、删除`DELETE`

## 3.1 插入语句

### 3.1.1 方式一

**语法**：

```sql
INSERT INTO 表名 (字段1, 字段2, ..., 字段n) VALUES (值1, 值2, ..., 值n);
```

**注意：**

- 插入的值的类型要与列的类型一致或兼容；

  ```sql
  INSERT INTO beauty(id, NAME, sex, borndate, phone, photo, boyfriend_id)
  VALUES(13, '唐艺昕', '女', '1990-4-23', '1898888888', NULL, 2);
  ```

- 不可以为`NULL`的列必须插入值，可以为`NULL`的列如何插入值？

  - 方式一：可以`NULL`的值，直接填写`NULL`即可。

  - 方式二：直接默认不写该字段即可，即

    ```sql
    INSERT INTO beauty(id, NAME, sex, borndate, phone, boyfriend_id)
    VALUES(13, '唐艺昕', '女', '1990-4-23', '1898888888', 2);
    ```

- 列（字段）的顺序可以调换；
- 列（字段）和值的个数必须匹配；
- 可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致；

### 3.1.2  方式二

**语法**：

```sql
INSERT INTO 表名
SET 字段1 = 值, 字段2 = 值, ......
```

### 3.1.3 两种方式比较

|          | 方式一 | 方式二 |
| :------: | :----: | :----: |
| 插入多行 |  支持  | 不支持 |
|  子查询  |  支持  | 不支持 |

**插入多行数据**：

```sql
INSERT INTO beauty
VALUES(23, '唐艺昕1', '女', '1990-4-23', '1898888888', NULL, 2)
, (24, '唐艺昕2', '女', '1990-4-23', '1898888888', NULL, 2)
, (25, '唐艺昕3', '女', '1990-4-23', '1898888888', NULL, 2)
```

**子查询**：

```sql
INSERT INTO beauty(id, NAME, phone)
SELECT 26, '宋茜', '11809866';
```



## 3.2 修改语句

### 3.2.1 修改单表记录

**语法**：

```sql
UPDATE 表名
SET 字段1 = 新值, 字段2 = 新值, ...
[WHERE 筛选条件] # 如果不添加筛选条件，则默认修改所有行对应字段的数据。
```

**案例**：

```sql
# 案例1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty
SET 电话 = '13899888899'
WHERE NAME LIKE '唐%';
```

```sql
# 案例2：修改boys表中id号为2的名称为张飞，魅力值为 10
UPDATE boys SET boyname = '张飞', usercp = 10;
```

### 3.2.2 修改多表的记录

**语法**：

```sql
# sql92语法
UPDATE 表1 别名, 表2 别名,
SET 字段1 = 新值, 字段2 = 新值, ...
WHERE 连接条件
AND 筛选条件;

# sql99语法（★）
UPDATE 表1 别名
INNER | LEFT | RIGHT JOIN 表2 别名
ON 连接条件
SET 字段1 = 新值, 字段2 = 新值, ...
WHERE 筛选条件;
```

**案例**：

```sql
# 案例1：修改张无忌的女朋友的手机号为114
UPDATE boys bo
INNER JOIN beauty b
ON bo.'id' = b.'boyfriend_id'
SET b.'phone' = '114'
WHERE bo.'boyName' = '张无忌';
```

```sql
# 案例2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b ON bo.'id' = b.'boyfriend_id'
SET b.'boyfriend_id' = 2
WHERE b.'id' IS NULL;
```



## 3.3 删除语句

### 3.3.1 DELETE

**单表的删除**（★）：

```sql
DELETE FROM 表名 WHERE 筛选条件;
```

**案例**：

```sql
# 案例1：删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
```

**多表的删除【补充】**：

```sql
# sql92语法
DELETE 表1的别名[表2的别名]
FROM 表1 别名1, 表2 别名2
WHERE 连接条件
AND 筛选条件;

# sql99语法（★）
DELETE 表1的别名[表2的别名]
FROM 表1 别名
INNER | LEFT | RIGHT JOIN 表2 别名 ON 连接条件
WHERE 筛选条件;
```

**案例**：

```sql
# 案例2：删除张无忌的女朋友的信息
DELETE b[bo]
FROM beauty b
INNER JOIN boys bo ON b.'boyfriend_id' = bo.'id'
WHERE bo.'boyName' = '张无忌';
```

```sql
# 案例3：删除黄晓明的信息以及他女朋友的信息
DELETE b, bo
FROM beauty b
INNER JOIN boys bo ON b.'boyfriend_id' = bo.'id'
WHERE bo.'boyName' = '黄晓明';
```

### 3.3.2 TRUNCATE

**语法**：

```sql
TRUNCATE TABLE 表名;
```

**案例**：

```sql
# 案例1：将魅力值>100的男神信息删除
TRUNCATE TABLE boys; # 只能删除整张表，不能加WHERE条件、多表连接
```

### 3.3.3 两种方式比较（★）

|                    |                 **DELETE**                 |              **TRUNCATE**               |
| :----------------: | :----------------------------------------: | :-------------------------------------: |
|   **WHERE**条件    |                    支持                    |                 不支持                  |
|        效率        |                    率低                    |                   高                    |
| 删除表中有自增长列 | 删除后，再插入数据，自增长列的值从断点开始 | 删除后，再插入数据，自增长列的值从1开始 |
|       返回值       |                     有                     |                  没有                   |
|        回滚        |                    支持                    |                 不支持                  |



# 四 DDL语言

**含义**：数据定义语言。

**目的**：对数据库和数据表进行管理。

**分类**：

- 数据库的管理：创建、修改、删除；
- 表的管理：创建、修改、删除

**关键字**：

- 创建：`CREATE`
- 修改：`ALTER`
- 删除：`DROP`

## 4.1 数据库的管理

### 4.1.1 创建

**语法**：

```sql
CREATE DATABASE [IF NOT EXISTS] 库名;
```

**案例**：

```sql
# 案例1：创建数据库Books
CREATE DATABASE books;

# 加上判断
CREATE DATABASE IF NOT EXISTS books;
```

### 4.1.2 修改

**语法**：

```sql
# 更改库的字符集
ALTER DATABASE books CHARACTER SET gbk[字符集名称];
```

### 4.1.3 删除

**语法**：

```sql
DROP DATABASE [IF EXISTS] 库名;
```



## 4.2 数据表的管理

### 4.2.1 创建（★）

**语法**：

```sql
CREATE TABLE [IF NOT EXISTS] 表名 (
	列名 列的类型【（长度） 约束】,
    列名 列的类型【（长度） 约束】,
    列名 列的类型【（长度） 约束】,
    ...
    列名 列的类型【（长度） 约束】
)
```

**案例**：

```sql
# 案例1：创建Book表
CREATE TABLE book (
	id INT, # 编号
    bName VARCHAR(20), # 图书名
    price DOUBLE, # 价格
    authorId INT(20), # 作者编号
    publishDate DATETIME # 出版日期
);
```

```sql
# 案例2：创建author表
CREATE TABLE author(
    id INT,
    au_name VARCHAR(20),
    nation VARCHAR(10)
);
```

### 4.2.2 修改

- 修改列名；
- 修改列的类型或约束
- 添加新列
- 删除列
- 修改表名

**语法**：

```sql
# 修改列名
ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 类型;

# 修改列的类型或约束
ALTER TABLE 表名 MODIFY COLUMN 列名 新类型;

# 添加新列
ALTER TABLE 表名 ADD COLUMN 新列名 类型;

# 删除列
ALTER TABLE 表名 DROP COLUMN 列名;

# 修改表名
ALTER TABLE 表名 RENAME TO 新表名;
```

### 4.2.3 删除

**语法**：

```sql
DROP TABLE [IF EXISTS] 表名;
```



**通用写法**：

```sql
# 创建数据库
DROP DATABASE IF EXISTS 旧库名;
CREATE DATABASE 新库名;
```

```sql
# 创建数据表
DROP 表名 IF EXISTS 旧表名;
CREATE TABLE 表名 (
	......
);
```



### 4.2.4 复制

**复制表的结构**：

```sql
CREATE TABLE 新表名 LIKE 表名;
```

**复制表的结构+数据**：

```sql
CREATE TABLE 新表名
SELECT * FROM 表名;
```

**复制部分数据**：

```sql
CREATE TABLE 新表名
SELECT 字段1, 字段2
FROM 表名
WHERE 字段3 = 值;
```

**复制部分结构**：

```sql
CREATE TABLE 新表名
SELECT 字段1, 字段2
FROM 表名
WHERE 恒不成立条件(1=2);
```

## 4.3 数据类型

**分类**：

- 数值型
  - 整型
  - 小数
    - 定点数
    - 浮点数

- 字符型
  - 较短的文本：`CHAR`、`VARCHAR`
  - 较长的文本：`TEXT`、`BLOB`（较长的二进制数据）

- 日期型

### 4.3.1 整型

**分类**：

|     整数类型     | 字节 |                             范围                             |
| :--------------: | :--: | :----------------------------------------------------------: |
|   **Tinyint**    |  1   |              有符号：-128~127<br>无符号：0~255               |
|   **Smallint**   |  2   |           有符号：-32768~32767<br>无符号：0~65535            |
|  **Mediumint**   |  3   |        有符号：-8388608~8388607<br/>无符号：0~1677215        |
| **Int、integer** |  4   |   有符号：-2147483648~2147483647<br/>无符号：0~4294967295    |
|    **Bigint**    |  8   | 有符号：-9223372036854775808~9223372036854775807<br/>无符号：0~9223372036854775807*2+1 |

**特点**：

- 如果不设置无符号还是有符号，默认是有符号，如果想设置无符号，需要添加`UNSIGNED`关键字；

- 如果插入的数值超出了范围，会报`OUT OF RANGE`异常，并且插入临界值；

- 如果不设置长度，会有默认的长度

  长度代表了显示的最大宽度，如果不够，则用0在左边填充，但必须搭配`ZEROFILL`使用。



**案例**：

```sql
# 设置无符号和有符号
CREATE TABLE 表名 (
	列名 INT(长度) ZEROFILL,
    列名 INT(长度) UNSIGNED
);
```

### 4.3.2 浮点型

**分类**：

|         浮点数类型         |   字节   |   范围   |
| :------------------------: | :------: | :------: |
|        float(M, D)         |    4     |   很大   |
|        double(M, D)        |    8     |   很大   |
|       **定点数类型**       | **字节** | **范围** |
| DEC(M, D)<br>DECIMAL(M, D) |   M+2    |   很大   |

**特点**：

- M和D，M代表整数部位和小数部位的长度和，D代表小数部位的长度。如果超过范围，则插入临界值；

- M和D都可以省略，

  如果是`DECIMAL`，则M默认为10，D默认为0。

  如果是`FLOAT`和`DOUBLE`，则会根据插入的数值的精度来决定精度。

- 定点型的精确度较高，如果要求插入数值的精度较高，如货币运算等则考虑使用。

**选取原则**：

- 所选的类型越简单越好，能保存的数值的类型越小越好。

### 4.3.3 字符型

**分类**：

- char型和varchar类型：用来保存MySQL中较短的字符串。

| 字符串类型 | 最多字符数 |    描述及存储需求    |
| :--------: | :--------: | :------------------: |
|  char(M)   |     M      |  M为0~255之间的整数  |
| varchar(M) |     M      | M为0~65535之间的整数 |

- 较长的文本：text 和 blob(较大的二进制)
- binary和varbinary类型：类似于char和varchar，不同的是它们包含二进制字符串而不包含非二进制字符串。

- Enum类型：又称为枚举类型，要求插入的值必须属于列表中指定的值之一。

  如果列表成员为1~255，则需要1个字节存储；

  如果列表成员为255~65535，则需要2个字节存储；

  ```sql
  CREATE TABLE 表名 (
  	e ENUM('a', 'b', 'c')
  );
  # 注意：该表中，e字段插入的值只能为：a、b、c、A、B、C，不区分大小写。
  ```

- 位类型：

  | 位类型 | 字节 |     范围      |
  | :----: | :--: | :-----------: |
  | Bit(M) | 1~8  | Bit(1)~Bit(8) |

- Set类型：和Enum类型相似，里面可以保存0~64个成员。和Enum类型最大的区别是：SET类型一次可以选取多个成员，而Enum只能选取一个。

  | 成员数 | 字节数 |
  | :----: | :----: |
  |  1~8   |   1    |
  |  9~16  |   2    |
  | 17~24  |   3    |
  | 25~32  |   4    |
  | 33~64  |   8    |

  ```sql
  # 创建表
  CREATE TABLE 表名 (
  	s SET('a', 'b', 'c', 'd')
  );
  
  # 插入数据
  INSERT INTO 表名 VALUES('a');
  INSERT INTO 表名 VALUES('A,B');
  INSERT INTO 表名 VALUES('a,c,d');
  # 注意：不区分大小写
  ```

  

**特点**：

- char中M代表固定长度的字符，可省略，默认为1，比较耗费空间，效率较高；
- varchar中M代表可变长度的字符，不可省略，比较节省空间，效率较低；

### 4.3.4 日期型

**分类**：

| 日期和时间类型 | 字节 |       最小值        |       最大值        |
| :------------: | :--: | :-----------------: | :-----------------: |
|      date      |  4   |     1000-01-01      |     9999-12-31      |
|    datetime    |  8   | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 |
|   timestamp    |  4   |   19700101080001    |  2038年的某个时刻   |
|      time      |  3   |     -838:59:59      |      838:59:59      |
|      year      |  1   |        1901         |        2155         |

**特点**：

- Timestamp支持的时间范围较小，取值范围：19700101080001~2038年的某个时间；

  Datetime的取值范围：1000-1-1~9999-12-31；

- timestamp和实际时区有关，更能反映实际的日期，而datetime则只能反映出插入时的当地时区；
- timestamp的属性受MySQL版本和SQLMode的影响很大；

```sql
# 创建表
CREATE TABLE 表名 (
	t1 DATETIME,
    t2 TIMESTAMP
);

# 插入值
INSERT INTO 表名 VALUES(NOW(), NOW());

# 修改系统时区
SET time_zone = '+9:00';
```

## 4.4 常见约束

**含义**：一种限制，用于限制表中的数据，为了保证表中数据的准确性和可靠性；

**分类**：六大约束

- **NOT NULL**：非空，用于保证该字段的值不能为空；比如：姓名、学号等。
- **DEFAULT**：默认，用于保证该字段的值有默认值；比如：性别。
- **PRIMARY KEY**：主键，用于保证该字段的值具有唯一性，并且非空；比如：学号、员工编号等。
- **UNIQUE**：唯一，用于保证该字段的值具有唯一性，但可以为空；比如：座位号等。
- **CHECK**：检查约束【MySQL中不支持】；比如：年龄、性别等。
- **FOREIGN KEY**：外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值。在从表添加外键约束，用于引用主表中某列的值。比如：学生表的专业编号，员工表的部门编号，员工表的工种编号。

**添加约束的时机**：

- 创建表时；
- 修改表时；

**约束的添加分类**：

- 列级约束：
  - 六大约束语法上都支持，但外键约束没有效果；
- 表级约束：
  - 除了非空`NOT NULL`、默认`DEFAULT`，其他都支持。

**语法**：

```sql
CREATE TABLE 表名 (
	字段名 字段类型 列级约束,
    字段名 字段类型,
    表级约束
);
```

### 4.4.1 列级约束

**语法**：

直接在字段名和类型后面追加 约束类型即可。

只支持：默认、非空、主键、唯一。

**案例**：

```sql
# 学生信息表
CREATE TABLE stuinfo (
	id INT PRIMARY KEY, # 主键
    stuName VARCHAR(20) NOT NULL, # 非空
    gender CHAR(1) CHECK(gender='男', OR gender = '女'), # 检查
    seat INT UNIQUE, # 唯一
    age INT DEFAULT 18, # 默认约束
    majorId INT FOREIGN KEY REFERENCES major(id) # 外键
);

CREATE TABLE major(
	id INT PRIMARY KEY,
    majorName VARCHAR(20)
);
```

### 4.4.2 表级约束

**语法**：在各个字段的最下面

```sql
【CONSTRAINT 约束名】 约束类型(字段名);
```

**案例**：

```sql
CREATE TABLE stuinfo (
	id INT,
    stuname VARCHAR(20),
    gender CHAR(1),
    seatc INT,
    age INT,
    majorid INT,
    
    CONSTRAINT pk PRIMARY KEY(id), # 主键
    CONSTRAINT uq UNIQUE(seat), # 唯一键
    CONSTRAINT ck CHECK(gender = '男' OR gender = '女'), # 检查
    CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id) # 外键
);
```

```sql
CREATE TABLE stuinfo (
	id INT,
    stuname VARCHAR(20),
    gender CHAR(1),
    seatc INT,
    age INT,
    majorid INT,
    
    PRIMARY KEY(id), # 主键
    UNIQUE(seat), # 唯一键
    CHECK(gender = '男' OR gender = '女'), # 检查
    FOREIGN KEY(majorid) REFERENCES major(id) # 外键
);
```

**通用的写法**：

```sql
CREATE TABLE IF NOT EXISTS stuinfo (
	id INT PRIMARY KEY,
    stuname VARCHAR(20) NOT NULL,
    sex CHAR(1),
    age INT DEFAULT 18,
    seat INT UNIQUE,
    majorid INT,
    CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
    
);
```

### 4.4.3 主键和唯一

|          | **唯一性** |  NULL  |   数量   |  是否允许组合  |
| :------: | :--------: | :----: | :------: | :------------: |
| **主键** |    保证    | 不允许 | 至多一个 | 允许，但不推荐 |
| **唯一** |    保证    |  允许  | 可以多个 | 允许，但不推荐 |

- 是否允许组合：是指两个列组成一个主键，或两个列组成一个唯一键。

```sql
CREATE TABLE stuinfo (
	id INT,
    stuname VARCHAR(20),
    gender CHAR(1),
    seat INT,
    age INT,
    majorid INT,
    seat2 INT,
    
    PRIMARY KEY(id, stuname), # 两个列组成一个主键
    UNIQUE(seat, seat2), # 两个列组成一个唯一键
    CHECK(gender = '男' OR gender = '女'), # 检查
    FOREIGN KEY(majorid) REFERENCES major(id) # 外键
);
```

### 4.4.4 外键的特点

- 要求在从表设置外键关系；

- 从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求；

- 主表的关联列必须是一个key（一般是主键或唯一键）；

- 插入数据时，先插入主表，再插入从表；

  删除数据时，先删除从表，再删除主表；

### 4.4.5 修改表时添加约束

```sql
# 原始表
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo (
	id INT,
    stuname VARCHAR(20),
    gender CHAR(1),
    seat INT,
    age INT,
    majorid INT
);
```

**1. 添加非空约束**

```sql
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NOT NULL;
```

**2. 添加默认约束**

```sql
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
```

**3. 添加主键**

```sql
# 1.列级约束
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
# 2.表级约束
ALTER TABLE stuinfo ADD PRIMARY KEY(id);
```

**4. 唯一键**

```sql
# 1.列级约束
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
# 2.表级约束
ALTER TABLE stuinfo ADD UNIQUE(seat);
```

**5. 外键**

```sql
ALTER TABLE stuinfo ADD [CONSTRAINT fk_stuinfo_major] FOREIGN KEY(majorid) REFERENCES major(id);
```



**特点**：

- 添加列级约束

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 新约束;
  ```

- 添加表级约束

  ```sql
  ALTER TABLE 表名 ADD [CONSTRAINT 约束名] 约束类型(字段名) [外键的引用];
  ```



### 4.4.6 修改表时删除约束

**1. 删除非空约束**

```sql
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
```

**2. 删除默认约束**

```sql
ALTER TABLE stuinfo MODIFY COLUMN age INT;
```

**3. 删除主键**

```sql
ALTER TABLE stuinfo MODIFY COLUMN id INT;
ALTER TABLE stuinfo DROP PRIMARY KEY;
```

**4. 删除唯一键**

```sql
ALTER TABLE stuinfo DROP INDEX seat;
# 查看
SHOW INDEX FROM stuinfo;
```

**5. 删除外键**

```sql
ALTER TABLE stuinfo DROP FOREIGN KEY majorid;
```



## 4.5 标识列

又称为**自增长列**；

**含义**：可以不用手动的插入值，系统提供默认的序列值。

**语法**：

```sql
CREATE TABLE tab_identity (
	id INT PRIMARY KEY [AUTO_INCREMENT],
    NAME VARCHAR(20)
);

# 设置增长步长
SET auto_increment_increment = 3;
```

**特点**：

- **标识列**必须和**主键、唯一键**或者**外键**搭配；
- 一个表中只能有一个**标识列**；
- 标识列的类型只能是数值型；
- 标识列可以通过 `SET auto_increment_increment = 3;` 设置步长，也可以通过手动插入值，设置起始值。

**修改表时设置标识列**：

```sql
ALTER TABLE tab_identity MODIFY COLUMN id INT PRIMARY KEY AUTO_INCREMENT;
```

**修改表时删除标识列**：

```sql
ALTER TABLE tab_identity MODIFY COLUMN id INT;
```



# 五 事务

## 5.1 TCL语言

### 5.1.1 事务概述

TCL（Transaction Control Language）事务控制语言。

**事务**：**事务由单独单元的一个或多个SQL语句组成,在这个单元中，每个MySQL语句是相互依赖的**。而整个单独单元作为一个不可分割的整体，如果单元中某条SQL语句一旦执行失败或产生错误，整个单元将会**回滚**。所有受到影响的数据将返回到事物开始以前的状态；如果单元中的所有SQL语句均执行成功，则事物被顺利执行。

### 5.1.2 存储引擎

**存储引擎**（了解）：

- 概念：在mysql中的数据用各种不同的技术存储在文件（或内存）中。
- 通过`SHOW ENGINES;`来查看mysql支持的存储引擎。
- 在mysql中用的最多的存储引擎有：innodb、myisam、memory等。其中innodb支持事务，而myisam、memory等不支持事务。

**案例**：

​		整个转账过程应该包含两个更新操作，但由于意外，转账人的更新成功，而收款人的更新没成功，这显然是不行的。**事务就是用于解决这种情况的**。

```sql
# 转账
# 张三丰	1000
# 郭襄	1000

UPDATE 表 SET 张三丰的余额 = 500 WHERE NAME = '张三丰';
[意外]
UPDATE 表 SET 郭襄的余额 = 1500 WHERE NAME = '郭襄';
```

### 5.1.3 事务的特点

- **原子性**（Atomicity）

  原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。

- **一致性**（Consistency）

  事务必须使数据库从一个**一致性**状态变换到另外一个**一致性**状态。

- **隔离性**（Isolation）

  事务的隔离性是指一个事务的执行不能被其他的事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

- **持节性**（Durability）

  持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响。

### 5.1.4 事务的使用步骤

- 隐式事务：事务没有明显的开启和结束的标记；比如：`INSERT`、`UPDATE`、`DALETE`语句。

  ```sql
  DELETE FROM 表 WHERE id = 1;
  ```

- 显示事务：事务具有明显的开启和结束的标记；

  前提：必须先设置自动提交功能为禁用。

  ```sql
  # 禁用自动提交功能
  SET AUTOCOMMIT = 0;
  
  开启事务的语句;
  UPDATE 表 SET 张三丰的余额 = 500 WHERE NAME = '张三丰';
  UPDATE 表 SET 郭襄的余额 = 1500 WHERE NAME = '郭襄';
  结束事务的语句;
  ```

**步骤**：

```sql
# STEP1：开启事务
SET AUTOCOMMIT = 0;
START TRANSACTION; # 可选的

# STEP2：编写事务中的sql语句（SELECT、INSERT、UPDATE、DELETE）
语句1;
语句2;
......

# STEP3：结束事务
COMMIT; # 提交事务
ROLLBACK; # 回滚事务
```

**案例**：

```sql
# 案例：转账
# STEP1：开启事务
SET AUTOCOMMIT = 0;
START TRANSACTION;
# STEP2：编写一组事务的语句
UPDATE account SET balance = 500 WHERE username = '张无忌';
UPDATE account SET balance = 1500 WHERE username = '赵敏';
# STEP3：结束事务
COMMIT(); # 或者：ROLLBACK(); 要么提交，要么回滚。
```



## 5.2 事务并发问题

### 5.2.1 问题分类

对于同时运行的多个事务，当这些事务访问**数据库中相同的数据**时，如果没有采取必要的**隔离机制**，就会导致各种并发问题：

- **脏读**：对于两个事务T1、T2，T1读取了已经被T2更新但还**没有被提交**的字段之后，若T2回滚，T1读取的内容就是临时且无效的。

- **不可重复读**：对于两个事务T1、T2，T1读取了一个字段，然后T2**更新**了该字段之后，T1再次读取同一个字段，值就不同了。

- **幻读**：对于两个事务T1、T2，T1从一个表中读取了一个字段，然后T2在该表中**插入**了一些新的行之后，如果T1再次读取同一个表，就会多出几行。

### 5.2.2 隔离级别

**数据库事务的隔离性**：数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响，避免各种并发问题。

**一个事务与其他事务隔离的程度称为隔离级别**。数据库规定了多种事务隔离级别，不同隔离级别对应不同的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱。

数据库提供的4种事务隔离级别：

|               隔离级别               |                             描述                             |
| :----------------------------------: | :----------------------------------------------------------: |
| READ UNCOMMITTED<br>（读未提交数据） | 允许事务读取未被其他事务提交的变更、脏读、不可重复读和幻读的问题都会出现 |
|  READ COMMITED<br>（读已提交数据）   | 只允许事务读取已经被其他事务提交的变更，可以避免脏读，但不可重复读和幻读问题仍然可能出现 |
|   REPEATABLE READ<br>（可重复读）    | 确保事务可以多次从一个字段中读取相同的值，在这个事务持续期间，禁止其他事务<br>对这个字段进行更新，可以避免脏读和不可重复读，但幻读的问题仍然存在。 |
|      SERIALIZABLE<br>（串行化）      | 确保事务可以从一个表中读取相同的行，在这个事务持续期间，禁止其他事务对该表执行插<br/>入，更新和删除操作，所有并发问题都可以避免，但性能十分低下。 |

Oracle支持的2种事务隔离级别：**READ COMMITED**，SERIALIZABLE。Oracle默认的事务隔离级别为：READ COMMITED。

Mysql支持4种事务隔离级别，Mysql默认的事务隔离级别为：REPEATABLE READ。



### 5.2.3 演示事务的隔离级别

- 每启动一个MySQL程序，就会获得一个单独的数据库连接，每个数据库连接都有一个全局变量**@@tx_isolation**，表示当前的事务隔离级别。
- 查看当前的隔离界别：`SELECT @@tx_isolation;`
- 设置当前MySQL连接的隔离级别：
  - `SET TRANSACTION ISOLATION LEVEL [READ COMMITTED];`

- 设置数据库系统的全局的隔离级别：
  - `SET GLOBAL TRANSACTION ISOLATION LEVEL [READ COMMITTED];`



### 5.2.4 回滚点的演示

**语法**：

```sql
SAVEPOINT 节点名; # 设置保存点
```

**使用实例**：

```sql
SET AUTOCOMMIT = 0;
START TRANSACTION;
DELETE FROM account WHERE id = 25;
SAVEPOINT a; # 设置保存点
DELETE FROM account WHERE id = 28;
ROLLBACK TO a; # 回滚到保存点
```



# 六 视图

## 6.1 概述

**视图**：MySQL从5.0.1版本开始提供视图功能。一种虚拟存在的表，行和列的数据来自定义试图的查询种使用的表，并且是在使用视图时**动态生成的，只保存了sql逻辑，不保存查询结果**。

**应用场景**：

- 多个地方用到同样的查询结果；
- 该查询结果使用的sql语句较复杂；

**语法**：

```sql
# 创建视图
CREATE VIEW 视图名
AS
SELECT 字段1, 字段2
FROM 表1 t1
INNER JOIN 表2 t2
ON t1.id = t2.id
WHERE t1.id = 1;

# 使用视图
SELECT * FROM 视图名 [筛选条件]
```

**举例**：舞蹈班和普通班级的对比。

**优点**：

- 重用sql语句；
- 简化复杂的sql操作，不必知道它的查询细节；
- 保护数据，提高安全性；



## 6.2 视图的创建

**语法**：

```sql
CREATE VIEW 视图名
AS
查询语句;
```

**案例**：

```sql
# 案例1：查询姓名中包含a字符的员工名、部门名和工种信息
# STEP1：创建视图，查看员工名、部门名和工种信息
CREATE VIEW myv1
AS
SELECT last_name, department_name, job_title
FROM employees e
JOIN departments d ON e.department_id = d.department_id
JOIN jobs j ON j.job_id = e.job_id;

# STEP2：从视图中查询满足邮箱包含a字符的员工信息
SELECT * FROM myv1 WHERE last_name LIKE '%a%';
```

```sql
# 案例2：查询各部门的平均工资级别
# STEP1：创建视图，查看每个部门的平均工资
CREATE VIEW myv2
AS
SELECT AVG(salary) ag, department_id
FROM employees
GROUP BY department_id;

# STEP2：使用视图，连接工资等级表
SELECT myv2.'ag', g.grade_level
FROM myv2
JOIN job_grades g
ON myv2.'ag' BETWEEN g.'lowest_sal' AND g.'highest_sal';
```

```sql
# 案例3：查询平均工资最低的部门信息
# STEP1：创建视图，查询每个部门的平均工资
CREATE VIEW myv3
AS
SELECT AVG(salary) ag, department_id
FROM employees
GROUP BY department_id;

# STEP2：使用视图，查找平均工资最低的部门信息
SELECT * FROM myv3 ORDER BY ag LIMIT 1;
```

```sql
# 案例4：查询平均工资最低的部门名和工资
# STEP1：创建视图，查询平均工资最低的部门信息
CREATE VIEW myv4
AS
SELECT * FROM myv3 ORDER BY ag LIMIT 1;

# STEP3：使用视图
SELECT d.*, m.ag
FROM myv4 m
JOIN departments d
ON m.'department_id' = d.'department_id';
```



## 6.3 视图的修改

**方式一**：

```sql
CREATE OR REPLACE VIEW 视图名
AS
查询语句;
```

**方式二**：

```sql
ALTER VIEW 视图名
AS
查询语句;
```



## 6.4 视图的查看和删除

**删除视图**：

```sql
DROP VIEW 视图名, 视图名, ...;
```

**查看视图**：

```sqL
DESC 视图名;
```



## 6.5 视图的更新

**更新**：修改视图中的数据，而不是修改视图的逻辑。

```sql
# 创建视图
CREATE OR REPLACE VIEW myv1
AS
SELECT last_name, email
FROM employees;
```

**插入数据**：

```sql
INSERT INTO myv1 VALUES('张飞', 'zf@qq.com');
```

**修改数据**：

```sql
UPDATE myv1 SET last_name = '张无忌' WHERE last_name = '张飞';
```

**删除数据**：

```sql
DELETE FROM myv1 WHERE last_name = '张无忌';
```

**注意事项**：

视图的可更新性和视图中查询的定义有关系，以下类型的视图是不能更新的。

- 包含以下关键字的sql语句：分组函数、DISTINCT、GROUP BY、HAVING、UNION 或者 UNION ALL；
- 常量视图；
- SELECT中包含子查询；
- JOIN；
- FROM一个不能更新的视图；
- WHERE子句的子查询引用了FROM子句中的表；



## 6.6 视图和表的对比

|      |     创建     |    占用物理空间    |          使用          |
| :--: | :----------: | :----------------: | :--------------------: |
| 视图 | CREATE VIEW  | 一点儿（保存逻辑） | 增删改查，一般不增删改 |
|  表  | CREATE TABLE |  占用（保存数据）  |        增删改查        |



## 6.7 DELETE和TRUNCATE在事务中的区别

### 6.7.1 DELETE

**支持回滚**

**演示**：

```sql
SET AUTOCOMMIT = 0;
START TRANSACTION;
DELETE FROM account;
ROLLBACK;
```

### 6.7.2 TRUNCATE

**不支持回滚**

**演示**：

```sql
SET AUTOCOMMIT = 0;
START TRANSACTION;
TRUNCATE TABLE account;
ROLLBACK;
```



# 七 变量

## 7.1 系统变量

**系统变量**：变量由系统提供，不是用户定义，属于服务器层面。

**使用语法**：

```sql
# 1.查看所有的系统变量
SHOW GLOBAL VARIABLES; # 查看全局变量
SHOW SESSION VARIABLES; # 查看会话变量

# 2.查看满足条件的部分系统变量
SHOW GLOBAL|[SESSION] VARIABLES LIKE '%char%';

# 3.查看指定的某个系统变量的值
SELECT @@系统变量名;
SELECT @@global|[session].系统变量名;

# 4.为某个系统变量赋值
# 方式一：
SET GLOBAL|[SESSION] 系统变量名 = 值;
# 方式二：
SET GLOBAL|[SESSION].系统变量名 = 值;
```

**注意**：

如果是全局级别，则需要加GLOBAL，如果是会话级别，则需要加SESSION，如果不写，则默认SESSION。

### 7.1.1 全局变量

```sql
# 1.查看所有的全局变量
SHOW GLOBAL VARIABLES;

# 2.查看部分的全局变量
SHOW GLOBAL VARIABLES LIKE '@char@';

# 3.查看指定的全局变量的值
SELECT @@global.autocommit;
SELECT @@tx_isolation;

# 4.为某个指定的全局变量赋值
SET @@global.autocommit = 0;
```

**作用域**：服务器每次启动将为所有的全局变量赋初始值，针对于所有的会话（连接）有效，但不能跨重启。

### 7.1.2 会话变量

```sql
# 1.查看所有的会话变量
SHOW [SESSION] VARIABLES;

# 2.查看部分的会话变量
SHOW [SESSION] VARIABLES LIKE '%char%';

# 3.查看指定的某个会话变量
SELECT @@[session.]tx_isolation;

# 4.为某个会话变量赋值
# 方式一：
SET @@session.tx_isolation = 'read-uncommitted';

# 方式二：
SET session tx_isolation = 'read-committed';
```

**作用域**：仅仅针对于当前会话（连接）有效。



## 7.2 自定义变量

**自定义变量**：变量是用户自定义的，不是由系统提供的。

**使用步骤**：

- 声明；
- 赋值；
- 使用（查看、比较、运算等）；

### 7.2.1 用户变量

```sql
# 1.声明并初始化
SET @用户变量名 = 值;
SET @用户变量名 := 值;
SELECT @用户变量名 := 值;
# 赋值的操作符：	= 或 :=

# 2.赋值（更新用户变量的值）
# 方式一：通过 SET 或 SELECT
SET @用户变量名 = 新值;
SET @用户变量名 := 新值;
SELECT @用户变量名 := 新值;
# 方式二：通过 SELECT INTO
SELECT 字段 INTO 变量名 FROM 表;

# 3.使用（查看用户变量的值）
SELECT @用户变量名;
```

**作用域**：针对于当前会话（连接）有效，同于会话变量的作用域。应用在任何地方，也就是`BEGIN END`里面或`BEGIN END`外面。

### 7.2.2 局部变量

```sql
# 1.声明
DECLARE 变量名 类型;
DECLARE 变量名 类型 DEFAULT 值;

# 2.赋值
# 方式一：通过 SET 或 SELECT
SET 局部变量名 = 新值;
SET 局部变量名 := 新值;
SELECT @局部变量名 := 新值;
# 方式二：通过 SELECT INTO
SELECT 字段 INTO 局部变量名 FROM 表;

# 3.使用
SELECT 局部变量名;
```

**作用域**：仅仅在定义它的`BEGIN END`中有效，而且必须是`BEGIN END`中的第一句话。

## 7.3 对比

|              |   **作用域**    |          定义或使用的位置           |              语法               |
| :----------: | :-------------: | :---------------------------------: | :-----------------------------: |
| **用户变量** |    当前会话     |          会话中的任何地方           |  必须加**@**符号，不用限定类型  |
| **局部变量** | **BEGIN END**中 | 只能在**BEGIN END**中，且为第一句话 | 一般不加**@**符号，需要限定类型 |

## 7.4 案例

```sql
# 案例：声明两个变量并赋初始值，求和，并打印。
# 1.用户变量
SET @m = 1;
SET @n = 2;
SET @sum = @m + @n;
SELECT @sum;

# 2.局部变量
DECLARE m INT DEFAULT 1;
DECLARE n INT DEFAULT 2;
DECLARE SUM INT;
SET = m + n;
SELECT SUM;
```



# 八 存储过程和函数

**存储过程和函数**：类似于JAVA中的方法。

**优点**：

- 提高代码的重用性；
- 简化操作；

## 8.1 存储过程

### 8.1.1 介绍

**含义**：一组预先编译好的SQL语句的集合，理解成批处理语句。

**优点**：

- 提高代码的重用性；
- 简化操作；
- 减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率。

### 8.1.2 语法

**一、创建语法**

```sql
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
	存储过程体（一组合法的SQL语句）
END
```

**注意**：

- 参数列表包含三部分

  - 参数模式
  - 参数名
  - 参数类型

  ```sql
  IN stuname VARCHAR(20);
  ```

- 参数模式：
  - **IN**：该参数可以作为输入，也就是该参数需要调用方传入值。
  - **OUT**：该参数可以作为输出，也就是该参数可以作为返回值。
  - **INOUT**：该参数即可以作为输入又可以作为输出，也就是该参数即需要传入值，又可以返回值。

- 如果存储过程体仅仅只有一句话，`BEGIN END`可以省略。

  存储过程体中的每条SQL语句的结尾要求必须加分号。

  存储过程的结尾可以使用`DELIMITER`重新设置。

  **语法**：

  ```sql
  DELIMITER 结束标记
  # 案例
  DELIMITER $
  ```



**二、调用语法**

```sql
CALL 存储过程名(实参列表);
```



### 8.1.3 空参的存储过程

```sql
# 案例1：插入到admin表中五条记录
DELIMITER $
CREATE PROCEDURE myp1()
BEGIN
	INSERT INTO admin(username, 'password') 
	VALUES('john1', '0000'),
	('lily', '0000'),
	('rose', '0000'),
	('jack', '0000'),
	('tom', '0000');
END $
# 调用
CALL myp1()$
```

### 8.1.4 带IN模式的存储过程

```sql
# 案例1：创建存储过程实现，根据女神名，查询对应的男神信息
CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
	SELECT bo.*
	FROM boys bo
	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name = beautyName;
END $
# 调用
CALL myp2('柳岩')$
```

```sql
# 案例2：创建存储过程实现，用户是否登录成功
CREATE PROCEDURE myp3(IN username VARCHAR(20), IN password VARCHAR(20))
BEGIN
	DECLARE result INT DEFAULT 0; # 声明并初始化
	SELECT COUNT(*) INTO result # 赋值
	FROM admin 
	WHERE admin.username = username
	AND admin.password = password;
	SELECT IF(result > 0, '成功', '失败'); # 使用，查看
END $
# 调用
CALL myp3('张飞', '8888')$
```

### 8.1.5 带OUT模式的存储过程

```sql
# 案例1：根据女神名，返回对应的男神名
CREATE PROCEDURE myp5(IN beautyName VARCHAR(20), OUT boyName VARCHAR(20))
BEGIN
	SELECT bo.boyName INTO boyName
	FROM boys bo
	INNER JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name = beautyName;
END $
# 调用
SET @bName$
CALL myp5('小昭', @bName)$
SELECT @bName$
```

```sql
# 案例2：根据女神名，返回对应的男神名和男神魅力值
CREATE PROCEDURE myp6(IN beautyName VARCHAR(20), OUT boyName VARCHAR(20), OUT userCP INT)
BEGIN
	SELECT bo.boyName, bo.userCP INTO boyName, userCP
	FROM boys bo
	INNER JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name = beautyName;
END $
# 调用
CALL myp6('小昭', @bName, @usercp)$
```

### 8.1.6 带INOUT模式的存储过程

```sql
# 案例1：传入a和b两个值，最终a和b都翻倍并返回
CREATE PROCEDURE myp8(INOUT a INT, INOUT b INT)
BEGIN
	SET a = a * 2;
	SET b = b * 2;
END $
# 调用
SET @m = 10$
SET @n = 20$
CALL myp8(@m, @n)$
```

### 8.1.7 存储过程的删除

**语法**：

```sql
DROP PROCEDURE 存储过程名
```

### 8.1.8 存储过程的查看

```sql
SHOW CREATE PROCEDURE 存储过程名;
```



## 8.2 函数

### 8.2.1 介绍

**含义**：一组预先编译好的SQL语句的集合，理解成批处理语句。

**优点**：

- 提高代码的重用性；
- 简化操作；
- 减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率。

**与存储过程的区别**：

- **存储过程**：可以有0个返回，也可以有多个返回，适合做**批量插入**、**批量更新**。
- **函数**：有且仅有1个返回，适合做**处理数据后返回一个结果**。

### 8.2.2 创建语法

```sql
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
	函数体
END
```

**注意**：

- 参数列表包含两部分：参数名、参数类型

- 函数体：肯定会有`return`语句，如果没有会报错。

  如果`return`语句没有放在函数体的最后也不会报错，但不建议。

- 函数体中仅有一句话，则可以省略`BEGIN END`。
- 使用`DELIMITER`语句设置结束标记。

### 8.2.3 调用语法

```sql
SELECT 函数名(参数列表)
```

### 8.2.4 案例演示

**1.无参有返回**

```sql
# 案例1：返回公司的员工个数
CREATE FUNCTION myf1() RETURNS INT
BEGIN
	DECLARE c INT DEFAULT 0; # 定义变量
	SELECT COOUNT(*) INTO c # 赋值
	FROM employees;
	RETURN c;
END
# 调用
SELECT myf1()$
```

**2.有参有返回**

```sql
# 案例2：根据员工名，返回它的工资
CREATE FUNCTION myf2(empName, VARCHAR(20)) RETURN DOUBLE
BEGIN
	SET @sal = 0; # 定义用户变量
	SELECT salary INTO @sal # 赋值
	FROM employees
	WHERE last_name = empName;
	RETURN @sal;
END $
# 调用
SELECT myf2('k_ing') $
```

```sql
# 案例3：根据部门名，返回该部门的平均工资
CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURN DOUBLE
BEGIN
	DECLARE sal DOUBLE;
	SELECT AVG(salary) INTO sal
	FROM employees e
	JOIN departments d ON e.department_id = d.department_id
	WHERE d.department_name = deptName;
	RETURN sal;
END $
# 调用
SELECT myf3('IT') $
```

### 8.2.5 函数的查看

**语法**：

```sql
SHOW CREATE FUNCTION 函数名;
```

### 8.2.6 函数的删除

**语法**：

```sql
DROP FUNCTION 函数名;
```



# 九 流程控制结构

## 9.1 介绍

- 顺序结构：程序从上往下依次执行；
- 分支结构：程序从两条或多条路径中选择一条去执行；
- 循环结构：程序在满足一定条件的基础上，重复执行一段代码；

## 9.2 分支结构

### 9.2.1 IF函数

**功能**：实现简单的双分支

**语法**：

```sql
IF(表达式1, 表达式2, 表达式3)
```

**顺序执行**：

如果表达式1成立，则IF函数返回表达式2的值，否则返回表达式3的值。

**应用**：任何地方。

### 9.2.2  CASE结构

**情况1**：类似于Java中的**switch**语句，一般用于实现等值判断。

**语法**：

```sql
CASE 变量|表达式|字段
WHEN 要判断的值 THEN 返回的值1[语句1;]
WHEN 要判断的值 THEN 返回的值2[语句2;]
......
ELSE 要返回的值n[语句n;]
END [CASE];
```

**情况2**：类似于Java中的多重**IF**语句，一般用于实现区间判断。

**语法**：

```sql
CASE
WHEN 要判断的条件1 THEN 返回的值1[语句1;]
WHEN 要判断的条件2 THEN 返回的值2[语句2;]
......
ELSE 要返回的值n[语句n;]
END [CASE];
```

**特点**：

- 可以作为表达式，嵌套在其他语句中使用，可以放在任何地方，`BEGIN END`中或外面都可。

  可以作为独立的语句去使用，只能放在`BEGIN END`中。

- 如果`WHEN`中的值满足或条件成立，则执行对应的`THEN`后面的语句，并且结束`CASE`。

  如果都不满足，则执行`ELSE`中的语句或值。

- `ELSE`可以省略，如果`ELSE`省略了，并且所有`WHEN`条件都不满足，则返回`NULL`

**案例**：

```sql
# 案例1：创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100，显示A，80-90，显示B，60-80，显示C，否则，显示D
CREATE PROCEDURE test_case(IN score INT)
BEGIN
	CASE
	WHEN score >= 90 AND score <= 100 THEN SELECT 'A';
	WHEN score >= 80 THEN SELECT 'B';
	WHEN score >= 60 THEN SELECT 'C';
	ELSE SELECT 'D';
END $
# 调用
CALL test_case(95)$
```

### 9.2.3 IF结构

**功能**：实现多重分支。

**语法**：

```sql
IF 条件1 THEN 语句1;
ELSEIF 条件2 THEN 语句2;
......
[ELSE 语句n;]
END IF;
```

**应用场景**：应用在`BEGIN END`中。

**案例**：

```sql
# 案例1：根据传入的成绩，来显示等级，比如传入的成绩：90-100，返回A，80-90，返回B，60-80，返回C，否则，返回D
CREATE FUNCTION test_if(score INT) RETURN CHAR
BEGIN
	IF score >= 90 AND score <= 100 THEN RETURN 'A';
	ELSEIF score >= 80 THEN RETURN 'B';
	ELSEIF socre >= 60 THEN RETURN 'C';
	ELSE RETURN 'D';
	END IF;
END $
```



## 9.3 循环结构

**分类**：

- **WHILE**：先判断后执行。
- **LOOP**：一般用于实现简单的死循环。
- **REPEAT**：先执行后判断。

**循环控制**：

- **ITERATE**：类似于`CONTINUE`，继续，结束本次循环，继续下一次；
- **LEAVE**：类似于`BREAK`，跳出，结束当前所在的循环

**案例**：

```sql
# 案例1：批量插入，根据次数插入到admin表中多条记录（没有添加循环控制语句）
CREATE PROCEDURE pro_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	WHILE i <= insertCount DO
		INSERT INTO admin(username, 'password') VALUES (CONCAT('Rose', i), '666');
		SET i = i + 1;
	END WHILE;
END $
# 调用
CALL pro_while1(100)
```

```sql
# 案例2：批量插入，根据次数插入到admin表中多条记录，如果次数大于20，则停止（添加循环控制语句LEAVE）
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	a:WHILE 1 <= insertCount DO
		INSERT INTO admin(username, 'password') VALUES (CONCAT('xiaohua', i), '0000');
		IF i >= 20 THEN LEAVE a;
		END IF;
		SET i = i + 1;
	END WHILE a;
END $
# 调用
CALL test_while1(100)$
```

```sql
# 案例3：批量插入，根据次数插入到admin表中多条记录，只插入偶数次（添加循环控制语句ITERATE）
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	a:WHILE 1 <= insertCount DO
		SET i = i + 1;
		IF MOD(i, 2) != 0 THEN ITERATE a;
		END IF;
		INSERT INTO admin(username, 'password') VALUES (CONCAT('xiaohua', i), '0000');
	END WHILE a;
END $
# 调用
CALL test_while1(100)$
```



### 9.3.1 WHILE

**语法**：

```sql
[标签:] WHILE 循环条件 DO
	循环体;
END WHILE [标签];
```



### 9.3.2 LOOP

**语法**：

```sql
[标签:] LOOP
	循环体;
END LOOP [标签];
```



### 9.3.3 REPEAT

**语法**：

```sql
[标签:] REPEAT
	循环体;
UNTIL 结束循环的条件
END REPEAT [标签];
```

