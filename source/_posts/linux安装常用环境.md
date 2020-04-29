---
title: linux安装常用环境
toc: true
tags: linux
categories: linux学习笔记
abbrlink: 2004179649
date: 2020-02-16 23:11:29
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;手里有一台免费的阿里云服务器是真的很香，但是我也不能让他就这样只放一个博客网站吧，于是乎，我想在这片几乎空白的区域，开拓出一块儿良好的开发环境，也顺便熟悉一下linux系统的操作。这篇主要就是讲了，如何在云服务器上安装JDK，MySql，Tomcat
<!--more-->

# 一、安装JDK1.8
## ①查看有无自带的JDK
- 有很多linux系统自带了`OpenJDK`，`OpenJDK`不包含`Deployment`（部署）功能，源代码不完整，而且精简化，只适合个人使用。

```bash
1. 查看有无jdk，如果有，则会出现openjdk
java -version

2.查看关于java的所有文件
rpm -qa | grep java

3. 删除掉openjdk（后面跟你自己的版本进行删除）
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.111-2.6.7.8.el7.x86_64
```

## ②下载JDK


- 去[ORACLE官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载对应的linux版本。记得后缀是`.tar.gz`结尾。先下载到本地，再通过工具上传到linux上。这里推荐使用的工具是finalshell。详情使用请另参考。

## ③安装JDK
- 在`usr/local/src`目录下新建一个文件夹`jdk`

```bash
mkdir /usr/local/src/jdk
```
- 将本地JDK上传到这个目录下。
- 进入文件夹，并解压

```bash
cd /usr/local/src/jdk
tar zvxf 你的压缩包
```
- 安装完成

## ④配置环境变量
- 进入环境变量的配置文件,编辑

```bash
vim /etc/profile
```
- 在最下面添加以下代码：

```bash
export JAVA_HOME=/usr/local/src/jdk/jdk1.8.0_161 （这里填写你自己的文件路径）
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
```
- 编写完之后：`ESC->:wq`退出。
- 查看是否安装成功

```java
java -version
```
# 二、安装Tomcat
## ①下载tomcat
我下载的是Tomcat8.5，和安装JDK几乎一样的步骤，[点击下载](http://mirrors.ustc.edu.cn/apache/tomcat/)对应的版本。
## ②上传，解压
- 新建存放目录

```bash
mkdir /usr/local/src/Tomcat
```

- 用工具上传到该目录中。
- 解压

```bash
cd //usr/local/src/Tomcat
tar zvxf 你的安装包
```
## ③配置环境变量
- 修改/etc/profile，加入Tomcat环境变量

```bash
1.
vim /etc/profile

2.加入以下代码：
export CATALINA_HOME=你的tomcat路径
export PATH=$PATH:${CATALINA_HOME}/bin

3.刷新环境变量
source /etc/profile
```

## ④启动tomcat

```bash
cd /usr/local/src/Tomcat/apache-tomcat-8.5.53/bin
sh start.sh
```

- 查看端口号和日志

```bash
1.查看端口号
ss -tan

2.查看日志
tail -f ../logs/catalina.out
```

# 三、安装Mysql
## ①查看自带版本
- 查看有没有`mysql`，有则卸载。

```bash
1. 查看
rpm -qa | grep mysql
2. 卸载
rpm -e --nodeps 查出来的mysql
```
## ②命令下载mysql
- 如果直接去官网下载，不知道要等多久，于是我找到了一个[镜像网站](http://mirrors.sohu.com/mysql/MySQL-5.6/)，点击找到你的对应版本，这里以`mysql-5.6.44-linux-glibc2.12-x86_64.tar.gz`为例
- 命令安装：既然找到了具体网址，那我们就用`wget`命令安装，省去上传的时间。

```bash
wget http://mirrors.sohu.com/mysql/MySQL-5.6/mysql-5.6.44-linux-glibc2.12-x86_64.tar.gz
```
- 解压

```bash
tar -zxvf mysql-5.6.44-linux-glibc2.12-x86_64.tar.gz
```
- 创建mysql用户组及用户

```bash
groupadd mysql
useradd -r -g mysql mysql
```

- 修改配置文件

```bash
cp /usr/local/mysql/support-files/my-default.cnf /etc/my.cnf
vi my.cnf
```

```java
[mysqld] 

#设置3306端口 
port = 3306  

# 设置mysql的安装目录 根据实际目录进行配置
basedir=/usr/local/mysql 

# 设置mysql数据库的数据的存放目录 根据实际目录进行配置
datadir=/usr/local/mysql/data 

# 允许最大连接数 
max_connections=200 

# 服务端使用的字符集默认为8比特编码的latin1字符集 
character-set-server=utf8 

# 创建新表时将使用的默认存储引擎 
default-storage-engine=INNODB 

max_allowed_packet=16M
```
- 安装autoconf库，赋权并执行初始化数据库

```bash
yum -y install autoconf
```

```bash
chown -R mysql.mysql /usr/local/src/mysql5.6
/usr/local/src/mysql5.6/scripts/mysql_install_db --verbose --user=mysql --defaults-file=/etc/my.cnf --datadir=/usr/local/src/mysql5.6/data --basedir=/usr/local/src/mysql5.6
```

## ③ 启动msyql：

```bash
service mysql start
```
- 如果出现以下情况：请按照以下步骤来

```bash
Failed to start mysql.service: Unit not found.
```

```bash
1.查询/etc/init.d/下是否存在mysql，一般都没有，如果有的话，记得备份
ll /etc/init.d/ | grep mysql

2. 到你的mysql安装目录下，查看是否有mysql.server
find / -name mysql.server

3.如果有，把他复制到/etc/init.d/mysql
cp /usr/local/src/mysql5.6/support-files/mysql.server /etc/init.d/mysql
```

- 再次启动mysql

```bash
service mysql start
```
- 查看mysql运行状态


```bash
service mysql status
```

- 配置环境变量

```bash
1.vim ~/.bash_profile
添加：export PATH=$PATH:/usr/local/src/mysql5.6/bin

2.source ~/.bash_profile
```

## ③登录

```bash
mysql -u root -p 
Enter password: 直接回车
```

-出现如下

```bash
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.29

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> 
```
## ④修改密码

```bash
set password for root@localhost = password('new password');;
new password替换成你要设置的密码
注意:密码设置必须要大小写字母数字和特殊符号（,/';:等）,不然不能配置成功
```
## 开启远程连接
### 前提：
- 必须要开启了3306端口才可以

```bash
1.开启端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent 

2.重启
firewall-cmd --reload

3.查看是否有3306
firewall-cmd --zone=public --list-ports
```

### 方法一：（不建议）
- 在linux上登陆mysql，登陆成功后，依次执行命令：

```bash
use mysql;
update user set host = '%' where user = 'root' and host ='localhost'; 
flush privileges;
```
- 并不建议对root账户进行开启远程访问权限，因为一旦拿到root权限之后，就可以对数据库执行任何操作，不安全。

### 方法二
- 新建一个用户并开启远程访问权限：
- 登录MySQL

```bash
use mysql;
grant all privileges on 库名.表名 to '用户名'@'IP地址' identified by '密码' with grant option;
flush privileges;
```
- 详细参数解释
`库名`要远程访问的数据库名称,所有的数据库使用`*`	
`表名`	要远程访问的数据库下的表的名称，所有的表使用`*`
`用户名`	要赋给远程访问权限的用户名称（这里写一个你自己创建的用户）
`IP地址`	可以远程访问的电脑的IP地址，所有的地址使用`%`
`密码`要赋给远程访问权限的用户对应使用的密码

## 本地连接
- 保证本地mysql服务是打开的，3306端口是开的。
- 打开sqlyong，新建连接，填入以下数据


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200403172306622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

## 大功告成！！！