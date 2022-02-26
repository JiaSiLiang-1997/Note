# 一 介绍

JavaScript语言诞生主要是完成页面的数据验证。因此它运行在客户端，需要运行浏览器来解析执行JavaScript 代码。JS是Netscape网景公司的产品，最早取名为Live Script ;为了吸引更多Java程序员。更名为JavaScript。**JS是弱类型，Java 是强类型。**

- 弱类型就是类型可变；
- 强类型，就是定义变量的时候。类型已确定，而且不可变。

**特点**：

1. 交互性（它可以做就是信息的动态交互）
2. 安全性（不允许直接访问本地硬盘）
3. 跨平台性（只要是可以解释JS的游览器都可以执行，和平台无关）



# 二 JavaScript和HTML代码的结合方式

## 2.1 第一种方式

只需要在head标签中，或者body标签中，使用script标签来书写JavaScript代码。

## 2.2 第二种方式

使用script标签引入单独的JavaScript代码文件。



# 三 变量

什么是变量？变量是可以存放某些值的内存的命名。

## 3.1 JS的变量类型

|    类型    |  关键字  |
| :--------: | :------: |
|  数值类型  |  number  |
| 字符串类型 |  string  |
|  对象类型  |  object  |
|  布尔类型  | boolean  |
|  函数类型  | function |



## 3.2 JS里特殊的值

- undefined：未定义，所有JS变量未赋予初始值的时候，默认值都是undefined；
- null：空值；
- NAN：全称是 Not a Number，非数字，非数值。



## 3.3 JS中定义变量的格式

```javascript
var 变量名;
var 变量名 = 值;
```



## 3.4 关系（比较）运算

- 等于：==    		简单的做字面值的比较；
- 全等于：===      除了做字面的值的比较之外，还会比较两个变量的数据类型。



## 3.5 逻辑运算

- 且运算：&&
- 或运算：||
- 取反运算：！

​	在JavaScript语言中，所有的变量，都可以作为一个`boolean`类型的变量去使用。

**注意**：0、null、undefined、""（空串）都认为是false；

### 3.5.1 &&与运算

有两种情况：

- 当表达式全为真的时候，返回最后一个表达式的值。
- 当表达式中，有一个为假的时候，返回第一个为假的表达式的值。

### 3.5.2 ||或运算

有两种情况：

- 当表达式全为假时，返回最后一个表达式的值；
- 只要有一个表达式的值为真，就会返回第一个为真的表达式的值；



## 4 数组

## 4.1 定义方式

JS中数组的定义格式：

```javascript
var 数组名 = []; // 空数组
var 数组名 = [1, 'abc', true]; // 定义数组同时赋值元素
```

**注意**：

- JavaScript语言中的数组，只要我们通过数组下标赋值，那么最大的下标值，就会自动的给数组做扩容操作。

  ```javascript
  var arr[2] = "abc";
  alert(arr.length); // 输出：3
  ```

  

# 5 函数

## 5.1 第一种定义方式

可以使用**function**关键字来定义函数。

**格式**：

```javascript
function 函数名(形参列表){
    函数体
}
```

**代码示例**：

```html
<script type = "text/javascript">
    // 定义一个无参函数
    function fun(){
        alert("无参函数被调用了！");
    }
    // 调用
    fun();
    
    // 定义有参函数
    function fun2(a, b){
        alert("有参函数fun2()被调用了 a=>" + a + ", b=>" + b);
    }
    
    // 定义有返回值的函数
    function sum(num1, num2){
        var result = num1 + num2;
        return result;
    }
</script>
```

**注意**：

- 在JavaScript语言中，只需要在函数体内直接使用`return`语句返回值即可。

## 5.2 第二种定义方式

**格式**：

```javascript
var 函数名 = function(形参列表){
    函数体
}
```

**代码示例**：

```javascript
var fun = function(){
    alert("无参函数被调用了！");
}
```

**注意**：在Java中函数允许重载，但是在JS中函数的重载会直接覆盖掉上一次的定义。



## 5.3 函数的arguments隐形参数

 就是在function函数中不需要定义，但却可以直接用来获取所有参数的变量。我们管它叫隐形参数。隐形参数特别像Java基础的可变长参数一样。

```java
public void fun(Object ...args){
    函数体
}
```

可变长参数其实是一个数组。JS中的隐形参数也跟Java的可变长参数一样，操作类似数组。

**代码示例**：

