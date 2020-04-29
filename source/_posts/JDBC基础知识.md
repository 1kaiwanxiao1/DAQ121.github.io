---
title: JDBC基础知识
author: 代澳旗
tags:
  - - JDBC
    - MySql
categories:
  - 数据库
toc: true
abbrlink: 3453823259
date: 2020-02-03 20:05:03
---
JDBC概述，作用，类型，以及使用时如何选择。
<!--more-->
## 一、JDBC是什么？
 - 百度百科解释：JDBC(java database connectivity)驱动程序是对JDBC规范完整的实现，它的存在在Java程序与数据库系统之间建立了一条通信的渠道。它是 一 种可用于执行 SQL 语句的 Java API(**Application Programming Interface， 应用程序设计接口**)。
 - 简单理解：java 数据库连接。有了它，就能够在java代码中操作任何数据库。由于数据库种类繁多，并且java程序使用广泛，sun公司就提供了一种规范，让其他的数据库提供商去实现底层的访问。
 - 刚开始每个数据库（Oracle，Mysql，SqlServer）的访问规则都不同，后来SUN公司想一统天下，出台了一套规范，各个数据库要按照这个规范去写底层代码，与此同时，Java程序也直接去找JDBC。


## 二、作用是什么？
 - JDBC 为数据库应用开发人员、数据库前台开发人员提供了一种标准的应用程序设计接口， 使开发人员可以用纯 Java 语言编写完整的数据库应用程序。
 - JDBC 通过调用其接口提供的方法， 提供了 Java 应用程序与各种数据库服务器之间的连接服务，。
 - 它支持 ANSI SQL- 92 标准， 实现了从 Java 程序内调用标准的 SQL 命令对数据库进行查询、插入、删除和更新等操作， 并确保数据事务的正常进行。

## 三、JDBC驱动程序
根据访问数据库的技术不同， JDBC 驱动程序相应地分为四种类型。不同类型的驱动程序有着不一样的特性和使用方法。

 1.**JDBC-ODBC桥驱动程序(JDBC-ODBC Bridge Driver)**
 - [ ] 此类驱动程序由JDBC-ODBC桥和一个ODBC驱动程序组成。
 - [ ] 工作原理：通过一段本地**C代码**将JDBC调用转化成ODBC调用。这一类型必须在本地计算机上先安装好ODBC驱动程序，然后通过JDBC-ODBCBridge的转换，将Java程序中使用的JDBC API访问指令转化成ODBC API指令，进而通过ODBC驱动程序调用本地数据库驱动代码完成对数据库的访问。


 2.**部分Java的本地JDBC API驱动程序**
 - [ ] JDBC API驱动程序(Anative API partly Java technology-enabled Driver)
 - [ ] 工作原理：此类驱动程序也必须在本地计算机上先安装好特定的驱动程序(类似ODBC)，然后通过PartialJavaJDBCDriver的转换，把Java程序中使用的JDBC API转换成NativeAPI，进而存取数据库。

3.**纯Java的数据库中间件驱动程序**
 - [ ] 纯Java的数据库中间件驱动程序(Pure Java Driver for Database Middleware)使用这类驱动程序时，不需要在本地计算机上安装任何附加软件，但是必须在安装数据库管理系统的服务器端加装中间件(Middleware)，这个中间件负责所有存取数据库时必要的转换。
 - [ ] 工作原理：驱动程序将JDBC访问转换成与数据库无关的标准网络协议(通常是HTTP或HTTPS)送出，然后再由中间件服务器将其转换成数据库专用的访问指令，完成对数据库的操作。中间件服务器能支持对多种数据库的访问。


4.**纯Java的JDBC驱动程序**
 - [ ] 纯Java的JDBC驱动程序(Direct-to-DatabasePureJavaDriver)这类驱动程序是直接面向数据库的纯Java驱动程序，即所谓的"瘦"驱动程序。
 - [ ] 工作原理：使用这类驱动程序时无需安装任何附加的软件(无论是本地计算机或是数据库服务器端)，所有存取数据库的操作都直接由JDBC驱动程序来完成，此类驱动程序能将JDBC调用转换成DBMS专用的网络协议，能够自动识别网络协议下的特殊数据库并能直接创建数据连接。

## 四、JDBC驱动程序的选择
在企业内部信息系统中，选择合适的JDBC驱动程序，使之符合数据库程序设计的要求，是提高系统性能必须考虑的一个方面。不同的应用有不同的需要，所以要根据应用选择合适的驱动程序。

 - [ ] **JDBC-ODBC桥驱动程序(JDBC-ODBC Bridge Driver)**
 - 优点：节省投资，利用了已有的ODBC驱动程序。
 - 缺点：中间有多次调用，性能受到影响，执行效率比较低，不适合对大数据量存取的应用。有损Java数据库程序的兼容性，不具备跨平台性，不适合基于Internet/Intranet的应用。
 - 应用：桥接驱动程序用于已经在ODBC技术上投资的情形，例如已经投资了Windows应用服务器。


 - [ ] **部分Java的本地JDBC API驱动程序**
 - 优点：具有开放性，其利用多层结构，上层用Java实现，支持多数据库，下层为本地代码(包括一些二进制代码和一个轻量的Java库)，加快了执行速度，提高了数据库访问效率。
 - 缺点：没有使用纯Java的API，如果在本地代码中存在缺陷，将可能使Java虚拟机完全垮掉。
 - 应用：受到限制


 - [ ] **纯Java的数据库中间件驱动程序**
 - 优点：由纯Java语言开发而成的，并且中间件也仅需要在服务器上安装，不再需要客户端的本机代码，这类驱动程序的体积最小，效率较高，具有最大的灵活性。此类驱动采用标准的网络协议，可以被防火墙支持，是开发Applet程序理想的选择(其实这些驱动是为Applet特别编写的)，是Internet应用理想的解决方案。开发者还可以利用单一的驱动程序连接到多种数据库。
 - 缺点：需要在服务器端安装中间件，这适当影响了驱动程序的效率。
 - 应用：基于Web的应用系统的开发。


 - [ ] **纯Java的JDBC驱动程序**
 - 最佳的JDBC驱动程序类型。
 - 优点：无需安装任何附加软件，不会增加任何额外开销，效率最高，拥有最佳的兼容性。
 - 缺点：可能不被防火墙支持，在Internet中会存在潜在安全隐患，成为这类驱动最大的缺陷。
 - 应用：在企业级应用软件中，是应优先考虑的。因此，一个Servlet程序，要适应不同的操作系统，最好使用此类驱动;这种驱动也非常适合Applet程序，事实证明它能安全通过TCP/IP协议连接到数据库。
 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200220152318929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/202002201540355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
