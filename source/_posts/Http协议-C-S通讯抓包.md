---
title: Http协议&C/S通讯抓包
date: 2020-02-08 13:01:53
tags: Http协议
categories: Java Web
---
http协议：针对网络上的客户端 与 服务器端在执行http请求的时候，遵守的一种规范。 规定了客户端在访问服务器端的时候,服务器端返回数据的时候，要带上什么东西。
<!--more-->
# 一、Http协议

 - 什么是协议？
双方在交互、通讯的时候， 遵守的一种规范、规则。
 -  http协议概述
针对网络上的客户端 与 服务器端在执行http请求的时候，遵守的一种规范。 其实就是规定了客户端在访问服务器端的时候，要带上哪些东西， 服务器端返回数据的时候，也要带上什么东西。 
 - 版本
	1.0：请求数据，服务器返回后， 将会断开连接。
	1.1：请求数据，服务器返回后， 连接还会保持着。 除非服务器 | 客户端 关掉。 有一定的时间限制，如果都空着这个连接，那么后面会自己断掉。
	
# 二、演示客户端 如何 与服务器端通讯。
问题： 在地址栏中键入网络地址 回车  或者是平常注册的时候，点击了注册按钮 ， 浏览器都能显示出来一些东西。那么背地里到底浏览器和服务器是怎么通讯。 它们都传输了哪些数据？
 - 安装抓包工具 HttpWatch (IE插件)

 - 打开tomcat. 输入localhost:8080 打开首页

 - 在首页上找到Example--->选择 Servlet Examples---> Request Parameter
 - 接着点击Request  Parameters 的 Execute超链接，会出现请求参数示例
 - 执行tomcat的例子，然后查看浏览器和 tomcat服务器的对接细节


# 三、Http请求数据解释
 - **请求的数据里面包含三个部分内容 ： 请求行 、 请求头 、请求体**
 - 请求行：


```bash
POST /examples/servlets/servlet/RequestParamExample HTTP/1.1 

		POST ： 请求方式 ，以post去提交数据
			
		/examples/servlets/servlet/RequestParamExample
		请求的地址路径 ， 就是要访问哪个地方。
	
		HTTP/1.1 协议版本
```

 - 请求头：

```bash
Accept: application/x-ms-application, image/jpeg, application/xaml+xml, image/gif, image/pjpeg, application/x-ms-xbap, */*
		Referer: http://localhost:8080/examples/servlets/servlet/RequestParamExample
		Accept-Language: zh-CN
		User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)
		Content-Type: application/x-www-form-urlencoded
		Accept-Encoding: gzip, deflate
		Host: localhost:8080
		Content-Length: 31
		Connection: Keep-Alive
		Cache-Control: no-cache

		Accept: 客户端向服务器端表示，我能支持什么类型的数据。 
		Referer ： 真正请求的地址路径，全路径
		Accept-Language: 支持语言格式
		User-Agent: 用户代理 向服务器表明，当前来访的客户端信息。 
		Content-Type： 提交的数据类型。经过urlencoding编码的form表单的数据
		Accept-Encoding： gzip, deflate ： 压缩算法 。 
		Host ： 主机地址
		Content-Length： 数据长度
		Connection : Keep-Alive 保持连接
		Cache-Control ： 对缓存的操作
```
 - 请求体：


```bash
浏览器真正发送给服务器的数据 
	
	发送的数据呈现的是key=value ,如果存在多个数据，那么使用 &

	firstname=zhang&lastname=sansan
```
# 四、Http响应数据解析

 - **请求的数据里面包含三个部分内容 ： 响应行 、 响应头 、响应体**


```bash
	HTTP/1.1 200 OK
	Server: Apache-Coyote/1.1
	Content-Type: text/html;charset=ISO-8859-1
	Content-Length: 673
	Date: Fri, 17 Feb 2017 02:53:02 GMT

	...这里还有很多数据...
```

-  响应行：第一行

```bash
HTTP/1.1 200 OK

		协议版本  
		状态码 
			咱们这次交互到底是什么样结果的一个code. 
			200 : 成功，正常处理，得到数据。
			403  : for bidden  拒绝
			404 ： Not Found
			500 ： 服务器异常
		OK
			对应前面的状态码  
```

* 响应头


```bash
Server:  服务器是哪一种类型。  Tomcat
		Content-Type ： 服务器返回给客户端你的内容类型
		Content-Length ： 返回的数据长度
		Date ： 通讯的日期，响应的时间		
```

# 五、Get 和  Post请求区别
- post
1. 数据是以流的方式写过去，不会在地址栏上面显示。  现在一般提交数据到服务器使用的都是POST。
2. 以流的方式写数据，所以数据没有大小限制。

-  get

1. 会在地址栏后面拼接数据，所以有安全隐患。 一般从服务器获取数据，并且客户端也不用提交上面数据的时候，可以使用GET。
2. 能够带的数据有限， 1kb大小。