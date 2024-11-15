---
title: JavaScript - Note01 ECMAScript
date: 2022-07-22 09:57:29
categories: Front End
tags: ECMAScript
---

# JavaScript

JavaScript 是运行在浏览器上的脚本语言，简称 JS，是一门事件驱动型的编程语言，依靠事件去驱动，然后执行对应的程序（事件前加 on 代表事件的句柄）

> 页面打开时，js 代码并不会执行，只是把这段代码注册到事件上，等事件发生后，注册在句柄后的 js 代码会被浏览器自动调用

JavaScipt 虽然带有 Java，但是没有任何关系，只是语法有点类似，运行位置不同（Java 运行在 JVM 中，JS 运行在浏览器中）

JavaScript 程序不需要程序员手动编译，编写完代码后，浏览器直接打开解释运行

> JavaScript 的“目标程序”以普通文本形式保存，这种语言都叫做“脚本语言”

## 嵌入方式

JS 中的字符串可以使用单/双引号

JS 语句结束的分号可省可不省

1. 直接添加属性

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>嵌入1</title>
   </head>
   <body>
       <!-- 实现功能：用户点击以下按钮，弹出消息框 -->
       <!-- 鼠标点击事件 click（事件句柄 onclick，以 html 标签属性存在） -->
       <!-- js 中内置 window 对象（浏览器对象），通过 window 对象 alert 实现弹窗 -->
       <input type="button" value="hello" onclick="window.alert('hello vv!')">
       <!-- 其中 window. 可省略 -->
       <input type="button" value="hello" onclick="alert('hello ee!')">
   </body>
   </html>
   ```

2. 脚本块

   ```html
   <!-- JS 脚本块页面中可出现多次，且出现位置无要求 -->
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>嵌入2</title>
   </head>
   <body>
       <script>
           // 暴露在脚本块中的程序，在页面打开时执行，并且遵循自上而下的原则
           alert("hello vv");
           alert("hello vv2");
       </script>
   
       <input type="button" value="I'm a button">
   </body>
   </html>
   ```

   - alert 会阻塞页面加载，直到用户点击确定按钮

3. 文件引入

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>嵌入3</title>
   </head>
   <body>
       <!-- 在需要的位置引入 js 脚本文件 -->
       <!-- 
           不能省略反标签：
               <script scr='...' />
           引入文件时，方法体中的代码不会执行，但可重写一个代码块
        -->
       <script src="js/03.js"></script>
   </body>
   </html>
   ```

## 变量

JavaScript 是一种弱类型语言，声明变量无需指定类型，可接收任意类型变量

> Java 是一种强类型语言，变量声明时的类型，之后不可变

```js
// 声明
var 变量名;
// 赋值
变量名 = 值;
```

在 JS 中，当一个变量没有手动赋值时，系统默认赋值 undefined，其在 JS 中是一个具体存在的值

```js
var i;
alert("i = " + i);
// i = undefined
```

若未声明变量直接访问则会报错（须在浏览器控制台查看错误信息）

```js
// 未定义变量（需在浏览器控制台查看错误信息）
alert(u);
```

连续定义

```js
var a, b, c = 100;
alert("a = " + a);
alert("b = " + b);
alert("c = " + c);
/*
	a = undefined
	b = undefined
	c = 100
*/
```

### 作用域

全局变量：在函数体之外，生命周期是浏览器打开到关闭（尽量少用，占用浏览器内存）

局部变量：在函数体中，包括形参，生命周期是函数开始到结束

示例代码：

```js
var name = "vv";
function showName(name) {
    var name = "ee";
    alert(name);
}
showName(); // ee
alert(name);    // vv
```

- 如果变量未使用 var 声明，则该变量无论位置，皆为全局变量

  ```js
  // 变量未使用 var 声明，该变量为全局变量
  function myfun() {
  	me = "myfun()";
  }
  alert(me);	// myfun()
  ```

## 函数

可被重复利用的代码片段，用于完成某个特定功能

语法格式：

```js
// 第一种方式
function 函数名(形式参数列表) {
    函数体;
}

// 第二种方式
函数名 = function(形式参数列表) {
    函数体;
}
```

- JS 中的函数不需要指定返回值类型

示例代码：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>函数初步</title>
</head>
<body>
    <script>
        // sum 函数
        function sum(a, b) {
            alert(a + b);
        }
        // 函数调用
        sum(10, 20);

        // sayHello 函数
        sayHello = function(name) {
            alert("hello, " + name);
        }
        // 函数调用
        sayHello('vv');
    </script>

    <input type="button" value="sayHello" onclick="sayHello('vv')">
</body>
</html>
```

### 调用

JS 中的函数调用时，参数类型、参数个数均无限制

```js
function sum(a, b) {
    return a + b;
}

