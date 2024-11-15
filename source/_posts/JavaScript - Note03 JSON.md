---
title: JavaScript - Note03 JSON
date: 2022-07-25 18:20:14
categories: Front End
tags: JSON
---

# JSON

JSON（JavaScript Object Notation），是一种数据交换格式标准，在 JS 里以对象形式存在，最主要的作用是进行数据交换

轻量级的数据交换格式，特点是：体积小、易解析

> 实际开发中还有一种数据交换格式：XML
>
> 虽体积大、解析麻烦，但是语法严谨（e.g. 银行业务）

## 定义一个 JSON 对象

```js
// 创建一个 json 对象
var studentObj = {
    "sno" : "101",
    "sname" : "vv",
    "sex" : "1"
};
// 访问该 json 对象的属性
alert(studentObj.sno + ", " + studentObj.sname + ", " + studentObj.sex);
```

- JSON 与对象定义方法相似，所以又被称为“无类型对象”

## 定义一个 JSON 数组

```js
// 定义一个 json 数组
var students = [
    {"sno" : "101", "sname" : "vv", "sex" : "1"},
    {"sno" : "102", "sname" : "ee", "sex" : "0"},
    {"sno" : "103", "sname" : "xx", "sex" : "1"}
];
// 遍历 json 数组
for (i in students) {
    document.write(students[i].sno + ", " + students[i].sname + ", " + students[i].sex + "<br>");
}
```

## JSON 嵌套

```js
// JSON 嵌套
var user = {
    "userid" : 101,
    "username" : "vv",
    "sex" : true,
    "address" : {
        "city" : "Wuhan",
        "street" : "Wuchang"
    },
    "habit" : ["Java", "Guitar"]
};
// 访问人名与居住地
alert(user.username + " live in " + user.address.city + " " + user.address.street);
```

## window.eval()

将字符串当作一段 JS 代码解释并执行

```js
window.eval("var i = 100");
document.write(i + "<br>"); // 100
```

Java 连接数据库，查询数据之后，将数据在 Java 程序中拼接成 JSON 格式的“字符串”，并将该字符串响应到浏览器

> Java 响应到浏览器上的只是一个 “JSON 格式字符串”，还不是一个 JSON 对象

示例代码：

```js
var fromJava = "{\"name\" : \"vv\", \"password\" : \"123\"}"
// 将以上 JSON 字符串转换为 JSON 对象
window.eval("var jsonObj = " + fromJava);
// 访问 JSON 对象
document.write(jsonObj.name + ", " + jsonObj.password + "<br>");
```

## 面试题

[] 与 {} 的区别？

- JS 中

  []：数组

  {}：JSON

- Java 中：

  []：取址符

  {}：数组

## 设置 table 的 tbody

将 JSON 数据绑定到 table 的 tbody 中

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>设置table的tbody</title>
</head>
<body>
    <script>
        var data = {
            "total" : 4,
            "emp" : [
                {"empno" : 101, "ename" : "vv", "sal" : 8000},
                {"empno" : 102, "ename" : "ee", "sal" : 16000},
                {"empno" : 103, "ename" : "xx", "sal" : 7000},
                {"empno" : 104, "ename" : "yy", "sal" : 6000}
            ]
        };

        function display() {
            // 遍历取出每条 emp 信息，拼接成 HTML 标签，通过 innerHTML 插入到 tbody
            var tbodyHTML = "";
            for (i in data.emp) {
                tbodyHTML += "<tr>";
                tbodyHTML += "<td>" + data.emp[i].empno + "</td>";
                tbodyHTML += "<td>" + data.emp[i].ename + "</td>";
                tbodyHTML += "<td>" + data.emp[i].sal + "</td>";
                tbodyHTML += "</tr>"
            }
            // 拼接
            document.getElementById("tbody").innerHTML = tbodyHTML;
            // 总条目
            document.getElementById("count").innerText = data.total;
        }
    </script>

    <h2>EMP TABLE</h2>
    <input type="button" value="display" onclick="display()">
    <hr>
    <table border=1px width="50%">
        <tr>
            <th>EMPNO</th>
            <th>ENAME</th>
            <th>SAL</th>
        </tr>
        <tbody id="tbody">
            <!-- <tr>
                <td></td>
                <td></td>
                <td></td>
            </tr> -->
        </tbody>
    </table>
    Total&nbsp;<span id="count">0</span>&nbsp;items
</body>
</html>
```

