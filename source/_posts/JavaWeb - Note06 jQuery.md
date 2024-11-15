---
title: JavaWeb - Note06 jQuery
date: 2022-08-17 17:32:07
categories: JavaWeb
tags: jQuery
---

# jQuery

jQuery 是一款跨主流浏览器的 JavaScript 库，封装了 JavaScript 相关方法调用，简化 JavaScript 对 HTML DOM 操作

## 下载

下载地址：https://jquery.com/download/

文件名带 min 为压缩版（compressed），适合发布使用，相比原版，删去了空格与换行符等，以压缩体积

而原版（uncompressed）保留空格与换行符，代码可读性高，适合开发使用

## 使用

1. 将下载的文件复制到项目的 js 目录下

2. 在 script 标签中指定 jQuery 文件

   ```html
   <!-- 指定 jQuery 路径 -->
   <script type="text/javascript" src="js/jquery-3.6.0.js"></script>
   ```

3. 使用 jQuery 语法

   ```html
   <script type="text/javascript">
       /*
               1. $ 是 jQuery 中的函数名，document 是函数参数
                   作用是将 document 对象编程 jQuery 函数库可使用的对象
               2. ready 是 jQuery 中的函数，是准备的意思
                   当页面的 dom 对象加载成功后，会执行 ready 函数内容
                   ready 相当于 js 中的 onLoad 事件
               3. function 函数表示 onLoad 后要执行的功能
           */
       // 1. 标准写法
       // 与 $(), jQuery(), window.jQuery() 等价
       $(document).ready(function () {
           alert("Hello jQuery");
       })
       // 2. 快速写法
       $(function () {
           alert("quickly method");
       })
   </script>
   ```

## DOM & jQuery

dom 对象：使用 javascript 语法创建的对象叫做 dom 对象（js 对象）

```js
var obj = document.getElementById("text01");
```

jQuery 对象：使用 jQuery 语法表示的对象叫做 jQuery 对象（都是数组，存放的是 dom 对象）

```js
var jobj = $("#text01");
```

- 建议 jQuery 对象名以 $ 开头

### 互相转换

dom -> jQuery：`$([dom 对象])`

```html
<script src="js/jquery-3.6.0.js"></script>
<script>
    function btnClick() {        
        // 获取 dom 对象
        var obj = document.getElementById("btn");
        // dom 打印 value
        // alert(obj.value);

        // dom 转换为 jquery 打印
        var jobj = $(obj);
        alert(jobj.val());
    }
</script>

<button id="btn" onclick="btnClick()" value="I'm btn">button</button>
```

jQuery -> dom：从数组中获取第一个对象，使用 `[0]` 或 `get(0)`

```html
<script src="js/jquery-3.6.0.js"></script>
<script>
    function calBtn() {
        // 使用 jquery 获取 dom 对象
        // var jobj = $("#text");

        // jobj to obj
        var obj = $("#text")[0];
        // 也可以使用 .get(0)
        var num = obj.value;
        alert(num * num);
    }
</script>

<h1>Caculate squere</h1>
<input type="text" id="text" placeholder="input...">
<button id="btn" onclick="calBtn()">Cal</button>
```

## 选择器

就是定位条件，通知 jQuery 函数定位满足条件的 DOM 对象

### 基本选择器

1. id 选择器：`$("#id")`

   id 在当前页面中是唯一值

2. class 选择器：`$(".class")`

   class 表示 css 中的样式

3. 标签选择器：`$("tag")`

   使用标签名定位

4. 所有选择器：`$("*")`

   选中所有标签

5. 混合选择器：`$("#id, .class, tag")`

   混合使用选择器

### 表单选择器

使用 input 标签的 type 属性值定位 dom 对象的方式

该方法无论是否存在表单 form，均可做出选择

- 根据元素类型定义，便于操作表单

语法：`$(:"type")`

示例代码：

```html
<script src="js/jquery-3.6.0.js"></script>
<script>
    $(function () {
        $(":text").css("background", "#efefef");
    })
</script>

<input type="text">
<input type="text">
<input type="text">
```

## 过滤器

定位了 DOM 对象后，根据条件筛选对象

- 过滤条件不能独立出现在 jQuery 函数中，只能出现在选择器后方

