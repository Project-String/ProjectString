---
title: JAVA-集合框架
date: 2020-11-09 14:00
description: 秋招面试常考点-JAVA集合框架
categories:
 - 后端
---

## 一、Collection and Map
- Java集合类主要由两个根接口Collection和Map派生出来的
	- 其中Collection又派生出三个子接口List、Set、Queue
- 以下介绍，若无指明，则默认为JDK1.8、非线程安全。介绍重点为HashMap

### 1.1 List主要实现类
- ArrayList
	- 底层数据结构：可改变大小的数组
		- ArrayList包含两个重要的对象，一是动态数组elementData，二是实际存储数据的大小size（与数组实际大小不一样）。创建ArrayList时可以指定动态数组创建的大小（默认容量大小是10）。除此之外，还有一个维护了一个变量modCount（其他的类也有这个变量）,记录着集合的修改次数(增删)，该变量在使用迭代器遍历的时候，用来检查列表中的元素是否发生结构性变化（列表元素数量发生改变）了，主要在多线程环境下需要使用，防止一个线程正在迭代遍历，另一个线程修改了这个列表的结构。（会抛出ConcurrentModificationException异常，快速失败机制）
	- 特点：查询和赋值速度快
	- ⭐扩容机制：
		- 插入数据时，执行ensureCapacityInternal(size+1)来校验并扩容。校验后若需要扩容，则先对扩容后的容量大小进行计算；首先newCapacity = oldCapacity + (oldCapacity >> 1)；若newCapacity仍然小于所需容量大小，则使用实际所需容量大小来进行扩容。扩容采用 Arrays.copyOf函数，其实现原理是根据给定的size创建一个新的相同类型的数组，然后调用System.arraycopy(native方法)将老数组的数据copy到新数组上，完成扩容过程。

- LinkedList
	- 底层数据结构：双向链表
	- 特点：增删速度快；LinkedList同时实现了Queue接口，支持队列操作

### 1.2 Set主要实现类
- HashSet
	- 底层数据结构：HashMap
	- 特点：元素不可重复
	- 实现原理：通过将存储的值作为HashMap的Key来保证唯一性。
- TreeSet
	- 底层数据结构：TreeMap
	- 优点：元素不可重复，且可排序
	- 实现原理：通过将存储的值作为HashMap的Key来保证唯一性，依托TreeMap进行排序

### 1.3 Queue主要实现类
- PriorityQueue
	- 底层数据结构：小顶堆
	- 特点：能保证每次取出的元素都是队列中权值最小的
	- 扩容机制：与ArrayList相似（计算容量大小有所不同），不累述

- LinkedList
	- 同上，不累述

### 1.4 Map主要实现类
- HashMap
	- 底层数据结构：数组+链表+红黑树
		- HashMap初始化时默认容量为16，负载因子为0.75。链表转红黑树的阈值是8（单个链表元素达到8个），红黑树退化回链表的阈值是6。当然，树化的前提条件是数组的容量要大于MIN_TREEIFY_CAPACITY=64，否则进行扩容而不进行树化。注意：数组的容量都是2的倍数（jdk1.8的一处优化），具体看<三>）
	- 特点：允许key==null，默认存在于table[0]。
	- 扩容机制：
		- 扩容条件：存放新值的时候当前已有元素的个数必须大于等于阈值（阈值=容量*负载因子，容量指数组大小）
		- 扩容：
			- 首先，确定扩容后的容量newCap和阈值newThr（这里指触发扩容机制的容量阈值），分为以下三种情况
				- 第一种：使用默认构造方法初始化HashMap。HashMap在一开始初始化的时候会返回一个空的table，并且thershold为0。因此第一次扩容的容量newCap为默认容量也就是16，newThr=默认容量*默认负载因子=16×0.75=12
				- 第二种：指定初始容量的构造方法初始化HashMap。则初始容量newCap会等于threshold（具体查看HashMap带参构造函数代码逻辑），接着newThr = 当前的容量（threshold） * DEFAULT_LOAD_FACTOR。
				- 第三种：HashMap不是第一次扩容。如果HashMap已经扩容过的话，那么每次table的容量newCap以及newThr为原有的两倍（这是jdk1.8的特性之一）
			- 然后，创建大小为newCap的数组newTable
			- 遍历原数组oldTable，将此前存入的节点都取出来，重新计算hash值（这里jdk1.8的设计比较巧妙）再插入newTable对应位置（与jdk1.7不同，这里采用尾插法，避免成环问题）。
			- newTable替换oldTable
	
- TreeMap
	- 底层数据结构：红黑树
	- 特点：允许key==null，默认存在于table[0]；可以对元素进行排序（自定义Comparator

- HashTable
	- 底层数据结构：数组+链表
	  - 与HashMap不同的是，初始容量cap为11，扩容时新容量=cap×2+1；（HashMap是cap*2）
	- 特点：线程安全(方法加了synchronized)，不允许Key为null。

## 二、并发场景下的问题及解决方案

- 并发场景存在的问题
  - 脏读，数据丢失
- 并发集合
  - ⭐ConcurrentHashMap（分段锁实现，支持多个线程同时操作）
  - CopyOnWriteArrayList
  - CopyOnWriteHashSet
  - Collections.synchronizedMap()方法（Collections.synchronizedXxx()类似，采用装饰模式，添加mutex对象作为锁对象，性能较低）

## 三、JDK1.7升级到1.8后的集合框架的关键优化

- HashMap由数组+链表结构变成数组+链表+红黑树结构
- 优化高位运算的Hash算法，h^(h>>>16)（将高16位也用于Hash计算，减少碰撞概率）
- 由于容量为2的倍数，所以根据index=hash&(cap-1)，扩容后，元素要么在本身位置，要么在原位置再移动2次幂的位置（比如原来在2现在变成4），且链表顺序不变。
- 在扩容的时候，摒弃jdk1.7的头插法，采用尾插法，避免成环问题（成环问题看[参考.1]）。
- ConcurrentHashMap的优化
  - 取消segments字段，直接将table数组的元素作为锁，实现对每行数据进行加锁（锁的粒度更细，并发度更高）

## 四、面试常考问题

- 主要考察HashMap
  - HashMap底层数据结构是什么？（其他实现类也同样可能被问到）

  - HashMap是线程安全的吗？

  - 描述下HashMap的扩容机制

  - HashMap、TreeMap、HashTable区别？

  - HashMap为什么引入红黑树？与平衡树相比，红黑树的优势是什么？

  - HashMap一直都是红黑树结构吗？什么时候由链表转红黑树？什么时候由红黑树退化回链表？

    - 为什么长度为6退化为链表而不是长度为7？

  - jdk1.8中HashMap的优化是什么?

  - jdk.1.8中HashMap的容量为什么都设置为2的倍数？这样做有什么好处？

  - 并发场景下，HashMap会出现什么问题？（这里jdk1.7和jdk1.8存在的问题不同）


# 五、参考

- [1. Java 8系列之重新认识HashMap](https://zhuanlan.zhihu.com/p/21673805)

- [2.jdk1.8的HashMap和ConcurrentHashMap](https://my.oschina.net/pingpangkuangmo/blog/817973)

- [3.搞懂 Java ArrayList 源码](https://juejin.im/post/6844903582194466824#heading-12)

  