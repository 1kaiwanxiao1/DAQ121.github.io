---
title: HashMap源码分析
toc: true
date: 2020-04-20 11:27:37
tags: HashMap
categories: 每天一道面试题
abbrlink: HashMap

---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;之前有一篇关于HashMap底层原理的分析，由于没有结合源码，所以只停留在浅显的层面，这篇结合JDK1.7和1.8的源码，分析一下HashMap的底层实现。
<!--more-->
- **全文概要**


| 版本 | 1.7 | 1.8 |
|--|--|--|
| 底层实现 | 数组+链表 | 数组+链表/红黑树 |
| 存值 | Entry<key，value>对象 | Node（key，value）节点 |
| 初始容量 | 小于传入数的2的幂次方数（移位运算） | 大于传入数的2的幂次方数 |
| 哈希值 | 高低位混合运算（异或和移位） | 高低位混合运算 | 
| 扩容机制 | 头插法 | 尾插法/链表长度大于8时转化为红黑树 |
|缺点| 链表反转，形成循环链表，线程不安全 | 高度依赖hash算法，如果key是自定义类害得重写hashcode（），线程不安全 |

## JDK1.7
### 大致了解
- `JDK1.7`：数组+链表
- `key`和`value`组合成一个`entry`对象，将这个对象的引用地址存放在数组中。
- `table`数组

```java
1.根据key先算出来一个hash值
int hash = key.hashcode();

2.用这个hash值去跟数组长度(table.length)做与操作
int i = hash & (length-1); //得到的这个i就是数组下标

3.就把这个key对应的value放入数组下标i对应的位置
table[i]=value;
```
- 那为什么要用链表呢？

1. 再传进来一个`value`值,万一这个值对应的`key`算出来的`hash`值跟之前的重复了怎么办？这就要用到链表了。
2. 将这个`key`对应的`value`值放在数组的同一个位置，只不过是以链表的形式。
3. 那怎么放呢？是把这个元素放在链表的头部还是尾部呢？当然是采用**头插法**，因为尾插法效率低（他要遍历整个链表，要知道尾结点是谁，才能放入。）而放在头部只需要将`next`指向链表的头结点。
4. 那么用`put（）`存进去了，用`get()`怎么取呢？，因为`get()`也是通过计算`hash`值，得到数组下标，但是这个下标上，有一个链表呀，怎么知道取的是哪个值呢？**所以说：** 一个新的结点插入到头部之后，要往下移动一个位置，（虽然说是移动一个位置，实际上是将这个新元素的值赋值给`table[i]`,这样，头结点在哪里，链表就在那里）那么取链表上的元素就好取了。

### 源码分析
- 先分析一下`put()`方法


```java
   public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
```

- 通过查看源码第一行的`if语句`可以看出，只有当要往里面存放元素的时候，才去初始化这个桶，懒加载（延迟初始化），在初始化数组的时候，调用了`inflateTable(threshold)`方法

```java
 private void inflateTable(int toSize) {
        // Find a power of 2 >= toSize
        int capacity = roundUpToPowerOf2(toSize);

        threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        table = new Entry[capacity];
        initHashSeedAsNeeded(capacity);
    }
```
- 在其中又调用了`roundUpToPowerOf2(toSize)`方法。

```java
    private static int roundUpToPowerOf2(int number) {
        // assert number >= 0 : "number must be non-negative";
        return number >= MAXIMUM_CAPACITY
                ? MAXIMUM_CAPACITY
                : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
    }
```

- `highestOneBit（）`方法,通过移位和或运算计算出小于`i`的`2的幂次方数capacity`（这就是庶族的初始化容量），比如说，传入10，计算得出8，传入19，计算得出16。这个方法里面的运算是先把传入的数转化为二进制，然后在进行移位位运算和或运算，就像这个方法名字一样：得到最高位的那一位，自然而然的得出来的数就是2次幂的数了。得出来的这个数就是`table数组`的长度，jdk的作者对于位运算的使用已经达到出神入化的境界。
- 这样做的目的是，如果你修改了这个`number值`，他也会在加载的时候将这个值转化小于`number`的2的幂次方数。

