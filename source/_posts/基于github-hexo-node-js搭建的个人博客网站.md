---
title: 建站系列之---基于github搭建hexo网站
tags: hexo
categories: Hexo建站系列
toc: true
abbrlink: 167674660
date: 2020-03-15 16:11:58
---
日常在网上找答案的时候，发现了一种干干净净的，类似于个人主页的网站，就很好奇，后来深入了解了一下，原来这是基于hexo博客框架搭建起来的网站，用github的page服务托管博文，于是我也想上手整一个，于是乎，就花了前前后后大概半个月的时间，搭建起来网站，并买了域名部署到了服务器上面，此时怀着欣慰的心情，写下了这个系列！！！！
<!--more-->

**概述：** 本文主要讲的是基于github，利用hexo博客框架搭建网站。购买域名（9块）并绑定。实现效果：可以看到网站首页。
## 一、下载安装Git
下载地址：[https://git-scm.com/download/win](https://git-scm.com/download/win)
安装过程就不赘述了！
## 二、下载安装Node.js
下载地址：[http://nodejs.org/download/](http://nodejs.org/download/)
安装的时候，选择`add to path`，他会自动配置环境变量。
- 换镜像（亲身体验，可以省去以后好多麻烦）


```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 三、安装Hexo


 - 用cnpm全局安装，在桌面右键打开`Git bash here`输入：


```bash
cnpm install -g hexo-cli
```

 - 安装完成后，在命令行里输入`hexo -v`检查是否安装成功。


 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305155050299.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

## 四、配置hexo
```bash
基本命令了解：
	1. hexo -v			//查看版本		
	2. hexo init		//初始化一个项目
	3. cnpm install		
	4. hexo clean		//清理生成的文件
	5. hexo g			//生成静态网页文件，g是generate的缩写
	6. hexo s			//运行到服务器端，s是server的缩写
	7. hexo d			//部署到远端GitHub, d是deploy的缩写
	8. hexo s -p 5000	//在4000端口被占用的时候，修改指定端口
	9. cnpm uninstall hexo-cli -g       //卸载hexo
```

 - 在本地新建一个文件夹，我命名它为blog
 - 在这个文件夹下，右键打开`Git Bash here`，输入`hexo init`，我在这个地方出现了一个`WARN`，暂时不用管它。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305135143147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 继续输入`cnpm install`可以看到blog文件夹里出现：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305155919286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
```bash
目录介绍：
	1. node_modules #本地依赖模块
	2. public #hexo g生成的目录，包含静态网页文件，就是看到的博客
	2. source #源文件，用来存放我们的写好的博客源文件
	3. themes #主题文件夹，里面存放主题
	4. _config.yml #站点配置文件，用来配置博客具体的显示内容等
	5. db.json #存储一些用到的数据
	6.package.json #依赖配置

	.deploy_git #hexo s部署到远端的时候会生成的文件夹，是根据public文件夹生成的，内容是差不多的
```

 - 输入：`hexo g`
 - 输入：`hexo s` 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305135917128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 打开浏览器输入`localhost：4000`，可以看到部署好了，有一篇`hello World`的博客

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305154439378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

 - 到这一步，本地部署就已经成功了！！

## 五、连接到远端Github

 - 安装 `deployer` 插件


```bash
cnpm install hexo-deployer-git --save
```
 - 打开`F:\blog\_config.yml`,找到 `deploy`。修改如下，其中`repo`填写你自己的仓库名字。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305161344694.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 生成公私钥


```bash
	ssh-keygen -t rsa -C "注册git使用的邮箱"
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305161705454.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 找到生成的 `id_rsa.pub` 公钥文件（在`C:\Users\pc\.ssh`下），复制公钥内容。
 - 到Github你的网站仓库界面：`Setting -> Deploy keys -> add deploy key -> Add
   key`，选择`Allow write access`（允许推送），这样方便以后推送的时候，不用每次都输入密码。
 - 开始推送


```bash
hexo deploy
```
这样在博客和github都可以看到推送的内容。
## 六、购买绑定域名

阿里云：[https://wanwang.aliyun.com/domain/](https://wanwang.aliyun.com/domain/)
- 在阿里云或者腾讯云购买一个域名，我买的是阿里云的`.top`后缀名的，后缀名不同，价格不同，top最便宜，一年只要九块。
- 如何买域名以及解析域名不做描述，注意要是实名认证，解析域名的意思就是，把ip地址和域名做一个映射，它让你填的`ip地址`就是你github网站仓库的`ip`。如何得到这个`ip`：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305163101123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- 填入`ip`，完成解析。
- 在`F:\blog\source`下新建一个`CNAME.txt`,在里面写入你的域名，不要前缀，如：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305163539152.png)
- 去掉`.txt`后缀

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305163544455.png)


- 绑定域名，到github，找到你的仓库，找到`Setting`，一直拉到最先面下面。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305164627522.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)


![在这里插入图片描述](https://img-blog.csdnimg.cn/2020030516473779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- 这样就绑定好了。


```bash
每次部署的执行次序
	1. hexo clean
 	2. hexo g
	3. hexo d
```


- 执行一次，查看效果。
