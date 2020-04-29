---
title: JSP模式&&MVC模式
tags: MVC
categories: Java Web
toc: true
abbrlink: 2199266903
date: 2020-03-10 08:36:18
---
一个项目有一个好的设计模式很重要，有一个好的设计模式，使得开发和维护也变得简单。MVC是一种程序开发设计模式，他实现了显示模块与功能模块分离，提高了程序的可维护性，可移植性，可扩展性，可重用性，降低了程序的开发难度。
<!--more-->

## JSP模式&MVC模式
- MVC=model（模型）+view（视图）+contorller（控制）
- 商城=仓库（模型）+店铺（视图），（控制）店铺去仓库拿货（取数据）


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323093038355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323093051579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

- **MVC工作机制**
1. 用户操作，将指令送到Conntoller（控制器）。
2. 控制器将指令和数据传递给业务模型（model）。
3. 模型进行业务逻辑判断，数据库存取。
4. 根据业务逻辑选择不的视图（View）展示给用户

- MVC设计模式可以分为两种：闭环和开放，但是开放派中（controllor）的职责过重。下面是闭环的优点：

1. 耦合性低，实现了用户显示模块与功能（业务逻辑）模块的分离
2. 提高应用系统的可维护性，可扩展性，可维护性，以及模块功能的复用性。

- **缺点：**
1. 对于简单的页面来说，会增加结构的复杂性，产生过多的更新操作，降低运行效率，而且使用的时候要精心计划，由于模型和视图严格分离，给调试程序带来了一定的困难。
2. 不适合中小应用程序，设计比较麻烦，用在中小应用上，或者改造一些工具来适应MVC，也是得不偿失的！！  