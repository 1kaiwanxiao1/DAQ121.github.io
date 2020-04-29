---
title: 认识web服务器&Eclipse配置部署Tomcat
tags:
  - web
  - Tomcat
categories: Java Web
toc: true
abbrlink: 629938038
date: 2020-02-07 13:01:53
---
Web服务器一般指网站服务器，是指驻留于因特网上某种类型计算机的程序，可以向浏览器等Web客户端提供文档，也可以放置网站文件，让全世界浏览；可以放置数据文件，让全世界下载。目前最主流的三个Web服务器是Apache、 Nginx 、IIS。
<!--more-->
## 一、程序架构
 - C /  S (client / server)
如：QQ  微信   LOL
优点：有一部分代码写在客户端，用户体验比较好。
缺点：占用资源大，比较吃硬盘，服务器更新，客户端也要随之更新。
 - B / S (browser / server)
如：网页QQ，  网页游戏。
优点：客户端只要浏览器就可以了，占用资源小，不用更新。
缺点：用户体验不佳。

## 二、web服务器

 - **服务器：其实服务器就是一台电脑。 配置比一般的要好。**
 - Web服务器软件 ：客户端在浏览器的地址栏上输入地址 ，然后web服务器软件，接收请求，然后响应消息。 


 - 处理客户端的请求， 返回资源 | 信息


 - Web应用  需要服务器支撑。 index.html


 - 常用web服务器：
	Tomcat  -----apache 免费
	WebLogic -----BEA  收费
	Websphere -----IBM 收费
	IIS   -----微软
## 三、安装tomcat
 - 直接解压 ，然后找到bin/startup.bat
 - 安装启动之后，如果能够正常看到黑窗口，表明已经成功安装。 为了确保万无一失， 最好在浏览器的地址栏上输入 ： http://localhost:8080 , 如果有看到内容 就表明成功了。在黑窗口开着的同时输入网址。
 - 出现控制台乱码问题，右键控制台，查看是GBK，还是UTF-8。然后在安装目录中找到conf--logging.properties文件，修改里面的编码方式，保持一致即可。
 - 如果双击了startup.bat,  看到一闪而过的情形，一般都是 JDK的环境变量没有配置。
## 四、Tomcat目录介绍

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225115238417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

 - bin： 包含了一些jar ,  bat文件 。  startup.bat。

 - conf：tomcat的配置   server.xml ，  web.xml。

 - lib：tomcat运行所需的jar文件。

 - logs：运行的日志文件。

 - temp：临时文件。

 - webapps：发布到tomcat服务器上的项目，就存放在这个目录。

 - work：jsp翻译成class文件存放地。


## 五、Eclipse配置Tomcat

1. Java EE 的视图下，在server里面，右键新建一个服务器， 选择到apache分类， 找到对应的tomcat版本， 接着一步一步配置即可。
2. 配置完毕后， 在server 里面， 右键刚才的服务器，然后open  ， 找到上面的Server Location , 选择中间的 Use Tomcat installation...
3. 创建web工程， 在WebContent下新建html文件， 右击工程， run as server 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225140218529.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
4. 至此成功！！！
