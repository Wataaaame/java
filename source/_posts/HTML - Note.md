---
title: HTML - Note
date: 2022-07-20 15:45:59
categories: Front End
tags: HTML
---

# 系统架构

## B/S 架构

Browser / Server（浏览器/服务器的交互形式）

Browser 支持的语言：HTML CSS JavaScript

Server 端支持的语言：C C++ Java Python

优点：升级方便，只升级服务器端代码

缺点：速度慢、体验差、界面简单

> 企业内部的解决方案都是采用 B/S 架构的系统，只需要做到数据的增删改查即可，且企业内部更注重的是开发成本

## C/S 架构

Client / Server（客户端/服务器端的交互形式）

优点：速度快、体验好、界面炫酷（娱乐型系统）

缺点：升级麻烦，维护成本较高

# HTML

Hyper Text Markup Language（超文本标记语言）

由大量标签组成，每一个标签都有开始标签和结束标签

```html
<标签>
    <标签 属性名="属性值"></标签>
</标签>
```

- 超文本：流媒体、图片、声音、视频等

- 开发使用普通文本编辑器就行，扩展名为 `.html`，专业开发工具：DreamWeaver、HBuilder等
- 运行使用浏览器打开 HTML 文件就是运行

## 制定者

W3C（World Wide Web）：世界万维网联盟制定了 HTML 规范，例如：HTML / XML / http / https...

提供帮助文档的网站：

https://www.w3school.com.cn/

## 第一个 HTML 网页

```html
<!--
	1. 这是 HTML 的注释
	2. 加上以下代码的第一行就表示使用 HTML5 的语法，去掉则为 HTML4
	3. HTML 不区分大小写，语法松散不严格
-->
<!doctype html>

<!-- 根 -->
<html>
    
    <!-- 头 -->
    <head>
        <!-- 网页标题，显示在网页左上角 -->
        <title>The Title</title>
    </head>
    
    <!-- 体 -->
    <body>
        Body, the main part of web
    </body>
    
</html>
```

## 基本标签

```html
<!doctype html>
<html>
	<head>
		<title>HTML 的基本标签</title>
	</head>
	<body>
		<!-- 段落标记 -->
		<p>《红楼梦》，别名《石头记》等，中国古代章回体长篇小说，中国古典四大名著之一，通行本共120回，一般认为前80回是清代作家曹雪芹所著，后40回作者为无名氏，整理者为程伟元、高鹗。小说以贾、史、王、薛四大家族的兴衰为背景，以富贵公子贾宝玉为视角，以贾宝玉与林黛玉、薛宝钗的爱情婚姻悲剧为主线，描绘了一些闺阁佳人的人生百态，展现了真正的人性美和悲剧美，可以说是一部从各个角度展现女性美以及中国古代社会百态的史诗性著作。</p><p>《红楼梦》版本有120回“程本”和80回“脂本”两大系统。程本为程伟元排印的印刷本，脂本为脂砚斋在不同时期抄评的早期手抄本。脂本是程本的底本。</p><p>《红楼梦》是一部具有世界影响力的人情小说、中国封建社会的百科全书、传统文化的集大成者。其作者以“大旨谈情，实录其事”自勉，只按自己的事体情理，按迹循踪，摆脱旧套，新鲜别致，取得了非凡的艺术成就。“真事隐去，假语存焉”的特殊笔法更是令后世读者脑洞大开，揣测之说久而遂多。二十世纪以来，《红楼梦》更以其丰富深刻的思想底蕴和异常出色的艺术成就使学术界产生了以为研究对象的专门学问——红学。</p>

		<!-- 标题字 -->
		<h1>h2标题字</h1>
		<h2>h2标题字</h2>
		<h3>h3标题字</h3>
		<h4>h4标题字</h4>
		<h5>h5标题字</h5>
		<h6>h6标题字</h6>

		<!-- 换行标记，br 标签是一个独目标签 -->
		Hello<br>World

		<!-- 横线，独木标记 -->
		<hr>
		<!-- color 和 width 都是 hr 标签的属性 -->
		<hr color="red" width="50%">

		<!-- 预留格式 -->
		<pre>
			for(int i = 0; i < 10; i++) {
				System.out.println(i);
			}
		</pre>

		<del>删除字</del>

		<ins>插入字</ins>

		<b>粗体字</b>

		<i>斜体字</i>

		右上角加字<sup>2</sup>

		右下角加字<sub>3</sub>

		<font color="blue" size="50">字体标签</font>
	</body>
</html>
```

## 实体符号

```html
<!doctype html>
<html>
	<head>
		<title>实体符号</title>
	</head>
	<body>
		<!-- 实体符号的特点是：以&开始
			&lt; 小于号
			&gt; 是大于号
			&nbsp; 空格
		-->
		b<a>c
		b&lt;a&gt;c
		a&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b
	</body>
</html>
```

