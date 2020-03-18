---
title: JDBC学习
author: 代澳旗
tags:
    - [JDBC,MySql]
categories:
    - 数据库 
date: 2020-02-03 20:05:03
---
如何使用JDBC连接到数据库
<!--more-->
## 一、添加JDBC驱动包


![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022310204253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223102114577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/2020022310240148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
## 二、使用JDBC简单步骤

 - 1、注册驱动`


```java
DriverManager.registerDriver(new com.mysql.jdbc.Driver());
```
- 2、建立连接

```java
Connection conn=(Connection) DriverManager.getConnection("jdbc:mysql://localhost/数据库名", "root", "daq");
```
- 3、创建statement----跟数据库打交道，一定需要这个对象


```java
st = conn.createStatement();
```
- 4、执行sql 查询，得到结果集ResultSet


```java
String sql = "select * from t_stu";
ResultSet rs = st.executeQuery(sql);
```
- 5、遍历查询每一条记录


```java
while(rs.next()){
		int id=rs.getInt("cid");
		String name =rs.getString("cname");
		String desc =rs.getString("cdesc");
				
		System.out.println("cid"+id +"cname"+name +"cdesc"+desc);
	}
```
- 6、关闭，释放资源


```java
新建一个包放置适房资源的工具类，在JDBC释放资源时调用即可
public class JDBCUtil {

	/**
	 * @param args
	 * @deprecated 释放资源
	 */
	public static void release(ResultSet rs,Statement st,Connection conn){
		closeRs(rs);
		closeSt(st);
		closeConn(conn);
	}
	
	private static void closeRs(ResultSet rs){
		try {
			if (rs != null) {
				rs.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			rs=null;
		}
	}
	
	private static void closeSt(Statement st){
		try {
			if (st != null) {
				st.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			st=null;
		}
	}
	
	private static void closeConn(Connection conn){
		try {
			if (conn != null) {
				conn.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			conn=null;
		}
	}

```

## 三、JDBC工具类（修改原有代码）

 - 查看源代码，有一段静态代码块（类加载的时候就会执行），所以等同于说注册了两次驱动，其实驱动只需要注册一次就够了。
 -  **1、注册驱动（防止二次注册）**


```java
改成以下代码即可
Class.forName("com.mysql.jdbc.Driver");
引号里面是Driver的全路径
```
- **2、在工具类中加方法getconn（）**


```java
	static String url="jdbc:mysql://localhost/daq";
	static String name="root";
	static String password="daq";
	/**
	 * 获取连接对象
	 * @return
	 */
	public static Connection getconn() {
		Connection conn = null;
		try {
			// 1.注册驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 2.建立连接
			conn = (Connection) DriverManager.getConnection(
					url, name, password);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return conn;

	}
```
- **使用properties配置文件**

```java
1.创建properties
	在src下面新建一个**jdbc.properties** 文件
	写入：
	driverClass=com.mysql.jdbc.Driver
	url=jdbc:mysql://localhost/daq
	name=root
	password=daq
	
2.读取properties
	在JDBCUtil工具类中添加静态代码块，保证工具类一加载，就可以读取配置文件。
	static String driverClass = null;
	static String url = null;
	static String name = null;
	static String password = null;

	static {
		try {
			// 1.创建一个属性配置对象
			Properties properties = new Properties();
			InputStream is = JDBCUtil.class.getClassLoader().getResourceAsStream("jdbc.properties");
			// 导入输入流
			properties.load(is);
			// 读取属性
			driverClass =properties.getProperty("driverClass");
			url =properties.getProperty("url");
			name =properties.getProperty("name");
			password =properties.getProperty("password");
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

```
## 四、JDBC的CRUD

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223163750100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

* insert（插入）


```java
INSERT INTO t_stu VALUES (NULL,'wangqiang2',28)

		// 1. 获取连接对象
			conn = JDBCUtil.getconn();
			// 2. 根据连接对象，得到statement
			st = conn.createStatement();
			
			//3. 执行添加
			String sql = "insert into t_stu values(null , 'aobama' , 59)";
			//影响的行数， ，如果大于0 表明操作成功。 否则失败
			int result = st.executeUpdate(sql);
			
			if(result >0 ){
				System.out.println("添加成功");
			}else{
				System.out.println("添加失败");
			}
```

* delete（删除）

```java
DELETE FROM t_stu WHERE id = 6

	// 1. 获取连接对象
		conn = JDBCUtil.getConn();
		// 2. 根据连接对象，得到statement
		st = conn.createStatement();
		
		//3. 执行添加
		String sql = "delete from stu where name='XXX'";
		//影响的行数， ，如果大于0 表明操作成功。 否则失败
		int result = st.executeUpdate(sql);
		
		if(result >0 ){
			System.out.println("删除成功");
		}else{
			System.out.println("删除失败");
		}
```

* query（查询）

```java
SELECT * FROM t_stu

			// 1. 获取连接对象
			conn = JDBCUtil.getConn();
			// 2. 根据连接对象，得到statement
			st = conn.createStatement();
	
			// 3. 执行sql语句，返回ResultSet
			String sql = "select * from stu";
			rs = st.executeQuery(sql);
	
			// 4. 遍历结果集
			while (rs.next()) {
				String name = rs.getString("name");
				int age = rs.getInt("age");
	
				System.out.println(name + "   " + age);
			}
```
* update（更新）


```java
UPDATE t_stu SET age = 38 WHERE id = 1;
		// 1. 获取连接对象
			conn = JDBCUtil.getConn();
			// 2. 根据连接对象，得到statement
			st = conn.createStatement();
			
			//3. 执行添加
			String sql = "update t_stu set age = 26 where name ='qyq'";
			//影响的行数， ，如果大于0 表明操作成功。 否则失败
			int result = st.executeUpdate(sql);
			
			if(result >0 ){
				System.out.println("更新成功");
			}else{
				System.out.println("更新失败");
			}
```
- 补充：单元测试junit

```java
 - 定义一个类， TestXXX , 里面定义方法 testXXX.

 - 添加junit的支持。 

   	右键工程 --- add Library --- Junit --- Junit4

 - 在方法的上面加上注解 ， 其实就是一个标记。

   	@Test
   	public void testQuery() {
   		...
   	}

 - 光标选中方法名字，然后右键执行单元测试。  或者是打开outline视图， 然后选择方法右键执行。
```
## 五、DAO模式

 - **Data Access Object 数据访问对象**


 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200223175345761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 1、新建一个dao的接口， 里面声明数据库访问规则


```java
		/**
		 * 定义操作数据库的方法
		 */
		public interface UserDao {
		
			/**
			 * 查询所有
			 */
			void findAll();
		}
```

- 2、新建一个dao的实现类，具体实现早前定义的规则

```java
public class UserDaoImpl implements UserDao{
		@Override
		public void findAll() {
			Connection conn = null;
			Statement st = null;
			ResultSet rs = null;
			try {
				//1. 获取连接对象
				conn = JDBCUtil.getConn();
				//2. 创建statement对象
				st = conn.createStatement();
				String sql = "select * from t_user";
				rs = st.executeQuery(sql);
				
				while(rs.next()){
					String userName = rs.getString("username");
					String password = rs.getString("password");
					
					System.out.println(userName+"="+password);
				}
				
			} catch (Exception e) {
				e.printStackTrace();
			}finally {
				JDBCUtil.release(conn, st, rs);
			}
		}
	}
```
- 直接使用实现

```java
   	@Test
   	public void testFindAll(){
   		UserDao dao = new UserDaoImpl();
   		dao.findAll();
   	}
```
## 六、JDBC Statement安全问题

 -  Statement执行 ，其实是拼接sql语句的。  先拼接sql语句，然后在一起执行。


```java
String sql = "select * from t_user where username='"+ username  +"' and password='"+ password +"'";

	UserDao dao = new UserDaoImpl();
	dao.login("admin", "100234khsdf88' or '1=1");
	
	SELECT * FROM t_user WHERE username='admin' AND PASSWORD='100234khsdf88' or '1=1' 
	
	前面先拼接sql语句， 如果变量里面带有了 数据库的关键字，那么一并认为是关键字。 不认为是普通的字符串。 
	rs = st.executeQuery(sql);
```
## 七、PreparStatement  CRUD

 - 该对象就是替换前面的statement对象。
 - 相比较以前的statement， 预先处理给定的sql语句，对其执行语法检查。 在sql语句里面使用 ? 占位符来替代后续要传递进来的变量。 后面进来的变量值，将会被看成是字符串，不会产生任何的关键字。

```java
String sql = "insert into t_user values(null , ? , ?)";
		ps = conn.prepareStatement(sql);
			 
		//给占位符赋值 从左到右数过来，1 代表第一个问号， 永远你是1开始。
		ps.setString(1, userName);
		ps.setString(2, password);
```
- PreparStatement的添加，删除，更新，
