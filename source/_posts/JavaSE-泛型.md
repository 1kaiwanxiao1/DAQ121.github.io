---
title: JavaSE---泛型
toc: true
date: 2020-03-12 13:45:03
tags: JavaSE
categories: JavaSE基础知识
abbrlink: 泛型

---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在JDK1.5，引进泛型这个概念。是指在创建对象或者调用方法的时候，再去明确类型。即参数化类型，简单来说，就是把类型当作一种参数来传递，只不过一般情况下参数写在（）中，而类型写在<>中。并且在<>中只能是引用类型。
<!--more-->
## 泛型是什么？
- **泛型本质：** 在创建对象或者调用方法的时候，再去明确类型。
- **引用类型（reference type）：** 指向一个对象，而不是原始值，指向对象的变量是引用变量。在java里面除去基本数据类型的其它类型都是引用数据类型，自己定义的class类都是引用类型，可以像基本类型一样使用。


- **泛型的设计原则：**  只要在编译时期没有出现警告，那么运行时期就不会出现`ClassCastException`异常。

- **一些常用的泛型类型变量：**


    E：元素（Element），多用于java集合框架
    K：关键字（Key）
    N：数字（Number）
    T：类型（Type）
    V：值（Value）


## 泛型对比测试
- 首先来看不加泛型的情况下，在list集合中添加不同类型的数据，并且在遍历的时候强转，编译的时候注定会报`ClassCastException`异常的。这就是由于集合中元素类型不统一造成的。

```java
List arrayList = new ArrayList();
//添加一个String类型的元素
arrayList.add("aaaa");
//添加一个Integer类型的元素
arrayList.add(100);

for(int i = 0; i< arrayList.size();i++){
	//都强转成String类型
    String item = (String)arrayList.get(i);
    Log.d("泛型测试","item = " + item);
}
```
- 加上泛型之后的，就规定了，这个集合中只能装这个类型的元素，要是装别的，在敲代码的时候就会报错，因为你加入的元素，跟规定的类型不一致，不让你加。

```java
List<String> arrayList = new ArrayList<String>();
```

## 为什么要使用泛型？
- 早期用Object来代替任意类型，但是这样做，有的时候就要进行向下强转，这样做不太安全。
- 没有泛型的话，像`Collection`，`Map`这种不限制元素类型的集合，你可以往里面丟任何元素，并且不会报语法错误，但是集合不知道这个元素是什么类型的，默认都是`Object`类型，等你取出来的时候，就给你返`Object`类型的，可谓乱丢一时爽，`get`火葬场。
- 有了泛型呢，就不用强制转换了，因为你在事先就规定好了，这个集合中装什么类型的元素，使代码更加简洁。程序也更加健壮（这才是猛男该做得事），因为只要编译没有警告，那么运行的时候就不会出现`ClassCastException`异常了。


## 使用泛型有什么好处？
- 上面通过对比已经看出，泛型有哪些好处，这里在总结一下。
①代码更简洁
②程序更健壮
③可读性和稳定性
④配合增强for循环遍历集合


## 怎么使用泛型？

### 泛型类
-  把泛型定义在类上，也可以定义在方法上，在使用该类或者方法的时候，才根据自己的需要，将类型明确下来。**注意：** **类上声明的泛型，只对非静态成员有效。**

```java
1.定义
//把泛型定义在类上
public class ObjectTool<T>{
	private object;
	
	public T getObj(){
	return object;
	}
	
	public void setObj(T object){
	this.object=object;
	}
}
```

```java
2.使用
public static void main（String[] args）{
	//创建对象，并指定其元素类型(这里是String型)
	ObjectTool<String> tool1 = new ObjectTool<>();
	
	tool1.setObj(new String("张三"));
	String s=tool.getObj();
	System.Out.println(s);
	
	//创建对象，并指定其元素类型(这里是Integer型)
	ObjectTool<Integer> tool2 = new ObjectTool<>();
	
	tool2.setObj(10);
	int i=tool2.getObj();
	System.Out.println(i);
}
```
### 泛型方法
- 如果外界仅仅对一个方法感兴趣，而不关心类中的其他属性，那么将泛型定义在类上就有些小题大做，这里直接定义在方法上，精准打击！！