## 表格

```html
<!doctype html>
<html>
	<head>
		<title>表格</title>
	</head>
	<body>
		<!-- 三行三列的表格 -->
		<!--
			border 设置表格边框像素
			width 设置宽度
			height 设置高度
			align 设置水平对齐方式
		-->
		<table border="1px" width="60%" height="150px">
			<tr align="center">
				<td>a</td>
				<td>b</td>
				<td>c</td>
			</tr>
			<tr>
				<td>d</td>
				<td>e</td>
				<td>f</td>
			</tr>
			<tr>
				<td>x</td>
				<td>y</td>
				<td>z</td>
			</tr>
		</table>
	</body>
</html>
```

### 单元格合并

```html
<!doctype html>
<html>
	<head>
		<title>表格单元格合并</title>
	</head>
	<body>
		<table border="1px" width="20%">
			<tr>
				<td>1</td>
				<td>2</td>
				<td>3</td>
			</tr>
			<tr>
				<td>4</td>
				<td>5</td>
				<!-- row 级合并：（6和9合并）删除后者，前者加上 rowspan="2" -->
				<td rowspan="2">69</td>
			</tr>
			<tr>
				<!-- column 级合并：（7和8合并）无删除顺序，加上 colspan="2" -->
				<td colspan="2">78</td>
				<!--
				<td>9</td>
				-->
			</tr>
		</table>
	</body>
</html>
```

### th 标签

```html
<tr>
	<th>th标签</th>
    <th>比 td 多了居中加粗</th>
</tr>
```

### thead, tbody and tfoot

```html
<!doctype html>
<html>
	<head>
		<!-- table 中不是必须的，只是方便后期 js 中的代码编写 -->
		<title>HTML单元格-thead tbody tfoot</title>
	</head>
	<body>
		<table border="1px" width="50%">

			<!-- 头 -->
			<thead>
				<tr>
					<th>1 group</th>
					<th>2 group</th>
					<th>3 group</th>
				</tr>
			</thead>

			<!-- 体 -->
			<tbody>
				<tr>
					<td>1</td>
					<td>2</td>
					<td>3</td>
				</tr>
			</tbody>

			<!-- 脚 -->
			<tfoot>
				<tr>
					<td>4</td>
					<td>5</td>
					<td rowspan="2">69</td>
				</tr>
				<tr>
					<td colspan="2">78</td>
				</tr>
			</tfoot>

		</table>
	</body>
</html>
```

## 背景颜色与背景图片

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- 告诉浏览器打开文件采用的编码方式，并非设置文件编码方式-->
    <meta charset="UTF-8">
    <title>背景颜色和背景图片</title>
</head>
<!-- 
    对背景进行设置
    背景颜色：bgcolor
    背景图片：background
 -->
<body bgcolor="green" background="img/IMG_2160.JPG">
    
</body>
</html>
```

## 图片

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图片img</title>
</head>
<body>
    <!-- 
        1. 设置图片宽高时，只需指定宽，高度会自动缩放
        2. title 设置鼠标悬停时显示的信息
        3. alt 设置图片加载失败时显示的提示信息
     -->
    <img src="img/dona.jpg" width="500px" title="dona">
    <br><br><br>
    <img src="img/dona.j" alt="not found">
</body>
</html>
```

## 超链接

通过超链接可以从浏览器向服务器发送请求（request）

服务器向浏览器发送数据称为响应（response）

B/S 结构的系统，每一个请求都会对应一个响应

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图片img</title>
</head>
<body>
    <!-- 
        1. 设置图片宽高时，只需指定宽，高度会自动缩放
        2. title 设置鼠标悬停时显示的信息
        3. alt 设置图片加载失败时显示的提示信息
     -->
    <img src="img/dona.jpg" width="500px" title="dona">
    <br><br><br>
    <img src="img/dona.j" alt="not found">