```java
 public static int highestOneBit(int i) {
        // HD, Figure 3-1
        i |= (i >>  1);
        i |= (i >>  2);
        i |= (i >>  4);
        i |= (i >>  8);
        i |= (i >> 16);
        return i - (i >>> 1);
    }
```

- 再看`indexFor()`方法，是通过将算出来的`hash值`与数组长度进行一个与运算，得到这个数组下标`i`。这个`table[i]`就是要存的位置。

```java
static int indexFor(int h, int length) {
// assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);
            }
思考为什么要length-1？
因为length是2的幂次方数
经过与运算之后，只得到低四位有效，有效的低四位取值范围就是0-15
就得出了下标在0-15这个范围之内。
```
- 这就可以解决以上的一个问题了，为什么hashmap在初始化一个数组的容量的时候，要是一个2的幂次方数，因为要配合`indexFor()`计算出数组下标，对应存储。但是这样算出来的`hash值`会覆盖掉很多种情况，这就导致了很多元素算出来的下标值一样，存储在同一链表上，链表就会很长，进而影响到`get()`的效率，因为`get()`要循环链表嘛，所以回到上一步，可以看到`hash()方法`。
- 再看`hash()`方法（并不是`hashcode()方法哦`），它里面有异或和移位运算（这样高位就参与到运算了--高低位混合运算），可以理解为`再次哈希`，这样就增加了散列性。解决了的单条链表很长的问题。如果我们要改写`hashcode（）`，但是我们水平不行，改了之后导致返回的`hashcode值`非常不均匀，那么这个`hash()方法`就会帮我们容错。

```java
 final int hash(Object k) {
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        h ^= k.hashCode();

        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
```
- 再看`for循环`，它要做的事就是遍历链表，看看有没有key值相同的，比如说`put(1,2);`和`put(1,3)`，可以看到，两个key值是相等的，for循环判断之后，就会用新的值覆盖老的值，再返回老的值`oldValue`。
- `modCount++`跟多线程并发有关。
- `addEntry(hash,key,value,i)`这才是真正的将`key和value`放到数组上面去。`if`里面的语句就是涉及到扩容了，`threshold`是通过阈值`capacity`乘加载因子`DEFAULT_LOAD_FACTOR `得到的，如16*0.75=12，可以看到扩容要满足两个条件。这里的`size`是指当前数组占用了多少，而不是指链表的长度。后面那个条件是指数组中没有位置为空了，**大于阈值，并且发生过一次碰撞之后就扩容！！！**

```java
 void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
```
- 扩容的目的就是为了提高效率，因为链表上元素太多了，就会影响`get()`的效率。点开`resize()`方法可以看到，先`new`出来一个新的数组，长度是原先的两倍，然后在调用`transfer()`方法将老元素移动到新数组中。在转移的时候，将一个长链表拆成两个个短链表，减轻了压力。

```java
 void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
```
- 扩容是怎么来实现的呢？看这个`transfer()`方法中显示，有需要进行`rehash`的情况，也有不需要`rehash`的情况。看到`for()循环`是遍历这个数组上有没有空，而嵌套的`while()`循环是遍历这个链表上的元素。
1. 先看不需要`rehash`的情况（**适用于单线程**）此时`rehash=false`，可以看到下面又使用了`indexFor`方法，传入的是被遍历的这个元素的hash值，和新数组的长度，计算得出一个值`i`，这就是新数组的下标，把老元素放到`newTable[i]`上。事实上，根据运算得到的`i`值和原先是一样的，因为原本的hash值没有变，只是拿过来用，而变化的是数组长度，但是并不会影响算出来的`i`值，所以说，传到新数组后，下标还是没有变。但是这样引发了扩容之后的一个问题：**链表上的元素全部倒过来了，意思就是新链表的顺序和之前的完全颠倒了。**
2. 多线程情况下，如果有两个线程同时调用haspmap的这个对象，都会使用到`put()`方法，如果最终同时走到了`resize()`方法中，各种混乱的走法之后，**最终会产生一个循环链表，最终耗尽CPU的资源。**--这就是haspmap扩容带来的第二个问题
3. 再看需要`rehash`的情况，其实很少会走到`rehash()`中，在`rehash（）`中打了一个断点，测试的时候无限`put()`,却发现，始终不会执行`rehash（）`。