```java
1.定义泛型方法
	public <T> void show(T t){
	System.Out.println(t);
	}
```
```java
2.泛型方法的使用
public static void main (String[] args){
	//创建对象
	ObjectTool obj = new ObjectTool();

	//调用方法，传进来什么类型，返回值就是什么类型
	obj.show("hello");
	obj.show(4);
	obj.show(false);
}
```

### 泛型类派生出的子类
- 换上马甲他还是王八，虽然加了泛型，但说到底，他还是个类，既然是个类，那么他就可以被继承。然而泛型类的继承也分两种。

①**子类明确泛型类的类型参数变量(泛型接口)**

```java
//把泛型定义在接口上
public interface Inter<T> {
    public abstract void show(T t);
}
```

```java
//子类明确泛型类的类型参数变量:
public class InterImpl implements Inter<String> {
    @Override
    public void show(String s) {
        System.out.println(s);
    }
}
```
②**子类不明确泛型类的类型参数变量**
- 此时，外界使用子类的时候，也需要传递类型参数变量进来，在实现类上需要定义出类型参数变量。


```java
//实现类要定义出<T>类型
public class InterImpl<T> implements Inter<T> {

    @Override
    public void show(T t) {
        System.out.println(t);
    }
}
```


### 类型通配符
- **问题：** 方法接收一个集合参数，遍历集合并把集合元素打印出来

```java
普通解决办法：
public void test(List list){
    for(int i=0;i<list.size();i++){
        System.out.println(list.get(i));
    }
}
```

```java
加类型通配符？
public void test(List<?> list){
    for(int i=0;i<list.size();i++){
        System.out.println(list.get(i));
    }
}
```
- 就加了一个问号？，能有什么作用
答案是：更优雅~
- 还有一个问题是设置通配符上限和下限。这个问题很绕，搞不清楚，当然，大多时候，使用泛型方法来替代通配符，条条大路通罗马嘛。
- **注意：加了？就只能调用与对象无关的方法，不能调用对象与类型有关的方法。因为直到外界使用才知道具体的类型是什么。也就是说，在上面的List集合，是不能使用add()方法的。因为add()方法是把对象丢进集合中，而现在不知道对象的类型。**


### 泛型擦除
- 因为泛型是提供给javac编译器使用的，它用于限定集合的输入类型，让编译器在源代码级别上，即**挡住向集合中插入非法数据**。但编译器编译完带有泛形的java程序后，生成的class文件中将不再带有泛形信息，以此使程序运行效率不受到影响，这个过程称之为“泛型擦除”。这一点应用在兼容老版本上，因为JDK1.5之前没有泛型，当把带有泛型特性的集合赋值给老版本的集合时候，就会把泛型擦除掉，amazing！

## 应用场景有哪些？
- **场景一：**最常用的还是应用于限制集合的参数类型。
- **场景二：**项目中，每次都要写好几个DAO，会显得臃肿。
- **优化办法--抽象DAO：** 在抽象DAO中，是不知道哪一个DAO会继承它，所以是不知道其具体的类型的。而泛型就是在创建的时候才指定其具体的类型。

```java
抽象DAO
public abstract class BaseDao<T> {
    private Session session;
    private Class clazz;

    //哪个子类调的这个方法，得到的class就是子类处理的类型（非常重要）
    public BaseDao(){
        Class clazz = this.getClass();  //拿到的是子类
        ParameterizedType  pt = (ParameterizedType) clazz.getGenericSuperclass(); 
         //BaseDao<Category>
        clazz = (Class) pt.getActualTypeArguments()[0];
        System.out.println(clazz);
    }

    public void add(T t){
        session.save(t);
    }

    public T find(String id){
        return (T) session.get(clazz, id);
    }

    public void update(T t){
        session.update(t);
    }

    public void delete(String id){
        T t = (T) session.get(clazz, id);
        session.delete(t);
    }

}
```

```java
继承抽象DAO
public class CategoryDao extends BaseDao<Category> {
	将抽象DAO中的方法继承了下来
	@override
	...
	...
	...
	}
	
public class BookDao extends BaseDao<Book> {
	@override
	...
	...
	...
	}
```