</body>
</html>
```

## 表单

收集用户的信息，用户填写表单，点击提交按钮提交数据到服务器

表单使用 form 标签，一个网页可以有多个表单

表单中的 action 属性填写提交地址（同 href 属性）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>表单</title>
</head>
<body>
    <!-- 请求路径，数据最终提交给 192.168.111.3 上的 8080 端口对应的软件 -->
    <form action="http://192.168.111.3:8080/oa/save">
        <!-- 画一个提交按钮，该按钮可以提交表单 -->
        <!-- 画按钮可以使用 input 输入域，type="submit" 时代表提交按钮，具有提交表单的能力 -->
        <input type="submit" value="设置按钮显示文本">
    </form>

    <br><br><br><br><br>

    <!-- 
        表单提交格式
            http://localhost:8080/test/login?loginName=222&loginPwd=222
            格式：action?name=value&name=value...
            表单中有 name 属性，一律提交
            若不想提交某字段，不要提供 name 属性
            当 value 未填写时，会提交空字符串
     -->
    <form action="http://localhost:8080/test/login">
        <table>
            <tr>
                <td>用户名</td>
                <!-- 此中 value 属性等待用户填写 -->
                <td><input type="text" name="loginName"></td>
            </tr>
            <tr>
                <td>密码</td>
                <td><input type="password" name="loginPwd"></td>
            </tr>
            <tr align="center">
                <td colspan="2">
                    <!-- 此中 value 用以改变显示文本，不设置 name 属性避免提交 -->
                    <input type="submit" value="login">
                    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;
                    <input type="reset" value="reset">
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

用户注册表单示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <!-- method="post" 可以让表单在浏览器地址栏隐藏（get 会显示） -->
    <form action="http://localhost:8080/test/login" method="post">
        用户名
        <input type="text" name="loginName">
        <br>

        密码
        <input type="password" name="loginPwd">
        <br>

        确认密码
        <input type="password">
        <br>

        <!-- 无用户输入的 input 需要手动选择 value -->
        性别
        <input type="radio" name="sex" value="1">男
        <input type="radio" name="sex" value="0">女
        <br>

        爱好
        <!-- 加上 checked 则默认选中 -->
        <input type="checkbox" name="habit" value="sing" checked>唱歌
        <input type="checkbox" name="habit" value="dancing">跳舞
        <input type="checkbox" name="habit" value="sport">运动
        <br>

        学历
        <select name="grade">
            <!-- 默认选中为 selected -->
            <option value="">--请选择--</option>
            <option value="gz">高中</option>
            <option value="dz">大专</option>
            <option value="bk">本科</option>
            <option value="ss">硕士</option>
        </select>
        <br>

        简介
        <!-- 文本域 -->
        <textarea name="introduce" cols="30" rows="10"></textarea>
        <br>

        <input type="submit" value="注册">
        <input type="reset" value="重置">
    </form>
    <!-- http://localhost:8080/test/login?loginName=vv&loginPwd=11&sex=0&habit=sing&grade=dz&introduce=vv -->

    <!-- 
        超链接也可以提交数据给服务器，但是提交的数据固定不变
        只能是 get 请求
     -->
     <a href="http://localhost:8080/test/login?loginName=vv"></a>
</body>
</html>
```

下拉列表支持多选：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>下拉列表支持多选</title>
</head>
<body>
    <!-- 
        使用 multiple 属性支持多选（按住 Ctrl）
        size 固定显示条数
     -->
    <select name="武汉市" multiple="multiple" size="2">
        <option>武昌区</option>
        <option>汉口区</option>
        <option>汉阳区</option>
    </select>
</body>
</html>
```

### file 控件

```html
<!-- 文件上传专用 -->
<input type="file">
```

### 隐藏域

```html
<!-- 隐藏域：网页上不显示，但表单提交会自动传给服务器 -->
<input type="hidden" name="userid" value="111">
```

### readonly & disabled

都是数据只读不能修改，但是 readonly 可以提交给服务器，disabled 数据不会提交（即使有 name 属性）

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>readonly disabled</title>
</head>
<body>
    <form action="http://localhost:8080/test">
        用户id
        <input type="text" name="userid" value="101" readonly>
        <br>
        
        用户名
        <input type="text" value="vv" disabled>
        <br>

        <input type="submit" value="提交数据">
    </form>
</body>
</html>
```

### input 控件的 maxlength

```html
<!-- 最多输入的字符数量 -->
<input type="text" maxlength="3">
```

## 元素 id 属性

id 属性的存在，更方便获取元素

> DOM（Document）树
>
> HTML 类似一颗树，每一个节点都有唯一的 id
>
> JavaScript 语言可以对 HTML 中的任意节点进行增删改操作，则使用 id 获取对象

HTML 文档中，任何元素（节点）都有 id 属性，id 属性是该节点的唯一标识

> id 就是节点身份证号

表单提交数据时与 id 属性无关

## div & span

都可以称为“图层”，作用于页面的灵活布局

> 最早网页采用 table 布局，不够灵活
>
> 现代网页开发中 div 布局使用最多

图层类比盒子，div 嵌套 div 就是盒子嵌套盒子

div 和 span 可以定位，只要确定左上角坐标即可

div 默认独占一行，span 不会独自占一行

## base

设置整个网页的基础路径，自动填充到没有以斜杠开始的路径，通常配置在 head 标签中

```html
<head>
    <meta charset="uft-8">
    <title>Dept</title>
    <base href="http://localhost:8080/oa/">
</head>
<body>
	<a href="dept/list">list</a>
    <!-- http://localhost:8080/oa/dept/list -->
</body>
```

- 不能应用于 Java 的 taglib 标签

结合 EL 动态获取 base：

```html
<base href="${pageContext.request.scheme}://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
```