// 调用函数
var retValue1 = sum(1, 2);
alert(retValue1);   // 3

var retValue2 = sum("vv");
alert(retValue2);   // vvundefined

var retValue3 = sum();
alert(retValue3);   // NaN
// NaN 表示一个具体的值，该值表示不是一个数字

var retValue4 = sum(1, 2, 3);
alert(retValue4);   // 3
```

同名函数会自动覆盖：

```js
// 同名函数会自动覆盖
function test(a) {
    alert("test")
}

function test() {
    alert("test test");
}

test("vv"); // test test
```

## 数据类型

虽然声明时不需要指定数据类型，但在赋值时还是有数据类型

原始类型：Undefined, Number, String, Boolean, Null

引用类型：Object 以及 Object 的子类

ES规范（ECMAScript）在 ES6 之后，新增了 Symbol

### typeof

可以在程序的运行阶段动态的获取变量的数据类型

语法格式：

```js
typeof 变量名
```

typeof 运算符结果是以下 6 个字符串之一（全都小写）

"undefined", "number", "string", "boolean", "object", "function"

JS 中比较字符串使用双等号完成

### Undefined

Undefined 类型只有一个值，这个值就是 undefined

当一个变量没有手动赋值，系统默认赋值 undefined，也可以手动赋值 undefined

```js
var i;	// "undefined"
var k = undefined;	// "undefined"
var y = "undefined"	// "string"
```

### Number

包括：整数、小数、正数、负数、不是数字（NaN）、无穷大（Infinity）

```js
var v1 = 1;
var v2 = 3.14;
var v3 = -100;
var v4 = NaN;
var v5 = Infinity;

// "number"
alert(typeof v1);
alert(typeof v2);
alert(typeof v3);
alert(typeof v4);
alert(typeof v5);
```

NaN（Not a number），不是一个数字，但属于 Number 类型。本来是数字运算，当运算完却不是数字时，结果是 NaN

```js
// NaN
var a = "abc";
var b = 100;
alert(a / b);   // NaN
// 字符串拼接
alert(a + b);   // abc100
```

Infinity，当除数为0是结果为无穷大

```js
// Infinity
alert(10 / 0);
```

#### 常用函数

1. isNaN()

   括号中填数据，结果是 true 表示不是一个数字，结果是 false 表示是一个数字

   ```js
   // 改进 sum
   function sum(a, b) {
       if (isNaN(a) || isNaN(b)) {
           alert(a + " and " + b + " not all number");
           return
       }
       return a + b;
   }
   
   alert(sum(10, "vv"));
   ```

2. parseInt()

   将字符串自动转换为数字，并且取整数位

   ```js
   // parseInt()
   alert(parseInt(3.14));	// 3
   alert(parseInt("4.15"));	// 4
   ```

3. parseFloat()

   将字符串自动转换为数字

   ```js
   // parseFloat()
   alert(parseFloat(4.44));	// 4.44
   alert(parseFloat("5.553") + 1);	// 6.553
   ```

4. Math.ceil()

   Math 是数学类，函数的作用是：向上取整

   ```js
   // Math.ceil()
   alert(Math.ceil(3.11));
   ```


### Boolean

JS 中的布尔类型永远只有 true 和 false

Boolean 类型中有一个函数叫做：Boolean()

语法格式：

```js
Boolean(数据)
```

- 将非布尔类型转换成布尔类型

if 中若不为 boolean 类型，则调用 Boolean() 函数自动转换

```js
var name = "vv";
// 若 if 中不为 boolean，会自动调用 Boolean() 将其转换为 boolean 类型
if (name) {
    alert("Welcome, " + name);
} else {
    alert("Name can't be null");
}
```

Boolean() 自动转换规律：

```js
// 规律：“有”转为 true，“无”转换为 false
alert(Boolean(1));  // true
alert(Boolean(0));  // false
alert(Boolean("")); // false
alert(Boolean("abc"));  // true
alert(Boolean(null));   // false
alert(Boolean(NaN));    // false
alert(Boolean(undefined));  // false
alert(Boolean(Infinity));   // true
```

### Null

Null 类型只有一个数据 null

- typeof(null) 结果为 object 类型

### String

JS 中字符串可以使用单引号或双引号

创建字符串的两种方式：

1. 直接使用引号

   ```js
   var s1 = "abc";
   ```

2. 使用 JS 内置支持类 String

   ```js
   var s2 = new String("def");
   ```

   - String 是一个内置的类，可以直接使用，父类是 Objcet

测试两种定义方式的数据类型：

```js
var s1 = "abc";
var s2 = new String("def");

// 小 String（String 原始数据类型）
alert(typeof(s1));	// string
// 大 String（Object 数据类型）
alert(typeof(s2));	// object

