---
title: JSP&EL&JSTL
tags:
  - JSP
  - EL
  - JSTL
categories: Java Web
toc: true
abbrlink: 1540231261
date: 2020-02-11 13:01:53
---

JSP，EL表达式，JSTL介绍
<!--more-->
# 一、JSP
## 什么是jsp？
**Java Server Page**  从用户角度看待 ，就是是一个网页 ， 从程序员角度看待 ， 其实是一个java类， 它继承了servlet，所以可以直接说jsp 就是一个Servlet.
## 为什么会有jsp?
为了有更好的用户体检，更好的交互效果。因为html 多数情况下用来显示静态内容 ， 一成不变的。 但是有时候我们需要在网页上显示一些动态数据， 比如： 查询所有的学生信息， 根据姓名去查询具体某个学生。  这些动作都需要去查询数据库，然后在网页上显示。 html是不支持写java代码  ， **jsp里面可以写java代码。** 
##  jsp怎么用？
### 一、jsp指令
**一、page指令**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200229121757727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

```bash
指令写法：
<%@ 指令名字 %>

page指令：
1. language：表明jsp页面中可以写java代码

2. contentType：其实就是说这个文件是什么类型，告诉浏览器我是什么内容类型，以及使用什么编码
	contentType="text/html; charset=UTF-8"
	text/html  MIMEType 这是一个文本，html网页

3. pageEncoding：jsp内容编码

4. extends 用于指定jsp翻译成java文件后，继承的父类是谁，一般不用改。

5. import 导包使用的，一般不用手写。

6. session 
 - 值可选的有true or false
 - 用于控制在这个jsp页面里面，能够直接使用session对象。
 - 具体的区别是，请看翻译后的java文件：
	如果该值是true , 那么在代码里面会有getSession（）的调用，
	如果是false :  那么就不会有该方法调用，也就是没有session对象了。在页面上自然也就不能使用session了。

7. errorPage： 指的是错误的页面， 值需要给错误的页面路径

8. isErrorPage：上面的errorPage 用于指定错误的时候跑到哪一个页面去。 那么这个isErroPage , 就是声明某一个页面到底是不是错误的页面。
```

**二、include指令**
- 包含另外一个jsp的内容进来。


```bash
<%@ include file="other02.jsp"%>
```
- 背后细节:
把另外一个页面的所有内容拿过来一起输出。 所有的标签元素都包含进来。

**三、taglib**

```bash
<%@ taglib prefix=""  uri=""%>  
	uri: 标签库路径
	prefix : 标签库的别名  
```

### 二、jsp动作标签（写在body里面）
```bash
<jsp:include page=""></jsp:include>
<jsp:param value="" name=""/>
<jsp:forward page=""></jsp:forward>
```

```bash
 - jsp:include
	<jsp:include page="other02.jsp"></jsp:include>
	包含指定的页面， 这里是动态包含。 也就是不把包含的页面所有元素标签全部拿过来输出，而是把它的运行结果拿过来。 

 - jsp:forward
	前往哪一个页面。
  	<jsp:forward page=""></jsp:forward>
	等同于：请求转发
	<% request.getRequestDispatcher("other02.jsp").forward(request,response);%>	

 - jsp:param
	第一步：在包含某个页面的时候，或者在跳转某个页面的时候，加入这个参数。
	<jsp:forward page="other02.jsp">
		<jsp:param value="beijing" name="address"/>
	</jsp:forward>
	
	第二步：在other02.jsp中获取这个参数
	<br>收到的参数是：<br>
	<%= request.getParameter("address")%>
```
## jsp内置对象（重点！）
 所谓内置对象（有九个），就是我们可以直接在jsp页面中使用这些对象。 不用创建。
 ### 作用域对象
 - **作用域对象有四个：**
1. **pageContext**
2. **request**
3. **session**
4. **application**

- **作用域 ：** 表示这些对象可以存值，他们的取值范围有限定。 
 用setAttribute   和  getAttribute来进行存取。

```bash
		使用作用域来存储数据<br>
		<%
			pageContext.setAttribute("name", "page");
			request.setAttribute("name", "request");
			session.setAttribute("name", "session");
			application.setAttribute("name", "application");
		%>
		
		取出四个作用域中的值<br>
		<%=pageContext.getAttribute("name")%>
		<%=request.getAttribute("name")%>
		<%=session.getAttribute("name")%>
		<%=application.getAttribute("name")%>
```
- 作用域范围大小：（由小到大）


```bash
pageContext -- request --- session -- application 
```
-  四个作用域的区别


```bash
11. pageContext 【PageContext】
作用域仅限于当前的页面，还可以获取到其他八个内置对象。

12. request 【HttpServletRequest】
作用域仅限于一次请求， 只要服务器对该请求做出了响应。 这个域中存的值就没有了。

13. session 【HttpSession】
作用域限于一次会话（多次请求与响应） 当中。 

14. application 【ServletContext】
 整个工程都可以访问， 服务器关闭后就不能访问了。 
```
### 其他内置对象
- out		 		【JspWriter】
- response  	【HttpServletResponse】
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200229140948897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- exception  【Throwable】
- page	 		【Object】 ---就是这个jsp翻译成的java类的实例对象
- config 		【ServletConfig】

# 二、EL表达式
- 是为了简化jsp代码，具体一点就是为了简化在jsp里面写的那些java代码。


