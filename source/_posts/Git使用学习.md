---
title: Git使用学习
tags: git
categories: git+github
toc: true
abbrlink: 339957974
date: 2020-02-25 16:25:03
---
Git是一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。也是程序员最常用工具之一，连接本地仓库与github仓库，提高了开发效率，github也是开源项目宝库。
<!--more-->
## 一、概述原理
Git是目前世界上最先进的分布式版本控制工具。
- 工作原理：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325163944876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

- 概念：
1. Workspace：工作区
2. Index/Stage：暂存区
3. Respository：仓库区（本地仓库）
4. Remote：远程仓库

- SVN与Git的区别
1. SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而干活的时候，用的都是自己的电脑，所以首先要从中央服务器哪里得到最新的版本，然后干活，干完后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，如果在局域网还可以，带宽够大，速度够快，如果在互联网下，如果网速慢的话，有时就得等很久了。

2. Git是分布式版本控制系统，那么它就没有中央服务器的，每个人的电脑就是一个完整的版本库，这样，工作的时候就不需要联网了，因为版本都是在自己的电脑上。既然每个人的电脑都有一个完整的版本库，那多个人如何协作呢？比如说自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。


##二、使用目的

通过git管理github托管项目代码。
##三、下载安装

下载地址：[https://git-scm.com/download/win](https://git-scm.com/download/win)
##四、Git工作区域
 - 工作区（Working Directory）： 添加、编辑、修改文件。
 - 暂存区 ： 暂存已经修改的文件最后统一提交到git仓库中。 留一个回旋的余地，没改好接着改，改好了再提交到仓库。
 - Git Repository （Git 仓库）：最终确定的文件保留到仓库，成为一个新的版本，并且对他人可见。

##五、初始化配置 Git
 - 设置用户名：


```bash
git config --global user.name 'DAQ121'
```
 - 设置用户名邮箱：


```bash
git config --global user.email '2829025551@qq.com'
```
- 查看设置

```bash
git config --list
```

**注意：** 该设置在github仓库主页显示谁提交了该文件。
 - 创建相对应的仓库并初始化：

```bash
mkdir test;//创建仓库
cd test;
git init;//初始化仓库
操作完之后会在test目录下出现一个.git的隐藏文件。
```

 ##六、向仓库中添加文件

 - 在test仓库里新建文件a1.java

```bash
touch a1.java//创建
git status //查看
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200222170530502.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 添加到暂存区


```bash
git add a1.java
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200222170537166.png)
 - 将文件从暂存区提交到仓库

```bash
git commit -m 'add a1.java'
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200222170859545.png)
##七、修改仓库文件

 - 修改后，a1.java 还要add到暂存区中。
 - 然后再从暂存区提交到仓库。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200222172151350.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
##八、删除仓库文件

```bash
1.删除文件
rm a1.java
2.从Git中删除文件
git rm a1.java
3.提交操作
git commit -m '描述'
```

 ##九、Git管理远程仓库
 

 - 使用远程仓库的目的：
作用：备份，实现代码共享集中化管理。
 - **Git克隆操作**
目的：将远程仓库（github对应的项目）复制到本地

```bash
git clone 仓库地址
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200222174133376.png)

 - 将仓库里的内容克隆到本地，在本地修改之后，添加到暂存区，然后在提交到仓库。
 - **将本地仓库同步到git远程仓库**


```bash
git push
```
- 无法同步，没有权限怎么办？或者想要设置权限，防止乱入


```bash
vi .git/config
将[remote "origin"]
	url = https://github.com/用户名/仓库名.git
修改为：
[remote "origin"]
	url = https://用户名：密码@github.com/用户名/仓库名.git

```