```java
void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
```
### 归纳总结
- **HashMap底层是怎么实现的呢？**
答：在jdk1.7的时候，底层是由数组+链表实现的，由key和value组成一个entry对象，用put方法将key和value传入，当调用这个put方法的时候，就会初始化一个数组（也就是所说的延迟初始化），这个数组的初始容量给的是16，负载因子是0.75，根据这个key值使用hash方法算出hash值，再用这个hash值跟数组的长度做模运算，得到一个数组下标，就将这个元素的值放入到对应下标的位置，那如果再传入的另一个元素算出来的hash值也是一样的（这就叫hash冲突），那么进而算出来的下标也是一样的，这就要用到链表了，使用头插法，并保证最新插入的这个元素是整个链表的头结点，就实现了存储的过程。
- **扩容的机制？**
 在put的时候，有一个方法是`addEntry`，先判断当前的size是不是大于阈值，这里的阈值就是（数组初始长度*加载因子），默认的就是12，大于这个阈值，并且发生了hash碰撞的时候，就要进行扩容了，就是执行`resize()`方法，扩容的方法就是，new一个新数组，长度是原数组长度的两倍，然后在用`transfer`方法将老数组中的元素转移过去，还重新算一下扩容之后的阈值，这样做出现的问题就是：在单线程情况下还好，只不过转移过去的链表会反转，**但是在多线程的情况下**，就会出现一个闭环，会把cpu的资源耗尽。这里面还有一个`rehash()`方法，但是这个方法我并没有具体研究，涉及到一个哈希种子，其实这个rehash方法并不会用到。在源码rehash中打个断点，测试代码中无限put操作，发现程序并不会停止。
- **HashMap为什么是线程不安全的？**
HashMap在put的时候，超过的阈值就会触发扩容操作，就是rehash，这个会重新将原数组的内容重新hash到新的扩容数组中，在多线程的环境下，存在同时其他的元素也在进行put操作，如果hash值相同，可能出现同时在同一数组下用链表表示，造成闭环，导致在get时会出现死循环，所以HashMap是线程不安全的。

## JDK1.8
### 大致了解
- 在1.7中叫entry对象，在1.8中叫node结点。
- **在1.8中加入了红黑树，为什么加红黑树？**
虽然在1.7中实行了很多措施，如扩容，去增加元素的散列性，使链表的长度更短一些，但是还是会出现一些极端情况，某些地方链表还是会很长，所以加入了红黑树，当链表达到一定长度大于8的时候，就要先看数组，决定是否要扩容，然后在不行就要换用红黑树了。当红黑树结点小于6的时候，又变为链表了。
- **为什么链表上限设置为8，为红黑树下限设置为6呢？为什么不设置为8？**
这样是为了防止平凡的插入和删除，打个比方，当我插入第9个元素的时候，这就要变为红黑树了，可是我马上又删除了一个元素，红黑树又要变成链表，这样不停的来回转换，会拉低效率，所以将红黑树结点下限设置为6。



### 源码分析
- 分析put（）方法。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200416091904314.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200416094525420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

- 在1.8中初始化容量的时候，和1.7有不同，如果你传入的一个数字，会调用`tableSizeFor()`方法进行移位运算，最终得到一个大于你传入的这个数字的数（这个数依旧是2的次幂），而1.7中得到的这个数字是小于你传入的这个数。这个数用来作为数组的初始容量。