// 无论大小 String，他们的属性通用
// 例如 .length 获取字符串长度
alert(s1.length);
alert(s2.length);
```

#### 常用属性

1. length

   获取字符串长度

#### 常用函数

1. indexOf()

   获取指定字符串在当前字符串中第一次出现处的索引

   ```js
   alert("http://wataaaame.github.io".indexOf("http"));  // 0
   alert("http://wataaaame.github.io".indexOf("https"));  // -1
   // 引申：判断一个字符串中是否包含某一个字符串？
   alert("http://wataaaame.github.io".indexOf("https") >= 0 ? "包含" : "不包含");  // 不包含
   ```

2. lastIndexOf()

   获取指定字符串在当前字符串中最后一次出现处的索引

3. replace()

   替换

   ```js
   // replace
   // 默认只替换第一个
   // 想全部替换需要正则表达式
   alert("a & b & c & d".replace("&", "+"));   // a + b & c & d
   ```

4. substr()

   截取子字符串

5. substring()

   截取子字符串

   ```js
   var str = "abcdefg";
   // substr(startIndex, length)
   alert(str.substr(2, 4));    // c d e f
   // substring(startIndex, endIndex)（左闭右开）
   alert(str.substring(2, 4)); // c d
   ```

6. toLowerCase()

   转换小写

7. toUpperCase()

   转换大写

8. split()

   拆分字符串

### Object

Object 类型是所有类型的超类，自定义的任何类型，默认继承 Object

JS 中定义的类默认继承 Object，会继承类中所有的属性以及函数

prototype 属性：给类动态地扩展属性和函数

定义类：

```js
function 类名(形参) {
    
}

类名 = function(形参) {
    
}
```

创建对象：

```js
new 构造方法名(实参);	// 构造方法名和类名一致
```

类与函数：

```js
// 把 function 当作一个类
new sayHello();

// 把 function 当作一个函数
sayHello();
```

类的定义同时又是一个构造函数的定义：

```js
function User(a, b, c) {
    // 声明属性（this 表示当前对象）
    // User 类中有三个属性：sno/sname/sage
    this.sno = a;
    this.sname = b;
    this.sage = c;
}

var u1 = new User(101, "vv", 13);
alert(u1.sno);  // 101
alert(u1.sname);    // vv
alert(u1.sage); // 13

// 访问一个对象的属性，还可以使用这种语法
alert(u1["sno"]); // 101
alert(u1["sname"]);   // vv
alert(u1["sage"]);    // 13
```

类中方法：

```js
// 类中函数
function Product(name, price) {
    // 属性
    this.name = name;
    this.price = price;

    // 函数
    this.getPrice = function() {
        return this.price;
    }
}
var pro = new Product("Watermallo", 4.0);
alert(pro.getPrice());	// 4
```

- 静态方法也需要创建对象后调用

prototype 属性动态扩展

```js
// 可通过 prototype 属性来给类动态扩展属性以及函数
// 扩展属性
Product.prototype.sale = 5;
// 扩展函数
Product.prototype.getName = function() {
	return this.name;
}

pro = new Product("Orange", 3);
alert(pro.getName());	// Orange
alert(pro.sale);	// 5
```

## null NaN undefined

| 区别           | null   | NaN               | undefined   |
| -------------- | ------ | ----------------- | ----------- |
| 数据类型不一致 | object | number            | undefined   |
| ==             |        | 与 undefined 等同 | 与 NaN 等同 |
| ===            | false  | false             | false       |

JS 中两个特殊运算符

1. ==，等同运算符，只判断值
2. ===，全等运算符，既判断值又判断数据类型

```js
alert(1 == true);	// true
alert(1 === true);	// false
```

## 事件

事件是可以被控件识别的操作

任何一个事件都对应一个时间句柄，事件句柄是在时间前添加 on，以属性的形式存在

### 类别

#### 焦点

1. blur 失去焦点
2. focus 获得焦点

#### 鼠标

1. click 鼠标单击
2. dblclick 鼠标双击
3. mousedown 鼠标按下
4. mouseup 鼠标弹起
5. mousemove 鼠标移动
6. mouseover 鼠标经过
7. mouseout 鼠标离开

#### 键盘

1. keydown 键盘按下

   ```html
   <script>
       window.onload = function() {
           // 获取键值，浏览器传递一个事件对象供函数调用
           document.getElementById("username").onkeydown = function(event) {
               // 对于“键盘事件对象”来说，都有 keyCode 属性用来获取键值
               // 回车键的键值是 13
               // ESC键的键值是 27
               if (event.keyCode == 13) {
                   alert("login success!");
               }
           }
       }
   </script>
   
   <input type="text" id="username">
   ```

2. keyup 键盘弹起

#### 表单

1. reset 表单重置
2. submit 表单提交

#### 其他

1. change 下拉列表选中项改变，或文本框内容改变

2. select 文本被选中

3. load 页面加载完毕

   整个 HTML 页面中所有的元素全部加载完毕之后发生

### 注册方式

1. 标签中直接使用事件句柄

   ```html
   <script>
       // 函数被调用称为：callback（回调函数）
       function sayHello() {
           alert("Hello");
       }
   </script>
   
   <input type="button" value="sayHello" onclick="sayHello()">
   ```

2. 使用纯 JS 代码

   ```html
   <input type="button" value="document" id="mybtn">
   <script>
       function sayDoc() {
           alert("Document");
       }
       // 第一步：先获取这个按钮对象（document 为内置对象，代表整个页面）
       var btnObj = document.getElementById("mybtn")
       // 第二步：给按钮对象的 onclick 属性赋值
       // 此处函数不能加小括号，否则会自动执行
       btnObj.onclick = sayDoc;
   </script>
   ```

   支持匿名函数：

   ```html
   <input type="button" value="niming" id="btnniming">
   <script>
       // 支持匿名函数
       // 页面打开时仅注册，事件发生后才会调用
       document.getElementById("btnniming").onclick = function() {
           alert("niming function");
       }
   </script>
   ```

### JS 代码执行顺序

```html
<!-- load 事件在全部元素加载完毕后发生 -->
<!-- 否则先定义函数，button 还未生成，未能绑定事件 -->
<body onload="ready()">
    <script>
        function ready() {
            document.getElementById("btn").onclick = function() {
                alert("already");
            }
        }
    </script>

    <input type="button" value="ready?" id="btn">
