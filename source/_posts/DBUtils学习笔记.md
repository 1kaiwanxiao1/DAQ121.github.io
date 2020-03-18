---
title: DBUtils学习笔记
author: 代澳旗
tags:
    - [MySql,DBUtils]
categories:
    - 数据库 
date: 2020-02-06 20:05:03
---

DbUtils是Apache组织提供的一个对JDBC进行简单封装的开源工具类库，使用它能够简化JDBC应用程序的开发，同时也不会影响程序的性能。并且简化了增删改查的操作。
<!--more-->
# 使用开源的DBUtils
## 概述
- Commons DbUtils是Apache组织提供的一个对JDBC进行简单封装的开源工具类库，使用它能够简化JDBC应用程序的开发，同时也不会影响程序的性能。
- 简化了增删改查的操作。


## 用法
- 增删改


```bash
1.导入jar包  commons-dbutils-1.4.jar
public class TestDBUtils {
	@Test
	public void testInsert() throws SQLException {
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		// dbutils 只是帮我们简化了CRUD 的代码， 但是连接的创建以及获取工作。 不在他的考虑范围
		QueryRunner queryRunner = new QueryRunner(dataSource);
		// 增加
		queryRunner.update("insert into category values (null , ? , ? )", "a", 1000);
		// 删除
		queryRunner.update("delete from account where id = ?", 5);
		// 更新
		queryRunner.update("update account set money = ? where id = ?", 10000000, 6);
	}
}
```
- 查


```bash
1.直接new接口的匿名实现类
QueryRunner queryRunner = new QueryRunner(new ComboPooledDataSource());
	Account  account =  queryRunner.query("select * from account where id = ?", new ResultSetHandler<Account>(){

		@Override
		public Account handle(ResultSet rs) throws SQLException {
			Account account  =  new Account();
			while(rs.next()){
				String name = rs.getString("name");
				int money = rs.getInt("money");
					
				account.setName(name);
				account.setMoney(money);
			}
			return account;
		} 
	 }, 6);
	System.out.println(account.toString());

2. 直接使用框架已经写好的实现类。（最常用）
	QueryRunner queryRunner = new QueryRunner(new ComboPooledDataSource());
	//查询单个对象
Account account = queryRunner.query("select * from account where id = ?", 
			new BeanHandler<Account>(Account.class), 8);
	System.out.println(category.toString());
	
	* 查询多个对象
List<Category> list = queryRunner.query("select * from category", new BeanListHandler<Category>(Category.class));
	for (Category category : list) {
		System.out.println(category.toString());
	}
```
# ResultSetHandler 常用的实现类
- 以下两个是使用频率最高的

1. BeanHandler          查询到的**单个**数据封装成一个对象
2. BeanListHandler    查询到的**多个**数据封装 成一个List<对象>

3. ArrayHandler,  查询到的单个数据封装成一个数组。
4. ArrayListHandler,  查询到的多个数据封装成一个集合 ，集合里面的元素是数组。 
	
5. 	MapHandler,  查询到的单个数据封装成一个map
6. MapListHandler,查询到的多个数据封装成一个集合 ，集合里面的元素是map。 

7. ColumnListHandler
8. KeyedHandler
9. ScalarHandler
