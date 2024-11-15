---
title: JavaScript - Note02 DOM & BOM
date: 2022-07-23 14:47:04
categories: Front End
tags: [DOM, BOM]
---

# JavaScript

JavaScript 包括三大块：

- ECMAScript：JS 的核心语法（ES 规范/ECMA-262 标准）

- DOM：Document Object Model（文档对象模型）

  对网页当中的节点进行增删改的过程，HTML 文档被当作一棵 DOM 数来看待

  ```js
  var domObj = document.getElementById("id");
  ```

- BOM：Browser Object Model（浏览器对象模型）

  关闭浏览器窗口、打开一个新浏览器窗口、后退、前进、浏览器地址栏上的地址等

## DOM & BOM

DOM 顶级对象是 document

BOM 顶级对象是 window

实际上 BOM 是包括 DOM 的

```html
<script>
    window.onload = function() {
        // BOM 包含 DOM，window. 可省略
        var btnElt = window.document.getElementById("btn")
        alert(btnElt);
    }
</script>
<input type="button" value="window.document" id="btn">
```

![BOM&DOM](/images/image-js/note02-01-BOM&DOM.PNG)

# DOM

获取文本框的 value

```html
<!-- 获取文本框的 value -->
<script>
    window.onload = function() {
        document.getElementById("btn").onclick = function() {
            alert(document.getElementById("txt").value);
        }
    }
</script>
<input type="text" id="txt">
<input type="button" value="copy text" id="btn">
```

输入文本框，点击按钮将内容复制到另一个文本框

```html
<!-- 输入文本框，点击按钮将内容复制到另一个文本框 -->
<script>
    window.onload = function() {
        document.getElementById("btnCopy").onclick = function() {
            var txtInput = document.getElementById("txtInput").value;
            document.getElementById("txtOutput").value = txtInput;

        }
    }
</script>
<input type="text" id="txtInput">
<input type="button" value="copy" id="btnCopy">
<br>
<input type="text" id="txtOutput" disabled>
```

this 获取文本框 value

```html
<!-- this 获取文本框 value -->
<input type="text" onblur="alert(this.value)">
```

- 失去焦点时，输出当前标签 value

## innerHTML & innerText

二者为属性，皆可设置元素内部内容

innerHTML 会把字符串当作 HTML 代码解释并执行

innerText 仅当作字符串执行

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>innerHTML & innerText</title>
    <style>
        div {
            width: 200px;
            height: 100px;
            background-color: rgb(199, 252, 225);
        }
    </style>
</head>
<body>
    <script>
        window.onload = function() {
            // 设置 div 内容
            var btn1 = document.getElementById("btn1");
            var btn2 = document.getElementById("btn2");

            btn1.onclick = function() {
                // 获取 div 对象
                var div1 = document.getElementById("div1");
                // 使用 innerHTML 属性来设置元素内部内容
                div1.innerHTML = "<font color='red'>username can't be null</font>";
            }
            btn2.onclick = function() {
                var div2 = document.getElementById("div2");
                // 使用 innerText 属性来设置元素内部内容
                div2.innerText = "<font color='red'>username can't be null</font>";
            }
        }
    </script>

    <input type="button" value="innerHTML" id="btn1">
    <div id="div1"></div>
    <input type="button" value="innerText" id="btn2">
    <div id="div2"></div>
