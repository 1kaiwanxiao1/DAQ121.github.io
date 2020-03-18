---
title: MySql基础操作
author: 代澳旗
tags:
    - MySql
categories:
    - 数据库 
date: 2020-02-01 20:05:03
---
本文简单介绍了数据库，和一些基本操作。
<!--more-->
# 什么是数据库？
- 数据库就是一个文件系统，只不过我们需要通过SQL命令来操作这个文件系统。
- 数据库（DataBase）是按照数据结构来组织，存储和管理数据的建立在计算机存储设备上的仓库。
- 作用：存储数据，数据的仓库，带有访问权限，限制不同的人可以有不同的操作。
- java EE操作的都是后台数据，取到后台数据进行封装，然后交给前端去展现。

# 有哪些数据库
 - mysql：开源免费，适用于中小企业的免费数据库。
 - oracle：甲骨文公司，收费软件，适用于大型网站。
 - db2：IBM公司，做解决方案，软件和硬件，服务器架构，银行系统。
 - sqlserver：windows里，政府网站。asp.net  大学教学，图形化做得好。

# Mysql的sql语句有哪些？
 - SQL(Structure Query Language)结构化查询语言
 - DDL数据定义语言：定义数据库，数据表他们的结构，create（创建），drop（删除），alert（修改）
 - DML数据操纵语言：主要用来操作数据，insert（插入），update（修改），delete（删除）
 - DCL数据控制语言：定义访问权限，取消访问权限，安全设置，grant
 - DQL数据查询语言：select（查询） from子句 where子句

# 数据库的CRUD操作(增删改查)
 - 登录数据库服务器


```sql
mysql -uroot -pdaq
```
 - 创建数据库：

```sql
create database 数据库的名字;
```
```sql
create database 数据库的名字 character set 字符集;//指定字符集
```

 - 查看所有数据库

```sql
show databases;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022018024091.png)

 - 查看指定数据库：


```sql
show create database 数据库的名字;
```
 - 修改数据库的字符集


```sql
alter database 数据库的名字character set 字符集;
```
 - 删除数据库


```sql
drop database 数据库的名字;
```
 - 切换数据库（选中数据库）


```sql
use 数据库的名字;
```
 - 查看当前正在使用的数据库


```sql
select database();
```
# 表的创建
 - 创建表


```sql
create table 表名（列名 列的类型(长度) 约束，列名2 列的类型(长度) 约束）;
```
```bash
列的类型：
java     sql
int      int
char     char（固定长度）
String   varchar（可变长度）长度代表字符的个数
         
         text：主要用来存放文本
         blob：存放的是二进制
```
```bash
列的约束：
	主键约束：primary key
	唯一约束：unique
	非空约束：not null
```
```sql
create table student(
	sid int primary key,
	sname varchar(10),
	sex int,
	age int);
```

 - 查看所有表：


```sql
show tables;
```
 - 查看表的定义：


```sql
show create table 表名;
```
 - 查看表结构：


```sql
desc 表名;
```
 - 修改表：


```bas
- 添加列（add）：
alter table 表名 add 列名 列的类型 列的约束;
alter table student add greade int not null;

- 修改列（modify）：
alter table 表名 modify sex varchar（2）;

- 修改列名（change）：
alter table 表名 change sex gender varchar（2）;

- 删除列（drop）
alter table 表名 drop greade;
```
 - 修改表名（rename）


```sql
rename table student to 新表名;
```
 - 修改表的字符集


```sql
alter table 表名 character set 字符集;
```
 - 删除表


```sql
drop table 表名;
```

# Sql完成对表中数据的CRUD操作
 - 插入数据


```sql
insert into 表名（列名1，列名2，列名3）values(值1，值2，值3);
如：insert into student(sid,sname,sex,age)values(1,'zhangsan',1,23);

简单写法：插入的是全列名的数据，表名后面的列名可以省略
insert into 表名values(值1，值2，值3);
如:insert into student values(1,'zhangsan',1,23);

如果插入的是部分数据，列名不能省略
如：insert into student(sid,sname,sex)values(1,'zhangsan',1);

批量插入：效率比单条插入效率高。但不能出错
insert into student values(1,'zhangsan',1，23),
						     (2,'lisi',0，22),
						     (3,'wangwu',1，24);
```
 - 查看表中数据


```sql
select * from student;
```
 - 表中插入数据中文乱码问题


```bash
1.暂停Mysql服务
2.在Mysql安装路径中找到my.ini配置文件
3.将57行的编码改成GBK。
4.保存，退出，启动mysql服务
```
 - 删除记录


```sql
delete from 表名[where 条件]
delete from student where sid=10;
delete from student;  如果没有指定条件，会将表中数据一条一条全部删除掉。
```
 - 面试问题：


```bash
请说一下delete 删除数据和truncate删除数据有什么差别？
答：delete：属于DML数据操纵语言，一条一条删除表中的数据。
	truncate：属于DDL数据定义语言，先删除表，在重建表。 

