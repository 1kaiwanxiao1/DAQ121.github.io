---
title: MySql多表操作
author: 代澳旗
tags:
    - MySql
categories:
    - 数据库 
date: 2020-02-02 20:05:03
---
**问：** 分类表和商品表之间是不是有关系? 如果有关系,在数据库中如何表示这种关系？
<!--more-->
# 创建多表及多表的关系
 **问：** 分类表和商品表之间是不是有关系? 如果有关系,在数据库中如何表示这种关系？
```sql
create table category(
  cid int primary key auto_increment,
  cname varchar(10),
  cdesc varchar(31)
);

insert into category values(null,'手机数码','电子产品,黑马生产');
insert into category values(null,'鞋靴箱包','江南皮鞋厂倾情打造');
insert into category values(null,'香烟酒水','黄鹤楼,茅台,二锅头');
insert into category values(null,'酸奶饼干','娃哈哈,蒙牛酸酸乳');
insert into category values(null,'馋嘴零食','瓜子花生,八宝粥,辣条');

select * from category;
select cname,cdesc from category;

--所有商品
1.商品ID
2.商品名称
3.商品的价格
4.生产日期
5.商品分类ID

商品和商品分类 : 所属关系
create table product(
	pid int primary key auto_increment,
  	pname varchar(10),
  	price double,
  	pdate timestamp,
  	cno int
);

insert into product values(null,'小米mix4',998,null,1);
insert into product values(null,'锤子',2888,null,1);
insert into product values(null,'阿迪王',99,null,2);
insert into product values(null,'老村长',88,null,3);
insert into product values(null,'劲酒',35,null,3);
insert into product values(null,'小熊饼干',1,null,4);
insert into product values(null,'卫龙辣条',1,null,5);
insert into product values(null,'旺旺大饼',1,null,5);
```

 - 多表之间的关系如何来维护


```sql
外键约束: foreign key
	-给product中的cno 添加一个外键约束
	 alter table product add foreign key(cno)  references  category(cid);
	-从分类表中,删除分类为5信息
	delete from category where cid =5;  //删除失败,
	首先得去product表, 删除所有分类ID5  商品
	delete from product where cno=5;
```
 - 建数据库原则:  **通常情况下,一个项目/应用建一个数据库**
 - 多表之间的建表原则


```sql
1. 一对多 : 商品和分类
    建表原则: 在多的一方添加一个外键,指向一的一方的主键

2.多对多: 老师和学生, 学生和课程
    建表原则: 建立一张中间表,将多对多的关系,拆分成一对多的关系,中间表至少要有两个外键,分别指向原来的那两张表。

3.一对一: 班级和班长, 公民和身份证, 国家和国旗
    建表原则:  
      - 将一对一的情况,当作是一对多情况处理,在任意一张表添加一个外键,并且这个外键要唯一,指向另外一张表
      - 直接将两张表合并成一张表
      - 将两张表的主键建立起连接,让两张表里面主键相等

4.实际用途: 用的不是很多.    (拆表操作  )
	如：相亲网站: 
        - 个人信息 : 姓名,性别,年龄,身高,体重,三围,兴趣爱好,(年收入,  特长,学历, 职业, 择偶目标,要求)
        - 拆表操作 : 将个人的常用信息和不常用信息,减少表的臃肿,
```
# 网上商城表案例分析：用户购物流程

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200221150553826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
-   商品分类表(分类ID,分类名称,分类描述


```sql
 create table category(
    	cid int primary key auto_increment,
      	cname varchar(15),
      	cdesc varchar(100)
    );

    insert into category values(null,'手机数码','电子产品,黑马生产');
    insert into category values(null,'鞋靴箱包','江南皮鞋厂倾情打造');
    insert into category values(null,'香烟酒水','黄鹤楼,茅台,二锅头');
    insert into category values(null,'酸奶饼干','娃哈哈,蒙牛酸酸乳');
    insert into category values(null,'馋嘴零食','瓜子花生,八宝粥,辣条');
```
-   商品表 (商品ID, 商品名称,商品价格,外键cno)

```sql
create table product(
    	pid int primary key auto_increment,
      	pname varchar(10),
      	price double,
      	cno int,
      	foreign key(cno) references category(cid)
    );

    insert into product values(null,'小米mix4',998,1);
    insert into product values(null,'锤子',2888,1);
    insert into product values(null,'阿迪王',99,2);
    insert into product values(null,'老村长',88,3);
    insert into product values(null,'劲酒',35,3);
    insert into product values(null,'小熊饼干',1,4);
    insert into product values(null,'卫龙辣条',1,5);
    insert into product values(null,'旺旺大饼',1,5);
```

 - 用户表


```sql
create table user(
  	uid int primary key auto_increment,
    	username varchar(31),
    	password varchar(31),
    	phone  varchar(11)
  );
  
  insert into user values(1,'zhangsan','123','13811118888');
```
- 订单表 (订单编号,总价,订单时间 ,地址,外键用户的ID)


```sql
create table orders(
    	oid int primary key auto_increment,
      	sum int not null,
        otime timestamp,
      	address varchar(100),
      	uno int,
      	foreign key(uno) references user(uid)
    );
    insert into orders values(1,200,null,'学校',1);
    insert into orders values(2,250,null,'家里',1);
```

- 订单项: 中间表(订单ID,商品ID,商品数量,订单项总价)

```sql
 create table orderitem(
  	ono int,
    	pno int,
    	foreign key(ono) references orders(oid),
    	foreign key(pno) references product(pid),
    	ocount int,
    	subsum double
  );
  --给1号订单添加商品 200块钱的商品
  insert into orderitem values(1,7,100,100);
  insert into orderitem values(1,8,101,100);

  --给2号订单添加商品 250块钱的商品 ()
  insert into orderitem values(2,5,1,35);
  insert into orderitem values(2,3,3,99);
```

 - **内连接查询**

```sql
-隐式内连接
	select * from product p,category c where p.cno=c.cid;
-显式内连接
	select * from product p inner join category c on p.cno=c.cid;
-区别：
	隐式内连接：在查询出结果的基础上去做where 条件过滤
	显式内连接：带着条件去查询结果，执行效率高。
```

 - **连接查询**
左外连接：会将左表中的所有数据都查询出来，如果右表中没有对应的数据，用NULL代替。
右外连接：会将右表中的所有数据查询出来如果左表没有对应数据的话![在这里插入图片描述](https://img-blog.csdnimg.cn/20200221170140826.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - **分页查询**
每页数据数据3，起始索引从0 ，第1页: 0，第2页: 3。
起始索引:  index 代表显示第几页 页数从1开始，每页显示3条数据

```sql
 startIndex  = (index-1)*3
```
 - 第一个参数是索引 
第二个参数显示的个数

```sql
select * from product limit 0,3;
select * from product limit 3,3;
```
 - **子查询**

 sql的嵌套：查询语句里面嵌套查询语句