</body>
</html>
```

## 正则表达式

Regular Expression，是一门独立的学科，主要用在字符串格式匹配方面，大部分编程语言都支持

### 常用符号

| 符号 | 含义                     |
| ---- | ------------------------ |
| .    | 除换行符以外的任意字符   |
| \w   | 字母或数字或下划线或汉字 |
| \s   | 任意空白符               |
| \d   | 数字                     |
| \b   | 单词的开始或结束         |
| ^    | 字符串的开始             |
| $    | 字符串的结束             |

### 匹配次数

| 符号   | 含义         |
| ------ | ------------ |
| *      | 零次或更多次 |
| +      | 一次或更多次 |
| ?      | 零次或一次   |
| {n}    | n 次         |
| {n, }  | n 次或更多次 |
| {n, m} | n 到 m 次    |

### 反义

| 符号     | 含义                                   |
| -------- | -------------------------------------- |
| \W       | 任意不是字母、数字、下划线、汉字的字符 |
| \S       | 任意不是空白的字符                     |
| \D       | 任意非数字的字符                       |
| \B       | 不是单词开头或结束的位置               |
| [^x]     | 除了 x 以外的任意字符                  |
| [^aeiou] | 除了 aeiou 字母以外的任意字符          |

### Example

QQ 号的正则表达式：`^[1-9]{1}[0-9]{4,}$`

从 ^ 到 $

[1-9] 表示1到9的任意1个数字（次数 {1} 可省略）

> `[A-Za-z0-9-]` 表示 A-Z、a-z、0-9、-，以上所有字符中的任意一字符

### 正则表达式对象

创建对象的方法：

1. 双斜杠

   ```js
   var regExp = /正则表达式或字符/flags;
   ```

2. 使用内置支持类 RegExp

   ```js
   var regExp = new RegExp("正则表达式或字符", "flags");
   ```

   关于 flags：

   | 符号 | 含义                                 |
   | ---- | ------------------------------------ |
   | g    | 全局匹配                             |
   | i    | 忽略大小写                           |
   | m    | 多行搜索（无法用在正则表达式的情况） |

对象的 boolean test() 方法：

正则表达式对象.test(用户填写的字符串)

true：字符串格式匹配成功

false：字符串格式匹配失败

邮箱地址格式的正则验证：

```html
<script>
    window.onload = function() {
        // 点击按钮验证邮箱地址格式
        document.getElementById("btn").onclick = function() {
            var email = document.getElementById("email").value;
            var emailRegExp = /^[a-z0-9]+([._\\-]*[a-z0-9])*@([a-z0-9]+[-a-z0-9]*[a-z0-9]+.){1,63}[a-z0-9]+$/;
            if (!emailRegExp.test(email)) {
                document.getElementById("emailError").innerText = "email is illegal";
            }
        }

        // 光标再次聚焦则需要恢复提示
        document.getElementById("email").onfocus = function() {
            document.getElementById("emailError").innerText = null;
        }
    }
</script>

<input type="text" id="email">
<span id="emailError" style="color: red; font-size: 12px;"></span>
<br>
<input type="button" value="check email" id="btn">
```

## trim

去除字符串前后空白

```html
<script>
    window.onload = function() {
        document.getElementById("btn").onclick = function() {
            alert("->" + document.getElementById("username").value.trim() + "<-");
        }
    }
</script>
<input type="text" id="username">
<input type="button" value="remove space" id="btn">
```

低版本的 IE 浏览器（IE 8.0）不支持字符串 trim() 的解决方法：

对 String 类扩展一个全新的 trim() 函数

```html
<script>
    // 对 String 类扩展一个全新的 trim() 函数
    String.prototype.trim = function() {
        return this.replace(/^\s+/, "").replace(/\s+$/, "");
    }

    window.onload = function() {
        document.getElementById("btn").onclick = function() {
            alert("->" + document.getElementById("username").value.trim() + "<-");
        }
    }
</script>
<input type="text" id="username">
<input type="button" value="remove space" id="btn"><script>
    // 对 String 类扩展一个全新的 trim() 函数
    String.prototype.trim = function() {
        return this.replace(/^\s+/, "").replace(/\s+$/, "");
    }

    window.onload = function() {
        document.getElementById("btn").onclick = function() {
            alert("->" + document.getElementById("username").value.trim() + "<-");
        }
    }
</script>
<input type="text" id="username">
<input type="button" value="remove space" id="btn">
```

## Date

```js
// 关于 JS 中内置的支持类：Date，可以用来获取时间/日期
// *获取系统当前时间
var nowTime = new Date();
// 输出（类似于 System.out.println())
// document.write(nowTime);

// 转换成具有本地语言环境的日期格式
document.write(nowTime.toLocaleString());
document.write("<br>");

// 获取时间后自定制日期格式
var d = new Date();
var year = d.getFullYear(); // 全格式返回年信息
var month = d.getMonth();   // 月份是 0-11
// var dayOfWeek = d.getDay(); // 获取一周的第几天
var day = d.getDate();  // 获取日信息
document.write(year + "年" + (month+1) + "月" + day + "日");
document.write("<br>");

// *获取毫秒数（1970.01.01 00:00:00 000 至今）
var msa = d.getTime();
document.write(msa);
document.write("<br>");
// 联合使用
document.write(new Date().getTime());
```

### 设置网页时钟

```html
<script>
	// 时间函数
    function displayDate() {
        var nowDate = new Date().toLocaleString();
        var dateElt = document.getElementById("date");
        dateElt.innerText = nowDate;
    }
    // 开始展示时间函数
    function displayStart() {
        // 每隔一秒调用一次函数，实现自动更新
        displayDate();
        // 全局变量传递给 window.cleanInterval() 以终止时间
        stop = window.setInterval("displayDate()", 1000);
    }
    // 停止时间函数
    function displayStop() {
        window.clearInterval(stop);
    }
</script>

