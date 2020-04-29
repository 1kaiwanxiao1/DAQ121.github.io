---
title: Servlet学习笔记
tags: Servlet
categories: Java Web
toc: true
abbrlink: 441652674
date: 2020-02-08 13:01:53
---
java Web离不开Servlet，它其实就是一个java程序，运行在我们的web服务器上，用于**接收和响应** 客户端的http请求。 
<!--more-->
## 一、Web资源介绍
 - **在http协议当中，规定了请求和响应双方， 客户端和服务器端。与web相关的资源。**
 - 有两种分类：
 - 静态资源：html 、 js、 css
 - 动态资源：servlet/jsp


## 二、Servlet介绍
 - **servlet是什么?**
1. 其实就是一个java程序，运行在我们的web服务器上，用于**接收和响应** 客户端的http请求。 
2. 更多的是配合动态资源来做。 当然静态资源也需要使用到servlet，只不过是Tomcat里面已经定义好了一个 DefaultServlet


 ## 三、Selvlet简单使用Web工程
1. 得写一个Web工程 ， 要有一个服务器。

2. 测试运行Web工程：


```bash
得写一个Web工程 ， 要有一个服务器。
测试运行Web工程
	1. 新建一个类， 实现Servlet接口`public class HelloServlet implements Servlet{}`

	2. 配置Servlet ， 用意： 告诉服务器，我们的应用有这么些个servlet。
		在webContent/WEB-INF/web.xml里面写上以下内容：
		
		  <!-- 向tomcat报告， 我这个应用里面有这个servlet， 名字叫做HelloServlet , 具体的路径是com.itheima.servlet.HelloServlet -->
		  <servlet>
		  	<servlet-name>HelloServlet</servlet-name>
		  	<servlet-class>com.itheima.servlet.HelloServlet</servlet-class>
		  </servlet>
		  
		  <!-- 注册servlet的映射。  servletName : 找到上面注册的具体servlet，  url-pattern: 在地址栏上的path 一定要以/打头 -->
		  <servlet-mapping>
		  	<servlet-name>HelloServlet</servlet-name>
		  	<url-pattern>/a</url-pattern>
		  </servlet-mapping>

		3. 在地址栏上输入 http://localhost:8080/项目名称/a
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200226115856385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

## 四、Servlet执行过程
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200226120652999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

## 五、Servlet的通用写法
- 实现Servlet接口，但接口里的方法很多，有些用不到。
1. Servlet (接口)		
2. GenericServlet	
3. HttpServlet （用于处理http的请求）
		

-  定义一个类，继承HttpServlet ,复写doGet 和 doPost


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200226124900942.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
## 六、Servlet的生命周期方法
- 生命周期： 从创建到销毁的一段时间
- 生命周期方法： 从创建到销毁，所调用的那些方法。


```bash
1. init方法
	在创建该servlet的实例时，就执行该方法。
	一个servlet只会初始化一次， init方法只会执行一次
	默认情况下是 ： 初次访问该servlet，才会创建实例。 
		
2.service方法
	只要客户端来了一个请求，那么就执行这个方法了。
	该方法可以被执行很多次。 一次请求，对应一次service方法的调用
	
3.destroy方法
	servlet销毁的时候，就会执行该方法
			1. 该项目从tomcat的里面移除。
		  	2. 正常关闭tomcat就会执行 shutdown.bat

4. doGet 和 doPost不算生命周期方法。
所谓的生命周期方法是指，从对象的创建到销毁一定会执行的方法， 但是这两个方法，不一定会执行。
```	 
## 七、让Servlet创建实例的时机提前。
1. 默认情况下，只有在初次访问servlet的时候，才会执行init方法。 有的时候，我们可能需要在这个方法里面执行一些初始化工作，甚至是做一些比较耗时的逻辑。 

2. 那么这个时候，初次访问，可能会在init方法中逗留太久的时间。 为了提升用户的体验，需要让这个初始化的时机提前一点。 

3. 在配置的时候， 使用load-on-startup元素来指定， 给定的数字越小，启动的时机就越早。 一般不写负数， 从2开始即可。 

```java
<servlet>
	<servlet-name>HelloServlet04</servlet-name>
	<servlet-class>com.itheima.servlet.HelloServlet04</servlet-class>
	<load-on-startup>2</load-on-startup>
</servlet>
```
## 八、ServletConfig

 - **Servlet的配置，通过这个对象，可以获取servlet在配置的时候一些信息。**

```java
	//1. 得到servlet配置对象 专门用于在配置servlet的信息
	ServletConfig config = getServletConfig();
		
	//获取到的是配置servlet里面servlet-name 的文本内容
	String servletName = config.getServletName();
	System.out.println("servletName="+servletName);
		
	//2. 可以获取具体的某一个参数。 
	String address = config.getInitParameter("address");
	System.out.println("address="+address);
	
	//3.获取所有的参数名称
	Enumeration<String> names = config.getInitParameterNames();
	//遍历取出所有的参数名称
	while (names.hasMoreElements()) {
		String key = (String) names.nextElement();
		String value = config.getInitParameter(key);
		System.out.println("key==="+key + "   value="+value);	
	}
```

 - 参数在哪里配置？在web.xml中


```bash
<servlet>
	<servlet-name>aa</servlet-name>
	<servlet-class>com.daq.servlet.HelloServletConfig</servlet-class>
	
	<!-- 可以添加初始化参数 -->
	<init-param>
		<param-name>adress</param-name>
		<param-value>bejing</param-value>
	</init-param>
</servlet>
	
<servlet-mapping>
	<servlet-name>HelloServlet02</servlet-name>
	<url-pattern>/HelloServlet02</url-pattern>
</servlet-mapping>
```

## 九、为什么需要有ServletConfig？
1. 未来我们自己开发的一些应用，使用到了一些技术，或者一些代码，我们不会。 但是有人写出来了。做成了jar包，它的代码放置在了自己的servlet类里面。 

2. 刚好这个servlet 里面需要一个数字或者叫做变量值。 但是这个值不能是固定了。 所以要求使用到这个servlet的公司，在注册servlet的时候，必须要在web.xml里面，声明init-params
3. 在开发当中比较少用。
