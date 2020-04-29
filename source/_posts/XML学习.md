---
title: XML学习
tags: XML
categories: Java Web
toc: true
abbrlink: 4122240603
date: 2020-02-08 13:01:53
---
**XML:  extendsible  markup  labguage  可扩展的标记语言**
<!--more-->

 ## 一、XML的作用？

 
1. 可以用来保存数据（数据多的时候用数据库，少量时候用XML）
2. 可以用来做配置文件
3. 数据传输载体


## 二、XML文档结构_倒状树形结构

```bash
<?xml version="1.0" encoding="UTF-8"?>
<stus>
	<stu>
		<name>daiaoqi</name>
		<age>18</age>
		<desc>我要学编程</desc>
	</stu>
	<stu>
		<name>wt</name>
		<age>20</age>
		<desc>我要找工作</desc>
	</stu>
</stus>
```

## 三、文档定义&声明


 - 定义：其实就是一个文件，文件的后缀为 **.xml**
 - 声明：


```bash
 <?xml version="1.0" encoding="gbk" standalone="no" ?>
 version : 解析这个xml的时候，使用什么版本的解析器解析
 encoding : 解析xml中的文字的时候，使用什么编码来翻译
 standalone  : no：该文档会依赖关联其他文档，yes：这是一个独立的文档
```
 - encoding：默认文件保存的时候，使用的是GBK的编码保存。
要想让我们的xml能够正常的显示中文，有以下解决办法：
1. 让encoding也是GBK 或者 gb2312 。
2. 如果encoding是 utf-8 ， 那么保存文件的时候也必须使用utf-8。
3. 保存的时候见到的ANSI 对应的其实是我们的本地编码 GBK。
4. 为了通用，建议使用UTF-8编码保存，以及encoding 都是 utf-8。


## 四、元素定义
1.其实就是里面的标签， <> 括起来的都叫元素 。 成对出现。  如下： 


```bash
<stu> </stu>
```

2.文档声明下来的第一个元素叫做根元素 (根标签)
3.标签里面可以嵌套标签
4.空标签：既是开始也是结束， 一般配合属性来用。
```bash
<age/>
		<stu>
			<name>张三</name>
			<age/>
		</stu>
```
5.标签可以自定义。 
XML 元素必须遵循以下命名规则：

```bash
1.名称可以含字母、数字以及其他的字符 
2.名称不能以数字或者标点符号开始 
3.名称不能以字符 “xml”（或者 XML、Xml）开始 
4.名称不能包含空格 
5.命名尽量简单，做到见名知义
```

## 五、元素定义
- 简单元素：元素里面包含了普通的文字
- 复杂元素：元素里面还可以嵌套其他的元素


## 六、属性定义
- 定义在元素里面， <元素名称  属性名称="属性的值"></元素名称>

```bash
<stus>
	<stu id="10086">
		<name>张三</name>
		<age>18</age>
	</stu>
	<stu id="10087">
		<name>李四</name>
		<age>28</age>
	</stu>
</stus>
```
## 七、xml注释
与html的注释一样。 xml的注释，不允许放置在文档的第一行。 必须在文档声明的下面。

```bash
<!-- --> 
	如： 
<?xml version="1.0" encoding="UTF-8"?>
	<!-- 
		//这里有两个学生
		//一个学生，名字叫张三， 年龄18岁， 学号：10086
		//另外一个学生叫李四  。。。
	-->
```
## 八、CDATA区
- 所有 XML 文档中的文本均会被解析器解析，只有 CDATA 区段（CDATA section）中的文本会被解析器忽略。
在 XML 元素中，"<" 和 "&" 是非法的。"<" 会产生错误，因为解析器会把该字符解释为新元素的开始，"&" 也会产生错误，因为解析器会把该字符解释为字符实体的开始。
在html中，某些文本，比如 JavaScript 代码，包含大量 "<" 或 "&" 字符。为了避免错误，可以将脚本代码定义为 CDATA。
- 一般在数据传输的时候会出现，比如说服务器给客户端传数据。
- 严格地讲，在 XML 中仅有字符 "<"和"&" 是非法的。省略号、引号和大于号是合法的，但是把它们替换为实体引用是个好的习惯。
 