- 在1.8中采用尾插法加入元素，这是因为，每次加入元素的时候，本来就要遍历一下链表，看有没有超过八个结点，超过了就变成红黑树，总之是要遍历的，直接用尾插法，一举两得。
- 在遍历的过程中，还要看有没有与老元素key值相同的，相同则覆盖掉，除此之外还要判断是否为红黑树。
- **关于树化:** 点开treeifyBin方法发现：在链表上的结点大于八个的时候，不会第一时间去树化，而是先判断是否需要扩容，然后再去决定要不要树化，其实扩容大概率能减少链表的长度了。一举两得，扩容比树化优点好多了。基于对内存的一个节省，不能扩容的太大，就用红黑树。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041609544419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

- 1.8里的扩容只要满足一个条件，而1.7中需要满足两个。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200416094609702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

- 扩容详解，条件只有一个，但是代码却长了很多，核心思想就是：使用2次幂的扩展（长度变两倍）所以，要么元素的位置是在原位置，要么是在原位置基础上移动2次幂的位置。

```java
  final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

- 看到里面的算hash值的方法。跟1.7的差不多。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200416092054872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
### 总结归纳
- JDK1.8中，HashMap是怎么实现的？
在JDK1.8中，底层是由数组+链表/红黑树实现的，将key和value封装成一个node结点，计算hash值和1.7是差不多的，在put的时候，有两点要注意，一个是判断新元素key值是否与老元素相等，相等则覆盖掉，不相等就直接插入，另一点就是判断是链表还是红黑树，选择遍历的方式，采用尾插法来插入元素，也不用担心需要遍历整个链表，因为判断链表上结点个数的时候总是要遍历的，这里值得注意的是，在判断是否需要变为红黑树的时候，（也就是链表结点大于等于8的时候）首先要考虑的是扩容，因为扩容大概率能够解决链表长度的问题，而且还增大了空间，但是不能一味的扩容，这样会导致内存浪费，所以就转换成红黑树了。

## 对比JDK1.7和1.8
|  |1.7| 1.8 |
|--|--|--|
| **底层实现** | 数组+链表 | 数组+链表/红黑树 |
| **形式** | Entry<key，value>对象 | Node<key,value>结点 |
| **下标值** | hash &（length-1） | （n-1）& hash |
| **元素插入** | 头插法 | 尾插法 |
| **扩容机制** | resize（链表反转，循环链表） | 解决了链表反转的问题 |
| **查询效率** | 链表：n | 红黑树：log（n） |
| **扩容**| transfor（）方法，新数组两倍长度 | resize（）方法 |


## 常见问题
- **HashMap线程不安全，为什么还要用它？**
它根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序却是不确定的。HashMap最多只允许一条记录的键为null，允许多条记录的值为null。HashMap非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果需要满足线程安全，可以用 Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap。但综合各种因素，首要推荐使用ConcurrentHashMap。

- **ConcurrentHashMap实现**
 ConcurrentHashMap基于lock实现锁分段技术。首先将Map存放的数据分成一段一段的存储方式，然后给每一段数据分配一把锁，当一个线程占用锁访问其中一个段的数据时，其他段的数据也能被其他线程访问。ConcurrentHashMap不仅保证了多线程运行环境下的数据访问安全性，而且性能上有长足的提升。

- **ConcurrentHashMap有哪些优点？**
1. 线程安全的，在多线程下效率更高。
2. ConcurrentHashMap对整个桶数组进行了分段，而HashMap则没有。
3. ConcurrentHashMap在每一个分段上都用锁进行保护，从而让锁的粒度更精细一些，并发性能更好，而HashMap没有锁机制，不是线程安全的。 

- **ConcourrentHashMap和Hashtable都是线程安全的，为什么不用Hashtable？**
1. hashtable:使用一把锁处理并发问题，当有多个线程访问时，需要多个线程竞争一把锁，导致阻塞。
2. concurrentHashMap则使用分段，相当于把一个hashmap分成多个，然后每个部分分配一把锁，这样就可以支持多线程访问。


