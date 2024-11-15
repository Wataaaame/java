---
title: Blog - Hexo+Fluid & GitHub 搭建个人博客
date: 2022-03-23 14:12:40
tags: [Hexo, Fluid, GitHub]
categories: Blog
---

# Introduction

Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

本文章演示的过程基于虚拟机Windows 7旗舰版系统。

# Before Starting

1. 注册GitHub账户

   官网地址：[GitHub](https://github.com/)

2. 下载所需工具（设置好安装目录后，一路下一步）

   nodejs（win7支持版本：node-v13.14.0-x64）

   git（Git-2.34.0-64-bit）

3. 安装完毕后，使用快捷键`win+R`，输入`cmd`打开命令行，并分别输入以下命令来测试是否安装成功

      ```bash
   node -v
   npm -v
   git --version
   ```
   
4. 安装淘宝源cnpm

      ```bash
      npm install -g cnpm --registry=https://registry.npm.taobao.org
      ```

5. 下载Hexo

      ```bash
      npm install hexo-cli -g
      ```

6. 检查Hexo版本号验证是否安装成功

      ```bash
      hexo -v
      ```

# Work it out

## 关联SSH

1. 登录GitHub

2. 新建一个Repository

   名称：`[user_name].github.io`
   
3. 桌面右键，点击`git bash here`

4. 输入ssh检测ssh是否安装

   ```bash
   ssh
   ```

5. 使用命令行创建ssh key

   ```bash
   ssh-keygen -t rsa -C "[user_mail]"
   ```

   **连续按4次回车**

6. 进入到`C:\user\[name]\.ssh\id_rsa.pub`（用记事本打开，复制密钥）

7. 找到`GitHub -> Setting -> SSH and GPG keys`

8. 新建一个`SSH Key`（名称随意）

9. 粘贴复制出来的Key

10. 在`Git`中输入命令测试ssh是否绑定成功

    ```bash
    ssh -T git@github.com
    ```

    **# 错误提示：Time out**

    > 进入~/.ssh下，创建config
    >
    > ```bash
    > vi config
    > ```
    >
    > 编辑文件内容
    >
    > ```
    > Host github.com
    > User git
    > Hostname ssh.github.com
    > PreferredAuthentications publickey
    > IdentityFile ~/.ssh/id_rsa
    > Port 443
    > 
    > Host gitlab.com
    > Hostname altssh.gitlab.com
    > User git
    > Port 443
    > PreferredAuthentications publickey
    > IdentityFile ~/.ssh/id_rsa
    > ```

## 创建本地博客

1. 新建一个目录（用于存放博客文件夹），进入到目录，右键`git bash here`

2. 初始化hexo

   ```bash
   hexo init
   ```

3. 生成本地页面，进入本地端口预览效果

   ```bash
   # hexo server
   hexo s
   ```

### 主题配置（以Floid为例）

**# 第三方开源主题可以自己按照文档配置**

Hexo 5.0.0 版本以上，推荐通过 npm 直接安装，进入博客目录执行命令：

```bash
npm install --save hexo-theme-fluid
```

#### 指定主题

如下修改 Hexo 博客目录中的 `_config.yml`：

```yaml
theme: fluid  # 指定主题

language: zh-CN  # 指定语言，会影响主题显示的语言，按需修改
```

#### 创建「关于页」

首次使用主题的「关于页」需要手动创建：

```bash
hexo new page about
```

创建成功后修改 `/source/about/index.md`，添加 `layout` 属性。

修改后的文件示例如下：

```yaml
---
title: 标题
layout: about
---

这里写关于页的正文，支持 Markdown, HTML
```

> WARNING
>
> `layout: about` 必须存在，并且不能修改成其他值，否则不会显示头像等样式。

#### 更新主题

在博客目录下执行命令：

```bash
npm update --save hexo-theme-fluid
```

详情请见：[Hexo Floid 用户手册](https://hexo.fluid-dev.com/docs/)

## 发布到GitHub

### deployment文件配置

1. blog目录找到`_config.yml`

2. 修改最后一行`deployment`配置

   ```yml
   deploy:
   	type: git
   	repository: [仓库ssh地址] # 在仓库页code下找到链接
   	branch: main
   ```

### 部署到GitHub

blog文件夹右键`git bash here`

1. 安装hexo-deployer-git自动部署发布工具

   ```bash
   npm install hexo-deployer-git --save
   ```

2. 生成blog页面命令

   ```bash
   # hexo generate
   hexo g
   ```

3. 部署到GitHub命令

   ```bash
   # hexo deploy
   hexo d
   ```

4. 部署完成后，在浏览器输入页面地址已访问blog

   ```
   https://[username].github.io
   ```

   **# 如果访问不了，可以刷新dns缓存**

   > cmd控制台输入
   >
   > ```
   > ipconfig/flushdns
   > ```


# After Building

## 文章创建

- 创建markdown文件

  ```
  hexo new [layout[post, draft, page]] "[title]"
  ```

  > layout布局：**post（文章）**、draft（草稿）、page（页面）
  >
  > - 启动服务器时加上`--draft`来查看草稿
  >
  >   ```
  >   hexo server --draft
  >   ```
  >
  > - 还可以在站点配置文件中把 `render_drafts` 参数设为 `true` 来预览草稿。
  >
  > - 我们可以通过 `publish` 命令将草稿发布文章或者页面，它将会被移动到指定的文件夹。
  >
  >   ```
  >   hexo publish [layout] "[title]"
  >   ```

- Front-matter

  当我们创建一个md文件后，打开后会看到一些内容，这些称为`Front-matter`，它是文件最上方以 `---` 分隔的区域，用于指定个别文件的变量，举例来说：

  ```markdown
  ---
  title: Hello World # 标题就是我们上面创建的时候指定的名字
  date: 2013/7/13 20:46:25 # 文件创建的时间
  ---
  ```

  > 在`Typora`中我们在md文件的首行（必须是第一行）输入`---` ，然后按回车就可以插入`Front-matter`了。

- **Front-matter预定义参数**

  ```markdown
    layout  布局  默认为true，如果你不想你的文章被处理，可以设置为false
    title  标题  标题会显示在最上方居中位置     
    date  建立日期    如果不指定则为默认值-文件创建日期，可以自定义。
    update  更新日期  如果不指定则为默认值-文件修改后重新生成静态文件的日期。
    comments  是否开启文章的评论功能 默认值为true
    tags  标签（不适用于页面page布局）
    categoreies  分类（不适用于页面page布局）
    permalink  覆盖文章网址
    keywords  仅用于 meta 标签和 Open Graph 的关键词（不推荐使用）
    excerpt	用于Fluid主题中的摘要
    comment bool	开启评论
  ```


## 常用命令

- **清除缓存：**`hexo clean`

- **生成静态文件：**`hexo generate`可简写为`hexo g`

- **启动本地服务器：**`hexo server`可简写为`hexo s`，常用参数`-p（--port）`重设端口

- **部署到云服务器：**`hexo deploy`可简写为`hexo d`，用于将网站部署到服务器上。
  常用参数：`-g（--generate）`，`hexo d -g`部署前预先生成静态文件，等同于 `hexo g -d`
  
  > **一般发布文章或者修改博客后需要这些操作：**清除缓存>生成静态文件>启动服务器，测试没问题后再部署。
  >
  > ```
  > # 我们可以写成一条命令
  > hexo clean && hexo g && hexo s
  > hexo d
  > ```
  >
  > 详情请见：[Hexo官方文档](https://hexo.bootcss.com/docs/)
