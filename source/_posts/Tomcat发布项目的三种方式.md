---
title: Tomcat发布项目的三种方式
date: 2020-02-07 13:01:53
tags: [web,Tomcat]
categories: Java Web
---
本文介绍Tomcat发布项目到服务器上的三种方式
<!--more-->
## 一、拷贝这个文件到webapps/ROOT底下， 在浏览器里面访问。

```bash
http://localhost:8080/stu.xml
```

- 在webaps下面新建一个文件夹xml  , 然后拷贝文件放置到这个文件夹中
- http://localhost:8080/xml/stu.xml
- http://localhost:8080 ： 对应的是到webapps/root
- http://localhost:8080/xml/ : 对应是 webapps/xml
- 使用IP地址访问：http://本机ip:8080/xml/stu.xml


 ##  二、配置虚拟路径 
 使用localhost：8080 打开tomcat首页， 找到Tomcat 8.5 Documentation,进入Reference，点击configuration ，找到左边的context，点击进入，复制此页面路径：

```bash
http://localhost:8080/docs/config/context.html
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225123552708.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 在conf/server.xml 找到host元素节点。加入以下内容。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200225124622947.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

 - 在浏览器地址栏上输入： http://localhost:8080/daq/person.xml


## 配置虚拟路径2
1. 在`tomcat/conf/catalina/localhost/` 文件夹下新建一个xml文件，名字可以自己定义。 `person.xml`

2. 在这个文件里面写入以下内容


```bash
<?xml version='1.0' encoding='utf-8'?>
<Context docBase="F：\XML2"></Context>
```


3. 在浏览器上面访问:


```bash
 http://localhost:8080/person/xml的名字即可
```