```bash
  <   &lt;
  &   &amp;
```
- CDATA区写法

```bash
CDATA区 开始于 "<![CDATA["
        结束于 "]]>"
    如：
<des><![CDATA[<a href="http://www.baidu.com">我要学编程</a>]]></des>
```

## 九、XML解析
- 其实就是获取元素里面的字符数据或者属性数据。
- XML解析方式(**面试常问**)有很多种，常用的有两种（DOM和SAX）。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224114632758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- 针对以上两种解析方式，给出的解决方案有哪些？
1. jaxp  sun公司自带的， 比较繁琐
2. jdom		
3. dom4j  （常用）使用比较广泛
补充：jdom和dom4j的故事：刚开始都是一伙人做的，后来产生了分歧，另外一批人就做了dom4j，把jdom挤下去了。


## 十、Dom4j 基本用法


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200224140840458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

```bash
element.element("stu") : 返回该元素下的第一个stu元素
element.elements(); 返回该元素下的所有子元素。 
```

1. 创建SaxReader对象
2. 指定解析的xml
3. 获取根元素。
4. 根据根元素获取子元素或者下面的子孙元素

```java
javapackage com.daq.test;
import java.io.File;
import java.util.List;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

public class MainTest {
	/**
	 * @param args
	 */
	public static void main(String[] args) {

		try {
			//1.创建sax读取对象
			SAXReader reader = new SAXReader();//jdbc--classloader
			//2.指定解析的xml源
			Document document=reader.read(new File("src/xml/stus.xml"));
			//3.得到元素
			//得到根元素
			Element rootElement=document.getRootElement();
			
			//4.获取根元素下面的子元素age
//			rootElement.element("age");
//			System.out.println(rootElement.element("stu").element("age").getStringValue());
			
			List<Element> elements=rootElement.elements();
			//遍历所有的stu元素
			for (Element element : elements) {
				//获取stu下面的name元素
				String name=element.element("name").getText();
				String age=element.element("age").getText();
				String adress=element.element("adress").getText();
				System.out.println("name="+name+"age="+age+"adress="+adress);
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

## 十一、Dom4j 的 Xpath使用


 - dom4j里面支持Xpath的写法。 xpath其实是**xml的路径语言**，支持我们在解析xml的时候，能够快速的定位到具体的某一个元素。
1. 添加jar包依赖 ：jaxen-1.1-beta-6.jar
2. 在查找指定节点的时候，根据XPath语法规则来查找
3. 后续的代码与以前的解析代码一样。

```java
//要想使用Xpath， 还得添加支持的jar 获取的是第一个 只返回一个。 
Element nameElement = (Element) rootElement.selectSingleNode("//name");
	System.out.println(nameElement.getText());

	System.out.println("----------------");

	//获取文档里面的所有name元素 
	List<Element> list = rootElement.selectNodes("//name");
	for (Element element : list) {
		System.out.println(element.getText());
		}
```
## 十二、XML 约束（了解即可）
如下的文档， 属性的ID值是一样的。 这在生活中是不可能出现的。 并且第二个学生的姓名有好几个。 一般也很少。那么怎么规定ID的值唯一， 或者是元素只能出现一次，不能出现多次？ 甚至是规定里面只能出现具体的元素名字。 

```java
<stus>
	<stu id="10086">
		<name>张三</name>
		<age>18</age>
		<address>深圳</address>
	</stu>
	<stu id="10086">
		<name>李四</name>
		<name>李五</name>
		<name>李六</name>
		<age>28</age>
		<address>北京</address>
	</stu>
</stus>
```
**约束：**
- DTD：语法自成一派， 早前就出现的。 可读性比较差。 
- Schema：其实就是一个xml ， 使用xml的语法规则， xml解析器解析起来比较方便 ， 是为了替代DTD 。但是Schema 约束文本内容比DTD的内容还要多。 所以目前也没有真正意义上的替代DTD