<!-- 点击按钮显示网页时钟 -->
<input type="button" value="Now Date" onclick="displayStart()" id="btnDate">
<!-- 点击按钮终止网页时钟 -->
<input type="button" value="Stop Date" onclick="displayStop()">
<div id="date"></div>
```

## Example

### 表单验证

要求：

1. 用户名不能为空
2. 用户名必须在6-14位之间
3. 用户名只能有数字和字母组成，不能有其他符号（正则表达式）
4. 密码和确认密码一致，邮箱地址合法
5. 统一失去焦点验证
6. 错误提示信息统一在 span 标签中提示，要求字体 12px、红色
7. 文本框再次获得焦点后，清空错误提示信息；如果文本框中数据不合法，要求清空文本框的 value
8. 最终表单中所有项均合法方可提交

```html
<!-- 
    表单验证
    要求：
        1. 用户名不能为空
        2. 用户名必须在6-14位之间
        3. 用户名只能有数字和字母组成，不能有其他符号（正则表达式）
        4. 密码和确认密码一致，邮箱地址合法
        5. 统一失去焦点验证
        6. 错误提示信息统一在 span 标签中提示，要求字体 12px、红色
        7. 文本框再次获得焦点后，清空错误提示信息；如果文本框中数据不合法，要求清空文本框的 value
        8. 最终表单中所有项均合法方可提交
 -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>表单验证</title>
    <style>
        span {
            font-size: 12px;
            color: red;
        }
    </style>
</head>
<body>
    <script>
        window.onload = function() {

            // 用户名
            // 获取用户名元素
            var unElt = document.getElementById("un");
            // 获取用户名的错误信息元素
            var unErr = document.getElementById("unErr");
            // 验证是否合法
            function unLegal() {
                var unVal = unElt.value.trim();
                // 判断用户名合法的正则表达式
                var unRegExp = /^[A-Za-z0-9]{6,14}$/;
                
                if (!unVal) {
                    unErr.innerText = "Username can't be null";
                    return false;
                } else if (unVal.length < 6 || unVal.length > 14) {
                    unErr.innerText = "Username must between 6 and 14";
                    return false;
                } else if (!unRegExp.test(unVal)) {
                    unErr.innerText = "Username were only in num or word";
                    return false;
                }
                return true;
            }
            // 失去焦点
            unElt.onblur = function() {
                unLegal();
            }
            // 获取焦点清空错误信息，若不合法，清空输入框
            unElt.onfocus = function() {
                // 直接通过是否有错误信息判断
                if (unErr.innerText) {
                    unElt.value = null;
                }
                unErr.innerText = null;
            }

            // 密码
            var pwdElt = document.getElementById("pwd");
            var pwdErr = document.getElementById("pwdErr");
            // 验证是否合法
            function pwdLegal() {
                var pwdVal = pwdElt.value.trim();
                if (pwdVal == "") {
                    pwdErr.innerText = "Password can't be null";
                    return false;
                }
                return true;
            }
            // 失去焦点
            pwdElt.onblur = function() {
                pwdLegal();
            }
            // 获取焦点
            pwdElt.onfocus = function() {
                pwdErr.innerText = null;
            }

            // 确认密码
            var rpwdElt = document.getElementById("rpwd");
            var rpwdErr = document.getElementById("rpwdErr");
            // 验证是否合法
            function rpwdLegal() {
                var rpwdVal = rpwdElt.value.trim();
                if (rpwdVal != pwdElt.value.trim()) {
                    rpwdErr.innerText = "Oops! Password not same";
                    return false;
                }
                return true;
            }
            // 失去焦点验证
            rpwdElt.onblur = function() {
                rpwdLegal();
            }
            // 获取焦点验证
            rpwdElt.onfocus = function() {
                rpwdErr.innerText = null;
            }

            // submit 提交判断，若有一个不合法，则聚焦对应文本框
            var submitElt = document.getElementById("submit");
            submitElt.onclick = function() {
                if (!unLegal()) {
                    return false;
                } else if (!pwdLegal()) {
                    return false;
                } else if (!rpwdLegal()) {
                    return false;
                }
            }

            // reset 清除所有错误提示
            resetElt = document.getElementById("reset");
            resetElt.onclick = function() {
                unErr.innerText = null;
                pwdErr.innerText = null;
                rpwdErr.innerText = null;
            }
        }
    </script>

    <form action="https://localhost:8080/dom/hw01" method="post">
        <table>
            <tr>
                <td>用户名*</td>
                <td><input type="text" name="username" id="un"></td>
                <td><span id="unErr"></span></td>
            </tr>
            <tr>
                <td>密码*</td>
                <td><input type="password" name="password" id="pwd"></td>
                <td><span id="pwdErr"></span></td>
            </tr>
            <tr>
                <td>确认密码*</td>
                <td><input type="password" id="rpwd"></td>
                <td><span id="rpwdErr"></span></td>
            </tr>
            <tr>
                <td>邮箱</td>
                <td><input type="text" name="email" id="em"></td>
                <td><span id="emErr"></span></td>
            </tr>
        </table>

        <input type="submit" value="register" id="submit">
        <input type="reset" value="reset" id="reset">
    </form>