```javascript
function fun(a, b){
    alert("有参函数fun(a, b)");
}
function fun(){
    alert("无参函数");
}
fun(1, "ad");
```



# 六 自定义对象

## 6.1 Object形式的自定义对象

**格式**：

```javascript
// 对象的定义
var 变量名 = new Object(); // 对象实例（空对象）
变量名.属性名 = 值; // 定义一个属性
变量名.函数名 = function(){} // 定义一个函数

// 对象的访问
变量名.属性/函数名();
```

## 6.2 花括号形式的自定义对象

**格式**：

```javascript
// 对象的定义
var 变量名 = {}; // 空对象
var 变量名 = {
    属性名:值, // 定义一个属性
    属性名:值 // 定义一个属性
    函数名:function(){} // 定义一个函数
};

// 对象的访问
变量名.属性/函数名();
```



# 七 JS中的事件

什么是事件？事件是电脑输入设备与页面进行交互的响应，我们称之为事件。

**常用的事件**：

- onload：加载完成事件，页面加载完成之后，常用于做页面JS代码初始化操作。
- onclick：单击事件，常用于按钮的点击响应操作。
- onblur：失去焦点事件，常用于输入框失去焦点后验证其输入内容是否合法。
- onchange：内容发生改变事件，常用于下拉列表和输入框内容发生改变后操作。
- onsubmit：表单提交事件，常用于表单提交前，验证所有表单项是否合法。

## 7.1 事件的注册（绑定）

**定义**：其实就是告诉游览器，当事件响应后要执行那些操作代码，叫事件注册或绑定。

**事件的注册（绑定）**又分为**静态注册**和**动态注册**两种：

- 静态注册事件：通过HTML标签的事件属性直接赋予事件响应后的代码，这种方式称为静态注册。
- 动态注册事件：是指先通过JS代码得到标签的DOM对象，然后再通过`DOM对象.事件名 = function(){}`这种形式赋予事件响应后的代码，称之为动态注册。

**动态注册的基本步骤**：

- 第一步：获取标签对象；
- 第二步：标签对象.事件名 = function(){}



# 八 DOM模型

DOM 全称是 Document Object Model 文档对象模型。

简单来说，就是把文档中的标签、属性、文本转换成为对象来管理。

那么，它们是如何实现把标签、属性、文本转换称为对象来管理呢？

## 8.1 Document 对象

![image-20210622162911417](https://jsl1997.oss-cn-beijing.aliyuncs.com/note/image-20210622162911417.png)

Document对象的理解：

- Document 它管理了所有的HTML文档内容。
- Document 它是一种树结构的文档，有层级关系。
- Document 可以把所有的标签都对象化。
- 可以通过Document访问所有的标签对象。

## 8.2 Document对象中的方法介绍

**`document.getElementById(elementId)`**

通过标签的**id**属性查找标签的DOM对象，elementId是标签的id属性值。

**`document.getElementsByName(elementName)`**

通过标签的**name**属性查找标签DOM对象，elementName标签的name属性值。

**`document.getElementsByTagName(tagname)`**

通过标签名查找标签DOM对象，tagname是标签名

**`document.createElement(tagName)`**

通过给定的标签名，创建一个标签对象，tagName是要创建的标签名。



**注意**：

document对象的三个查询方法，如果有id属性，优先使用**getElementById**方法来进行查询。

如果没有id属性，则优先使用**getElementsByName**方法进行查询。

如果id属性和name属性都没有，最后再按标签名查**getElementsByTagName**。

以上三个方法，一定要在页面加载完成之后执行，才能查询到标签对象。



## 8.3 节点的常用属性和方法

节点就是标签对象。

### 8.3.1 方法

通过具体的元素节点调用

- **`getElementsByTagName()`**：获取当前节点的指定标签名孩子节点。
- **`appendChild(oChildNode)`**：可以添加一个子节点，`oChildNode`是要添加的孩子节点。

### 8.3.2 属性

- **childNodes**：获取当前节点的所有子节点。
- **firstChild**：获取当前节点的第一个子节点。
- **lastChild**：获取当前节点的最后一个子节点。
- **parentNode**：获取当前节点的父节点。
- **nextSibling**：获取当前节点的下一个节点。
- **previousSibling**：获取当前节点的上一个节点。
- **className**：获取或设置标签的class属性值。
- **innerHTML**：表示获取、设置起始标签和结束标签中的内容。
- **innerText**：表示获取、设置起始标签和结束标签中的文本。