> jQuery 对象中存储的 DOM 对象顺序与页面标签声明位置关系
>
> ```html
> <div>dom1</div>
> <div>dom2</div>
> <div>dom3</div>
> ```
>
> $("div") == [dom1, dom2, dom3]

### 基本过滤器

1. 选择第一个：`$("select:first")`
2. 选择最后一个：`$("select:last")`
3. 选择指定：`$("select:eq(index)")`
4. 选择小于索引：`$("select:lt(index)")`
5. 选择大于索引：`$("select:gt(index)")`

### 表单属性过滤器

根据表单中 dom 对象的状态情况，定位 dom 对象

1. 选择可用的文本框：`$(":text:enabled")`

2. 选择不可用的文本框：`$(":text:disabled")`

3. 复选框选中的元素：`$(":checkbox:checked")`

4. 选择指定下拉列表的被选中元素：`$("select>option:selected `

   `>` 代表左父右子

## 函数

### 第一组

1. val

   操作数组中 DOM 对象的 value 属性

   `$(select).val()`：无参调用，读取数组中第一个 DOM 对象的 value 属性值

   `$(select).val(value)`：有参调用，对数组中所有 DOM 对象的 value 属性统一赋值

2. text

   操作数组中所有 DOM 对象的文字显示内容属性

   `$(select).text()`：无参调用，读取数组中所有 DOM 对象的文字显示内容，并拼接字符串返回

   `$(select).text(value)`：有参调用，对数组中所有 DOM 对象的文字显示内容统一赋值

3. attr

   对 val、text 之外的其他属性操作

   `$(select).attr("name")`：获取 DOM 数组第一个对象的属性值

   `$(select).attr("name", "value")`：对数组中所有 DOM 对象的属性设为新值

### 第二组

1. remove

   `$(select).remove()`：将数组中所有的 DOM 对象及其子对象一并删除

2. empty

   `$(select).empty()`：将数组中所有 DOM 对象的子对象删除

3. append

   `$(select).append("<tag></tag>")`：为数组中所有 DOM 对象添加子对象

4. html

   设置或返回被选元素的内容（innerHTML）

   `$(select).html()`：无参调用，获取 DOM 数组**第一个**匹配的元素内容

   `$(select).html(value)`：有参调用，设置 DOM 数组中所有元素内容

5. each

   对数组、json、dom 数组等的遍历，对每个元素调用一次函数

   `$.each(要遍历的对象, function(index, element) {处理程序})`

   - index：数组下标 / key
   - element：数组对象 / value / dom 对象

   由于 jQuery 对象就是一个数组，所以有以下形式：

   `[jQuery对象].each(function(index, element) {处理程序})`

## 事件

jQuery 中给 DOM 对象绑定事件

### 定义元素监听事件

`$("select").监听名(处理函数)`

监听名是 js 事件去掉 on 后的内容，例如：

```js
$("#button").click(fun);
```

- 其中，fun 可以声明为隐式，或在 js 中声明

### on() 绑定事件

`$(select).on(event, function)`

## AJAX

jQuery 简化了 AJAX 请求的处理，使用三个函数可以实现 AJAX

1. $.ajax()

   核心函数，获取 ajax

2. $.post(URL, data, function(resp), dataType)

   使用 post 方式发送 ajax 请求

3. $.get(URL, data, function(resp), dataType)

   使用 get 方式发送 ajax 请求

> $.post() 与 $.get() 内部都是调用 $.ajax()

### ajax()

使用函数的参数（json）表示 url、请求方式、参数值等信息

json 结构参数说明：

| key               | value                                                        |
| ----------------- | ------------------------------------------------------------ |
| **url**           | 发送请求的 URL                                               |
| type              | 请求类型（默认 GET）                                         |
| **data**          | 规定发送到服务器的数据，可以是字符串、数组、json（多数情形） |
| **dataType**      | 期望从服务器响应的数据类型。**xml**（XML 文档）、**html**（纯文本）、**text**（纯文本字符串）、**json**（JSON 运行响应，以对象返回） |
| async             | boolean，表示请求是否异步处理，默认 true                     |
| contentType       | 发送数据到服务器时所使用的内容类型（如 applicaion/json），可省略 |
| **success(resp)** | 请求成功运行的函数，resp 为响应回的数据                      |
| error()           | 请求失败运行的函数                                           |
