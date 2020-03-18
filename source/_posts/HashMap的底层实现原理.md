---
title: 一、HashMap的底层实现原理
author: 代澳旗
tags:
    - HashMap
categories:
    - 每天一道面试题
date: 2020-03-14 20:05:03
---
HeshMap是基于哈希表的Map接口的实现，此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了非同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。 此实现假定哈希函数将元素适当地分布在各桶之间，可为基本操作（get 和 put）提供稳定的性能。迭代 collection 视图所需的时间与 HashMap 实例的“容量”（桶的数量）及其大小（键-值映射关系数）成比例。所以，如果迭代性能很重要，则不要将初始容量设置得太高（或将加载因子设置得太低）。在面试中也经常出现这道考题，记录一下！
<!--more-->


# HsahMap的实现原理

## 简要概括

-  HashMap 基于 Hash 算法实现的，底层是由数组+链表/红黑树构成的，我们通过 put(key,value)存储，get(key)来获取。当传入 key 时，HashMap 会根据 key. hashCode() 计算出 hash 值，根据 hash 值将 value 保存在 bucket 里。当计算出的 hash 值相同时，我们称之为 hash 冲突，HashMap 的做法是用链表和红黑树存储相同 hash 值的 value。当 hash 冲突的个数比较少时，使用链表，否则使用红黑树。


## HashMap的存取实现
- HashMap通过**键值对**实现存取。
- `put（）`方法：对key做null检查。如果key是null，会被存储到table[0]，因为null的hash值总是0。 key的hashcode()方法会被调用，然后计算hash值。hash值用来找到存储Entry对象的数组的索引。有时候hash函数可能写的很不好，所以JDK的设计者添加了另一个叫做hash()的方法，它接收刚才计算的hash值作为参数。