关于那条执行效率高：具体要看表中的数据量。
	如果数据比较少，delete比较高效。
	如果数据比较多，truncate比较高效。 
```
 - 更新表记录


```sql
update 表名 set 列名=列的值，列名2=列的值2 [where 条件]

需求：将sid为5的名字改成李四
update student set sname='李四' where sid=5;
//如果参数是字符串或者日期，要加上单引号
```
# select查询的简单查询
```sql
select [distinct] [ * ] [列名，列名2] from 表名 [where 条件]
distinct：去除重复的数据

--简单商品：手机数码，鞋靴箱包
1.分类的ID
2.分类名称
3.分类描述

//创建表
create table category(
	cid int primary key auto_increment,
	cname varchar(10),
	cdesc varchar(31)
	);

//插入数据	
insert into category values(null,'手机数码','电子产品');
insert into category values(null,'鞋靴箱包','江南皮鞋厂');
insert into category values(null,'香烟酒水','黄鹤楼');
insert into category values(null,'酸奶饼干','安慕希');
insert into category values(null,'家用电器','美的空调');

--所有商品
1.商品ID
2.商品名称
3.商品价格
4，生产日期
5，商品分类ID

//创建表
create table product(
	pid int primary key auto_increment,
	pname varchar(10),
	price double,
	pdate timestamp,
	cno int 
	);

//插入数据
insert into product values(null,'xiaomi',998,null,1);
insert into product values(null,'chuizi',2998,null,1);
insert into product values(null,'nike',888,null,2);
insert into product values(null,'laocunzhang',88,null,3);
insert into product values(null,'jingjiu',32,null,3);
insert into product values(null,'xiaoxiong',3,null,4);
insert into product values(null,'weilong',0.5,null,5);
insert into product values(null,'wangwang',0.5,null,5);
```

 - 简单查询


```sql
1.查询所有的商品：
select * from product;

2.查询商品名称和商品价格
select pname,price from product;

3.别名查询，as 的关键字 ，as关键字可以省略
	-表别名：select p.pname,p.price,from product p;(主要用在多表查询)
	select p.pname,p.price 
	from product as p;
	-列别名：select pname as 商品名称,price as 商品价格 from product;
	select pname as 商品名称,price as 商品价格 from product;

4.去掉重复的值
	-查询商品所有的价格
	select distinct price from product;

5.select运算查询
	select *,price*1.5 as 折后价 from product; 

6.条件查询[where关键字]
	指定条件，确定要操作的记录
	-查询商品价格大于60元的所有商品信息
	select * from product where price>60;

7.where 后的条件写法
	-关系运算符：>  >=  <  <=   =  !=  <>
		<> : 不等于   ：标准SQL语法
		!= : 不等于   ：非标准SQL语法
	-查询商品价格不等于88的所有商品
	select * from product where price <> 88;
	select * from product where price != 88;
	-查询商品价格在10到100之间的商品
	select * from product where price<100 and price >10;
	select * from product where price between 10 and 100;
	-逻辑运算：and   or   not
	-查询出商品价格 小于100 或者大于900
	select * from product where price <100 or price >900;
```

 - 复杂查询


```sql
1.like :模糊查询
	_ ：代表的是一个字符
	% ：代表的是多个字符
	in：在某个范围内获得值
	-查询出名字中带有饼的所有商品  ‘%饼%’
	select * from product where pname like '%饼%';
	-查询第二名字是熊的所有商品  '_熊%'
	select * from product where pname like '_熊%';
	-查询出商品分类ID在1，4，5里面的所有商品
	select * from product where cno in (1,4,5);

2.排序查询: order by  关键字
	asc:   ascend  升序（默认的排序方式）
	desc： descend 降序
	-查询所有商品，按照降序排序
	select * from product order by price desc;
	-查询名称有“小”的商品，按照升序排序
	select * from product where pname like '%小%' order by price asc;

3.聚合函数：
	-获得所有商品价格总和：
	select sum(price) from product;
	-获得所有商品价格平均值：
	select avg(price) from product;
	-获得所有商品的个数：
	select count(*) from product;

4.分组：group by
	-根据cno字段分组，分组后统计商品的个数
	select cno,count(*) from product group by cno; 
	-根据cno字段分组，分组后统计商品的平均价格，并且商品平均价格 >60
	select cno,avg(price) from product group by cno having avg(price) >60;
	-having 关键字，他可以接聚合函数的，  出现在分组之后
	-where 关键字，他不可以接聚合函数，出现在分组之前
```
**补充：**
```sql
编写顺序：
S..F..W..G..H..O
select..from..where..group by..having..drder by
执行顺序：
F..W..G..H..S..O
from..where..group by..having..select..drder by
```
