---
title: Java - IntelliJ IDEA
date: 2022-06-06 11:24:41
categories: Java
tags: [IDEA, Crack, Tip, Command, Shortcut]
---

# Before Start

## 下载

### 软件本体

软件本体下载：[IDEA 官网下载地址](https://www.jetbrains.com/idea/)

> 如果打不开 Jetbrains 官网的话，可能是之前破解 Jetbrains 全家桶时，修改过本地的hosts文件，将 `0.0.0.0 account.jetbrains.com` 这句话删除即可

### 破解补丁

破解文件下载：[IDEA 破解文件友链](**https://www.idzd.top/usr/uploads/2020/01/2368964038.rar**)

## 安装

**Create Desktop Shortcut**：选择电脑系统位数（32/64）创建桌面快捷方式。

**Update PATH variable**（restart needed）：添加环境变量（需要重启）。

Update context menu：在右键菜单中添加open folder as project选项。-- 用处不大

Create Associations：文件关联（关联后该种文件会默认IDEA打开）。

Download and install JRE x86 by JetBrains：下载JRE （JetBrains运行时是一个运行时环境，用于在Windows，Mac OS X和Linux上运行基于IntelliJ平台的产品。） -- 用不到

## 激活

1. 将破解文件 **JetbrainsCrack.jar 放到一个目录**（随意目录即可），**记下目录的绝对路径**。

2. 选择试用30天，先打开IDEA

3. **修改 IDEA 配置文件**

   如果你是第一次打开IDEA，**选择 Configure -- Edit Custom VM Options**

   否则在 **IDEA 里面打开：Help  --  Edit Custom VM Options**

   在文件末尾加上破解jar包的绝对路径：

   ```
   -javaagent:[file_path]\JetbrainsCrack.jar
   ```

4. **修改本地hosts文件**

   **打开 hosts 文件**，**末尾添加一行代码**：

   （Windows 下路径为：`C:\Windows\System32\drivers\etc\hosts`）

   ```
   0.0.0.0 account.jetbrains.com
   ```

5. 重启IDEA，**选择 Help -- Register**

   激活方式为 License server，激活地址填：`http://fls.jetbrains-agent.com`

# Using

## Tip

设置字体：`File -> Settings -> Editor -> Font`

设置主题：`File -> Settings -> Editor -> Color Scheme`

IDEA 会自动保存

运行：

1. 代码上右键
2. 代码行左侧绿色箭头
3. 菜单栏绿色箭头

左侧目录窗口：

1. 上下键移动光标
2. 左键折叠目录
3. 右键展开目录

## Command

快速生成 main 方法：`psvm`

快速生成 System.out.println()：`sout`

## Shortcut

任何新建：`Alt + Insert`（包括文件、构造方法）

删除一行：`Ctrl + Y`

折叠目录栏：`Ctrl + Shift + F12`

切换文件：`Alt + 左右箭头`

运行：`Shift + F10`

打开功能栏：`Alt + 数字键`

查看传入参数：`Ctrl + P`（光标移入括号内）

注释：

1. 单行注释：`Ctrl + /`
2. 多行注释：`Ctrl + Shift + /`

定位方法/属性/变量：`Ctrl + 选中点击`

复制一行：`Ctrl + D`

纠正错误：`Alt + Enter`

搜索类：`双击 Shift`

快速定位属性或方法：`Ctrl + F12`

# Problem

## 关闭右上角 Git，切换为 VCS

`File -> Setting -> Version Control`

VCS 中 Git 切换为 none

## Unable to open debugger port

1. 修改服务器端口

2. cmd 搜索端口进程

   ```dos
   netstat -ano|findstr "[errorMsg port]"
   ```

   根据 pid 强制关闭该进程

   ```dos
   taskkill -f -pid [pid]
   ```

   
