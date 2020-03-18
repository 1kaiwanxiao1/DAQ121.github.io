---
title: MySql安装与卸载
author: 代澳旗
tags:
    - MySql
categories:
    - 数据库 
date: 2020-02-01 20:05:03
---
MySql的卸载与安装
<!--more-->
# 安装
 - 运行安装程序，在启动配置教程之前，一路下一步，没有下一步的话就直接`finish`
 - 第一次`finish`之后启动服务器配置教程
 - 第一个注意：`include mysql bin directory to windows path`
 - 第二个注意：端口号不要修改，字符集选择UTF-8

# 卸载
 - 打开控制面板，删除软件
 - 删除mysql安装目录的所有文件
 - 删除mysql数据存放文件，C盘下面的`Programe data`里，它是隐藏文件，要点击查看隐藏文件。