```java
 public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    /**
     * Implements Map.put and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @param value the value to put
     * @param onlyIfAbsent if true, don't change existing value
     * @param evict if false, the table is in creation mode.
     * @return previous value, or null if none
     */
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
  		 Node<K,V>[] tab; 
  		 Node<K,V> p; 
  		 int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

- `Get（）`：对key进行null检查。如果key是null，table[0]这个位置的元素将被返回。
key的hashcode()方法被调用，然后计算hash值。indexFor(hash,table.length)用来计算要获取的Entry对象在table数组中的精确的位置，使用刚才计算的hash值。在获取了table数组的索引之后，会迭代链表，调用equals()方法检查key的相等性，如果equals()方法返回true，get方法返回Entry对象的value，否则，返回null。

```bash
 public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

    /**
     * Implements Map.get and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @return the node, or null if none
     */
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```
补充：
1. HashMap有一个叫做Entry的内部类，它用来存储key-value对。
2. 上面的Entry对象是存储在一个叫做table的Entry数组中。
3. table的索引在逻辑上叫做“桶”(bucket)，它存储了链表的第一个元素。
4. key的hashcode()方法用来找到Entry对象所在的桶。
5. 如果两个key有相同的hash值，他们会被放在table数组的同一个桶里面。
6. key的equals()方法用来确保key的唯一性。

#  有关知识的具体解析
## 一、Map的几种类型 ##
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200314144530842.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- Map就是一个值key对应一个value。
- Hashtable（线程安全）和HashMap（非线程安全）在代码实现上，基本上是一样的。现在Hashtable已经过时了(小写的t，因为sun当时的一个失误，因为是JDK1.0的产物，所以不方便改)。
- ConcurrentHashMap也是线程安全的，但性能比HashTable好很多，Hashtable是锁整个Map对象，而ConcurrentHashMap是锁Map的部分结构。

## 二、什么是哈希表？

- 利用**数组**寻址容易，但插入和删除困难。而**链表**是：寻址困难，插入和删除容易。而**哈希表**便综合两者的特性，是一种寻址容易，插入删除也容易的数据结构。
- 哈希表有多种不同的实现方法，最常用的方法—— 拉链法，可以理解为“链表的数组” 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200314161134205.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
- 一个长度为16的数组中，每个元素存储的是一个链表的头结点。这些元素是按照什么样的规则存储到数组中呢？一般情况是通过`hash(key)%len`获得，也就是元素的key的哈希值对数组长度取模得到。
- 比如上述哈希表中12%16=12 , 28%16=12 , 108%16=12 , 140%16=12。所以12、28、108，140都存储在数组下标为12的位置。
- **HashMap其实也是一个线性数组（`Entry[]`）实现的,所以可以理解为其存储数据的容器就是一个线性数组。但是一个线性的数组怎么实现按键值对来存取数据呢？这里HashMap是做了一些处理的。**


## 三、什么是哈希算法？
- Hash算法虽然被称为算法，但实际上它更像是一种思想。Hash算法没有一个固定的公式，只要符合散列思想的算法都可以被称为是Hash算法。
- 哈希（hash）算法又称为散列算法，通过hash算法，可以将任意长度的信息转换成一个固定长度的二进制数据，我们经常会使用十六进制值来表示转换后的信息。
- 比如，数字123，使用md5的hash算法后，得到十六进制的值：202cb962ac59075b964b07152d234b70
- 哈希算法的特点：
（1）不同的信息，理论上得到的hash值不同，我们称之为“无碰撞”，或者发生“碰撞”的概率非常小。
（2）不可逆，hash算法是单向的，从hash值反向推导出原始信息是很困难的。所以，有些系统中，我们可以使用hash算法对密码进行处理后保存。
- 哈希算法的应用
①

## 四、什么是红黑树？
- **二叉树（BST）**：
①左子树结点的值小于等于根节点的值。
②右子树结点的值大于等于根节点的值。
③左右子树分开来也是单独的二叉树。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200314174010980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - **红黑树（RBT）**：红黑树是一种自平衡的二叉树，除了符合二叉树的基本特征之外还引入了一些附加的条件。
①节点是红色或黑色。
②根节点是黑色。
③每个叶子节点都是黑色的空节点（NIL节点）。
④每个红色节点的两个子节点都是黑色。(从每个叶子到根的所有路径上不能有两个连续的红色节点)。
⑥从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200314174041551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

## 五、HashMap 和 Hashtable 有什么区别？
- HashMap是非线程安全的，HashMap是Map的一个实现类，是将键映射到值的对象，不允许键值重复。允许空键和空值；由于非线程安全，HashMap的效率要较 Hashtable 的效率高一些。
- Hashtable 是线程安全的一个集合，不允许 null 值作为一个 key 值或者value 值。             
- Hashtable是sychronized，多个线程访问时不需要自己为它的方法实现同步，而HashMap 在被多个线程访问的时候需要自己为它的方法实现同步。
- 一般现在不建议用Hashtable：
①注意是小写的t，这是sun公司的一个失误，但是由于是JDK1.0的产物，所以没有改
②是Hashtable是遗留类，内部实现很多没优化和冗余。
③即使在多线程环境下，现在也有同步的ConcurrentHashMap替代，没有必要因为是多线程而用HashTable。

# 如何解决hash冲突
## 产生hash冲突的原因
- 当我们通过put(key, value)向hashmap中添加元素时，需要通过hash函数确定元素究竟应该放置在数组中的哪个位置，因为不同的元素可能通过hashcode（）计算得到的哈希值相同，那么不同的元素被放置在了数据的同一个位置时，后放入的元素会以链表的形式，插在前一个元素的尾部，这个时候我们称发生了hash冲突。

## 解决方法
- 事实上，想让hash冲突完全不发生，是不太可能的，我们能做的只是尽可能的降低hash冲突发生的概率。
①开放定址法
②链地址法（拉链法）
Java 中 HashMap 解决 Hash 冲突就是利用了这个方法，具体实现这里暂时不做详解，可以参考 Jdk HashMap 源码进行理解。
③再哈希法
④建立公共溢出区


