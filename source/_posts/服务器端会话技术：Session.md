---
title: 服务器端会话技术：Session
date: 2020-02-09 13:01:53
tags: Session
categories: Java Web
---
Session对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的Web页之间跳转时，存储在Session对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。他是用来解决Cookie带来的安全问题。
<!--more-->
## 一、Session概述
- 会话 ， Session是基于Cookie的一种会话机制。 
- Cookie是服务器返回一小份数据给客户端。并且存放在客户端上。 
- Session是，数据存放在服务器端。


## 二、常用方法
```bash
	//得到会话ID
	String id = session.getId();
	
	//存值
	session.setAttribute(name, value);
		
	//取值
	session.getAttribute(name);
		
	//移除值
	session.removeAttribute(name);
```
## 三、Session的创建与销毁。
* 创建
如果有在servlet里面调用了 request.getSession()
* 销毁
session 是存放在服务器的内存中的一份数据。 当然可以持久化. Redis . 即使关了浏览器，session也不会销毁。
1. 关闭服务器
2. session会话时间过期。 有效期过了，默认有效期： 30分钟。

## 四、移除Session中的元素
```bash
	//强制干掉会话，里面存放的任何数据就都没有了。
	session.invalidate();
		
	//从session中移除某一个数据
	session.removeAttribute("cart");
```
## 五、总结
- Session： 也是基于cookie的一种会话技术，  数据存放存放在服务器端。


```bash
会在cookie里面添加一个字段 JSESSIONID . 是tomcat服务器生成。 
setAttribute 存数据
getAttribute 取数据
removeAttribute  移除数据
getSessionId();  获取会话id
invalidate() 强制让会话失效。
```
- 创建和销毁
	调用**request.getSesion**创建 
	 服务器关闭 ， 会话超时（30分）


- setAttribute 存放的值， 在浏览器关闭后，依然不会丢失！
