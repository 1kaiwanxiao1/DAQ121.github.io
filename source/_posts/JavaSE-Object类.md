---
title: JavaSE---Object类
toc: true
date: 2020-04-17 11:51:07
tags: javaSE
categories: JavaSE基础知识
abbrlink: Object

---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Object类是所有类的父类，位于java.lang包中。这也是面试的一个小考点吧，这篇文章看看Object中有哪些方法。
<!--more-->

## Object类概述

 - Object类是JDK默认提供的一个类。java中除了Object类，所有类都存在继承，默认会继承Object父类
 - 所有类的对象都可以使用 Object 接收。
 - Object 达到最高参数统一化。

## 什么时候使用Object类?

- 对于Object类的使用可以分为两个阶段：JDK 1.5以前，以及JDK 1.5之后。
- Object之所以会被大量的进行参数的接收处理，很大的一部分原因在于：你的程序里面需要接受的类型很多，并不固定，可是现在的开发理念中强调的问题不再是这一点了，而是如何可以避免向下转型（如果避无可避，那么就用），因为从JDK1.5之后引入了泛型机制（在基础课程讲解泛型机制的时候重点分析了Object缺陷）。
- 现在的设计思想是用泛型来避免向下转型的操作（ClassCastException），如果你要认真读了API文档你会发现，可以接收Object类型的方法是越来越少了，开发中尽量回避Object接收的项目为主。

## Object类有哪些方法？

- 可以看到里面都是用native关键字修饰的方法。都是原生函数，得调用C++的函数。


![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041711473684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)