</body>
</html>
```

> vv：
>
> 1. 若获取的属性需要修改，则应获取对象元素即可，否则修改的是变量而不是目标属性。因为存储对象的变量是一个引用类型，可以直接访问到该对象的属性
> 2. trim() 是字符串的方法

### 复选框的全选

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>全选</title>
</head>
<body>
    <script>
        window.onload = function() {
            saElt = document.getElementById("sa");
            // 根据 name 获取所有 habit 元素
            var saSta = document.getElementsByName("habit");

            // 全选框 onclick
            saElt.onclick = function() {
                // 可将所有元素选中状态直接更改为全选框的选中状态
                for (i in saSta) {
                    saSta[i].checked = saElt.checked;
                }
            }

            // 每个单选框 onclick
            for (i in saSta) {
                saSta[i].onclick = function() {
                    // 每次选中则遍历列表，若选中数等于总数，则全选框自动选中
                    var checkedCount = 0;
                    for (i in saSta) {
                        if (saSta[i].checked) {
                            checkedCount++;
                        }
                    }
                    // 判断选中数与总数，并修改全选框状态
                    saElt.checked = checkedCount == saSta.length;
                }
            }
        }
    </script>
    <input type="checkbox" id="sa">Select All
    <br>
    <input type="checkbox" name="habit">Sing
    <br>
    <input type="checkbox" name="habit">Dancing
    <br>
    <input type="checkbox" name="habit">Piano
</body>
</html>
```

> vv：
>
> 譬如 16、32 行代码，运算结果判断影响变量时可进行代码优化，直接赋值

### 获取下拉列表选中的 value

```html
<select onchange="alert(this.value)">
    <option value="">--请选择城市--</option>
    <option value="001">武汉市</option>
    <option value="002">襄阳市</option>
    <option value="003">麻城市</option>
</select>
```

# BOM

BOM 编程汇总，window 对象是顶级对象，代表浏览器窗口

## open

window.open('url', '_[self, blank, parent, top]')

打开窗口，第一个参数填写地址，第二个参数填写打开方式

```html
<input type="button" value="开启 blog（默认窗口）" onclick="window.open('https://wataaaame.github.io')">
<input type="button" value="开启 blog（当前窗口）" onclick="window.open('https://wataaaame.github.io', '_self')">
<input type="button" value="开启 blog（新窗口）" onclick="window.open('https://wataaaame.github.io', '_blank')">
<input type="button" value="开启 blog（父窗口）" onclick="window.open('https://wataaaame.github.io', '_parent')">
<input type="button" value="开启 blog（顶级窗口）" onclick="window.open('https://wataaaame.github.io', '_top')">
<br>

<input type="button" value="to 02-close.html" onclick="window.open('02-close.html')">
```

## close

window.close()

关闭窗口

```html
<input type="button" value="close" onclick="window.close()">
```

## history

window.history.back()

同浏览器返回，默认后退上一级

window.history.go()

同浏览器前进，默认前进0

- go(-1) 等同于 back()

```html
<input type="button" value="back" onclick="window.history.back()">
<input type="button" value="back" onclick="window.history.go(-1)">
```

## location

window.location.href

该**属性**设置浏览器地址

- href 可省略

```html
<input type="button" value="vv blog" onclick="window.location.href = 'https://wataaaame.github.io'">
```

document 中也有 .location.href，且 href 同样可以省略

```js
document.location.href = "url";
```

总结：哪些方法可以通过浏览器向服务器发送请求？

1. 直接地址栏输入 URL 回车
2. 超链接
3. 表单 submit
4. window.open
5. window.location.href
6. document.location.href

以上所有请求方式均可携带数据给浏览器，但只有**直接输入**与**表单 submit** 可以动态携带

## iframe

iframe元素表示嵌套的浏览上下文。

```html
<iframe src="08.html" width="500px" height="500px"></iframe>
```

## top

window.self

当前窗口

window.top

顶层窗口

```html
<script>
    // 如果当前页面不是顶级窗口，设置为顶级窗口
    function setTop() {
        if (window.top != window.self) {
            window.top.location = window.self.location;
        }
    }
</script>
<input type="button" value="如果当前页面不是顶级窗口，设置为顶级窗口" onclick="setTop()">

```