```bash
写法格式：
	${表达式 }
```
- 如果从作用域中取值，会先从小的作用域开始取，如果没有，就往下一个作用域取。  一直把四个作用域取完都没有， 就没有显示。

### EL取值方式

```bash
1. 取出4个作用域中存放的值
	<%
		pageContext.setAttribute("name", "page");
		request.setAttribute("name", "request");
		session.setAttribute("name", "session");
		application.setAttribute("name", "application");
	%>
		
	按普通手段取值：
	<%= pageContext.getAttribute("name")%>
	<%= request.getAttribute("name")%>
	<%= session.getAttribute("name")%>
	<%= application.getAttribute("name")%>
		
	使用EL表达式取出作用域中的值：
	${ pageScope.name }
	${ requestScope.name }
	${ sessionScope.name }
	${ applicationScope.name }

2. 如果域中所存的是数组
   	<%	String [] a = {"aa","bb","cc","dd"};
   		pageContext.setAttribute("array", a); %>
   		
   	使用EL表达式取出作用域中数组的值：
   	${array[0] } , ${array[1] },${array[2] },${array[3] }


3. 如果域中存的是集合
	使用EL表达式取出作用域中集合的值：
	${li[0] } , ${li[1] },${li[2] },${li[3] }

4. 取出Map集合的值
	<br>-------------Map数据----------------<br>
  	<%
   		Map map = new HashMap();
   		map.put("name", "zhangsna");
   		map.put("age",18);
   		map.put("address","北京..");
   		map.put("address.aa","深圳..");
   		pageContext.setAttribute("map", map);
   	%>
   	
   	使用EL表达式取出作用域中Map的值：
   	${map.name } , ${map.age } , ${map.address }  , ${map["address.aa"] }
```
### 取值细节
```bash
1.  从域中取值。  得先存值。

  <%  //pageContext.setAttribute("name", "zhangsan");
      session.setAttribute("name", "lisi...");  %>
  
   直接指定说了，到这个作用域里面去找这个name
   ${ pageScope.name } 

   //先从page里面找，没有去request找，去session，去application 
   ${ name }

   指定从session中取值
   ${ sessionScope.name }  

2. 取值方式

- 如果这份值是有下标的，那么直接使用[]
	<%	String [] array = {"aa","bb","cc"}
		session.setAttribute("array",array);  %>
	
	${ array[1] } --> 这里array说的是attribute的name 

- 如果没有下标， 直接使用 .的方式去取
	<%	User user = new User("zhangsan",18);
		session.setAttribute("u", user);  %>
		
	${ u.name }  , ${ u.age } 

3. 一般使用EL表达式，用的比较多的，都是从一个对象中取出它的属性值，比如取出某一个学生的姓名。
```
### EL表达式 的11个内置（隐式）对象
```bash
内置对象：已经存在，不用创建，可以直接获取成员变量或者属性
	用法：${ 对象名.成员 }

JSP相关对象
- pageContext 

作用域相关对象（用的比较多）
- pageScope
- requestScope
- sessionScope
- applicationScope

请求头信息相关对象
- header
- headerValues

请求参数信息相关对象
- param
- paramValues

Cookie
- cookie

全局初始化参数
- initParam
```

# 三、 JSTL（标准标签库）
-  全称 ： JSP Standard Tag Library  ： jsp标准标签库
-  简化jsp的代码编写。 替换 <%%> 写法。 一般与EL表达式配合

## JSTL使用
1. 导入jar文件到工程的WebContent/Web-Inf/lib    jstl.jar和standard.jar
2. 在jsp页面上，使用taglib 指令，来引入标签库
3. 注意： 如果想支持 EL表达式，那么引入的标签库必须选择1.1的版本，1.0的版本不支持EL表达式。


```bash
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```
## 常用标签
```bash
	<c:set></c:set>
	<c:if test=""></c:if>
	<c:forEach></c:forEach>
```

```bash
1. c:set
		<!-- 声明一个对象name， 对象的值 zhangsan , 存储到了page（默认） ， 指定是session -->
		<c:set var="name" value="zhangsan" scope="session"></c:set>
		
		${sessionScope.name }

2. c:if
 判断test里面的表达式是否满足，如果满足，就执行c:if标签中的输出 ， c:if 是没有else的。 
		<c:set var="age" value="18" ></c:set>
		<c:if test="${ age > 26 }">
			年龄大于了26岁...
		</c:if>

		<c:if test="${ age <= 26 }">
			年龄小于了26岁...
		</c:if>


		------------------------------

		定义一个变量名 flag  去接收前面表达式的值，然后存在session域中

		<c:if test="${ age > 26 }" var="flag" scope="session">
			年龄大于了26岁...
		</c:if>


3. c:forEach
	从1 开始遍历到10 ，得到的结果 ，赋值给 i ,并且会存储到page域中， step , 增幅为2， 
	<c:forEach begin="1" end="10" var="i" step="2">
		${i }
	</c:forEach>
	-----------------------------------------------

	<!-- items : 表示遍历哪一个对象，注意，这里必须写EL表达式。 
	var: 遍历出来的每一个元素用user 去接收。 -->
	<c:forEach var="user" items="${list }">
		${user.name } ----${user.age }
	</c:forEach>
```