</body>
```

也可以不在标签内写 onload

```html
<script>
    // 在这里调用 window.onload，并使用匿名内部类
    // 页面加载过程中，将外层函数注册给 load 事件
    // 页面加载完毕后，load 事件发生，执行外层回调函数
    // 外层回调函数执行过程中，将内层函数注册给 click 事件
    // 当按钮节点发生 click 事件后，内层函数被调用并执行
    window.onload = function() {
        document.getElementById("btn").onclick = function() {
            alert("already");
        }
    }
</script>

<input type="button" value="ready?" id="btn">
```

### 设置节点属性

```html
<script>
    window.onload = function() {
        document.getElementById("btn").onclick = function() {
            // 一个节点对象中有的属性都可以“.”
            document.getElementById("text").type = "checkbox";
        };
    }
</script>

<input type="text" id="text">
<input type="button" value="将文本框改变为复选框" id="btn">
```

## void 运算符

void(表达式)

void 运算符执行括号中的表达式，但不返回任何结果

示例代码：

```html
Title
<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>

<!-- 
    href 若不填，则返回空字符串
    此处不能直接 void(0)，会被识别为路径，需要加上 “javascript:”，且 void() 中的表达式可为任意值
-->
<a href="javascript:void(0)" onclick="window.alert('yes')">
    既保留住超链接样式，同时用户点击超链接时执行一段 JS 代码，但页面不能跳转
</a>
```

## 控制语句

if, switch, while, do...while..., for, break, continue,

*for...in*

```js
// for...in 语句
// 用在数组，拿出的是数组下标（Java foreach 拿出的是元素）
for (var i in arr) {
    alert(arr[i]);
}

// for...in 语句可以遍历对象的属性
User = function(name, password) {
    this.name = name;
    this.password = password;
}
var u1 = new User("vv", 123);
// 用在对象，拿出的是属性名
for (var i in u1) {
    // typeof 为 String 类型，访问不需要加双引号
    alert(u1[i]);
}
```

*with*

自动将 `类名.` 加到属性前面

```js
// 使用 with 访问对象属性
var u2 = new User("ee", 456);
with(u2) {
    alert(name + ", " + password);
}
```

## Array

```js
// 创建长度为0的数组
var arr1 = [];
// 数据类型随意
var arr2 = [1, 3.14, -5, "abc", false, new Object()];
// 下标越界自动扩容
arr2[7] = "test";
for (var i = 0; i < arr2.length; i++) {
    document.write(arr2[i] + "<br>");
}
document.write("<br>");

// 另一种创建数组对象的方式
var a = new Array(0);
document.write(a.length);   // 0

var a2 = new Array(3);
document.write(a2.length);  // 3

var a3 = new Array(2, 3);   // 存储2和3
document.write(a3.length);  // 2
```

数组方法：

```js
// 分割
var a = [1, 2, 3, 9];
document.write(a.join("-") + "<br>");    // 1-2-3-9

// 末尾追加元素（长度+1）
a.push(10);
document.write(a.join("-") + "<br>");   // 1-2-3-9-10

// 弹出末尾元素（长度-1）（返回该元素）
document.write(a.pop() + "<br>");   // 10

// 反转
a.reverse();
document.write(a.join("-"));    // 9-3-2-1
```

