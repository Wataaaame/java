---
title: CSS - Note
date: 2022-07-21 15:52:26
categories: Front End
tags: CSS
---

# CSS

CSS（Cascading Style Sheet）：层叠样式表语言

作用：修饰 HTML 页面，设置某些元素的样式，让其更加好看

> CSS 好比 HTML 的化妆品

## 使用 CSS 的三种方式

1. 内联定义：在标签内部使用 style 属性来设置元素的 CSS 样式

   ```html
   <标签 style="样式名:样式值; 样式名:样式值..."></标签>
   ```

   示例代码：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>内联定义样式</title>
   </head>
   <body>
       <!-- 
           width 宽度样式
           height 高度样式
           background-color 背景色样式
           display 布局样式（none 隐藏，block 显示）
        -->
       <div style="display:none; width:300px; height:300px; background-color:#CCFFFF"></div>
       <br><br><br>
       <div style="width:300px; height:300px; background-color:#CCFFFF;
           border: 1px solid black;"></div>
   </body>
   </html>
   ```

2. 内部样式块对象：在 HTML 文档的 `<HTML>` 和 `<BODY>` 标记之间插入一个 `<STYLE>...</STYLE>` 块对象

   ```html
   <head>
   	<style type="text/css">
           选择器 {
   			样式名: 样式值;
               样式名: 样式值;
               ...
           }
   		选择器 {
   			样式名: 样式值;
               样式名: 样式值;
               ...
           }
   	</style>
   </head>
   ```

   示例代码：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>内部样式块对象</title>
   
       <style type="text/css">
           /* 这是 CSS 的注释（与 Java 相同） */
           /* id 选择器 */
           #usernameErrorMsg {
               color: red;
               font-size: 50px;
           }
   
           /* 标签选择器 */
           div {
               background-color: black;
               border: 1px solid red;
               width: 800px;
               height: 100px;
           }
   
           /* 类选择器 */
           .class {
               width: 400px;
               height: 100px;
           }
       </style>
   </head>
   <body>
       <span id="usernameErrorMsg">Sorry, username can't be null</span>
       <br>
   
       <div></div>
       <div></div>
       <div></div>
       <br>
   
       <!-- class 相同的属于同一类 -->
       class<input type="text" class="class">
       <select class="class">
           <option>11</option>
           <option>22</option>
       </select>
   </body>
   </html>
   ```

3. **链入外部样式表文件**：将样式写到一个独立的 xxx.css 中，在需要的网页上直接引入 css 文件

   ```html
   <link type="text/css" rel="stylesheet" href="css文件路径" />
   ```

   优点：易维护，维护成本低

   HTML

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>引入外部样式文件</title>
   
       <!-- 引入样式文件 -->
       <link rel="stylesheet" href="css/03.css">
   </head>
   <body>
       <a href="https://wataaaame.github.io">真实的超链接无样式</a>
       <br><br>
       <span id="span1">虚假的文字像超链接</span>
   </body>
   </html>
   ```

   CSS

   ```css
   a {
       text-decoration: none;
   }
   
   #span1 {
       text-decoration: underline;
       /* 
           coursor 鼠标样式，pointer、hand 都代表小手
               但是 hand 有兼容性问题，建议使用 pointer
        */
       cursor: pointer;
   }
   ```

## 列表样式

去除小圆点

HTML

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>列表样式</title>

    <style>
        /* 去除列表小圆点 */
        ul {
            list-style-type: none;
        }
    </style>
</head>
<body>
    <ul>
        <li>湖北
            <ul>
                <li>武汉</li>
                <li>襄阳</li>
            </ul>
        </li>
        <li>浙江
            <ul>
                <li>嘉善</li>
                <li>绍兴</li>
                <li>金华</li>
            </ul>
        </li>
    </ul>
</body>
</html>
```

CSS

```css
a {
    text-decoration: none;
}

#span1 {
    text-decoration: underline;
    /* 
        coursor 鼠标样式，pointer、hand 都代表小手
            但是 hand 有兼容性问题，建议使用 pointer
     */
    cursor: pointer;
}
```

## 绝对定位

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>绝对定位</title>

    <style>
        #div1 {
            background-color: lightpink;
            width: 300px;
            height: 300px;
            border-radius: 50%;
            position: absolute;
            left: 25%;
            top: 25%;
        }
    </style>
</head>
<body>
    <div id="div1"></div>
</body>
</html>
```

