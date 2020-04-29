---
title: 客户端会话技术：Cookie
tags: Cookie
categories: Java Web
toc: true
abbrlink: 887317365
date: 2020-02-09 13:01:53
---
类型为“小型文本文件”，是某些网站为了辨别用户身份，进行Session跟踪而储存在用户本地终端上的数据（通常经过加密），由客户端计算机暂时或永久保存的信息。
<!--more-->
## Cookie概述


- **概述：** 一份小数据， 是服务器给客户端，并且存储在客户端上的一份小数据
- **应用场景：** 自动登录、浏览记录、购物车。
- **为什么要有这个Cookie：**  http的请求是无状态。 客户端与服务器在通讯的时候，是无状态的，其实就是客户端在第二次来访的时候，服务器根本就不知道这个客户端以前有没有来访问过。 为了更好的用户体验，更好的交互 **[自动登录]**，其实从公司层面讲，就是为了更好的收集用户习惯 **[大数据]**。


## 如何使用Cookie


### 发送Cookie给客户端
1. 在响应的时候，添加cookie


```bash
Cookie cookie = new Cookie("aa", "bb");

	//给响应，添加一个cookie
	response.addCookie(cookie);
```
2. 客户端收到的信息里面，响应头中多了一个字段 Set-Cookie


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200228143218378.png)


### 获取客户端带过来的Cookie
```bash
//获取客户端带过来的cookie
Cookie[] cookies = request.getCookies();
	if(cookies != null){
		for (Cookie c : cookies) {
			String cookieName = c.getName();
			String cookieValue = c.getValue();
			System.out.println(cookieName + " = "+ cookieValue);
		}
	}
```
### 常用方法
```bash
//关闭浏览器后，cookie就没有了。 ---> 针对没有设置cookie的有效期。
//	expiry： 有效 以秒计算。
//正值： 表示 在这个数字过后，cookie将会失效。
//负值： 关闭浏览器，那么cookie就失效， 默认值是 -1
cookie.setMaxAge(60 * 60 * 24 * 7);
		
//赋值新的值
cookie.setValue(newValue);
		
//用于指定只有请求了指定的域名，才会带上该cookie
cookie.setDomain(".itheima.com");
		
//只有访问该域名下的cookieDemo的这个路径地址才会带cookie
cookie.setPath("/CookieDemo");
```
## 典型案例：
###  显示最近访问的时间

```bash
1. 判断账号是否正确
2. 如果正确，则获取cookie。 但是得到的cookie是一个数组， 我们要从数组里面找到我们想要的对象。
3. 如果找到的对象为空，表明是第一次登录。那么要添加cookie
4. 如果找到的对象不为空， 表明不是第一次登录。 

	if("admin".equals(userName) && "123".equals(password)){
		//获取cookie last-name --- >
		Cookie [] cookies = request.getCookies();
			
		//从数组里面找出我们想要的cookie
		Cookie cookie = CookieUtil.findCookie(cookies, "last");
			
		//是第一次登录，没有cookie
		if(cookie == null){
				
			Cookie c = new Cookie("last", System.currentTimeMillis()+"");
			c.setMaxAge(60*60); //一个小时
			response.addCookie(c);
				
			response.getWriter().write("欢迎您, "+userName);
				
		}else{
			//1. 去以前的cookie第二次登录，有cookie
			long lastVisitTime = Long.parseLong(cookie.getValue());
				
			//2. 输出到界面，
			response.getWriter().write("欢迎您, "+userName +",上次来访时间是："+new Date(lastVisitTime));
				
			//3. 重置登录的时间
			cookie.setValue(System.currentTimeMillis()+"");
			response.addCookie(cookie);
		}
	}else{
		response.getWriter().write("登陆失败 ");
	}
```
### 显示商品浏览记录
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200228173445822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- **jsp**
Java Server Pager ---> 最终会翻译成一个类， 就是一个Servlet

```bash
jsp中写java代码：
	<%
		只能写java代码
	%>
定义全局变量:
	<%! int a = 99; %>

定义局部变量:
	<% int b = 999; %>

在jsp页面上，显示 a 和 b的值:
	<%=a %> 
	<%=b %>
```
**删除浏览记录**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022818005011.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
**清除浏览记录**

-  其实就是清除Cookie， 删除cookie是没有什么delete方法的。只有设置maxAge 为0 。


```bash
	Cookie cookie = new Cookie("history","");
	cookie.setMaxAge(0); //设置立即删除
	cookie.setPath("/CookieDemo02");
	response.addCookie(cookie);
```

## 总结
1.服务器给客户端发送过来的一小份数据，并且存放在客户端上。

2.获取cookie， 添加cookie

	request.getCookie();

	response.addCookie();

3.Cookie分类


```bash
会话Cookie
	默认情况下，关闭了浏览器，那么cookie就会消失。

持久Cookie
	在一定时间内，都有效，并且会保存在客户端上。 
		cookie.setMaxAge(0); //设置立即删除
		cookie.setMaxAge(100); //100 秒
```
4.Cookie的安全问题。
由于Cookie会保存在客户端上，所以有安全隐患问题。  还有一个问题， Cookie的大小与个数有限制。 为了解决这个问题 ---> **Session .**



