---
title: JavaSE - Note08 Set
date: 2022-06-29 12:49:19
categories: JavaSE
tags: Set
---

# 集合

`java.util.*`

集合实际上就是一种容器，可以容纳其他类型的数据

- 集合不能直接存储基本数据类型（需装箱），也不能直接存储 Java 对象，存储引用数据类型

## 分类

1. 单个方式

   超级父接口：`java.util.Collection`

   ![Collect 集合继承结构图](/images/image-java/note08-01-Collect集合继承结构图.png)

2. 键值对方式

   超级父接口：`java.util.Map`

   ![Map 集合继承结构图](/images/image-java/note08-02-Map集合继承结构图.png)

### 总结

#### 所有实现类

| 实现类     | 底层                                                         |
| ---------- | ------------------------------------------------------------ |
| ArrayList  | 数组                                                         |
| LinkedList | 双向链表                                                     |
| Vector     | 数组，线程安全，效率较低，使用较少                           |
| HashSet    | HashMap，放到 HashSet 集合中的元素等同于放到 HashMap 集合 key 部分 |
| TreeSet    | TreeMap，放到 TreeMap 集合中的元素等同于放到 TreeMap 集合 key 部分 |
| HashMap    | 哈希表                                                       |
| Hashtable  | 哈希表，线程安全，效率较低，使用较少                         |
| Properties | 哈希表，线程安全，key 和 value 只能存储 String               |
| TreeMap    | 二叉树，key 可自动按照大小排序                               |

#### 集合存储特点

List：有序可重复

- 有序：存入与取出顺序相同，每个元素都有下标
- 可重复：存储值可重复

Set（Map）：无序不可重复

- 无序：存入与取出顺序不一定相同，Set 集合中也没有下标
- 不可重复：存储值不可重复

SortedSet（SortedMap）：无序不可重复，但集合中的元素可排序

- 可排序：可以按照大小顺序排列

> Map 集合的 key，就是一个 Set 集合
>
> 往 Set 集合中放数据，实际上放到了 Map 集合的 key 部分

## Collection 集合

### 常用方法

`java.util.Collection`

创建 Collection 类型的集合（多态）

```java
Collection c = new ArrayList();
```

1. boolean add(Object e)

   添加元素到集合末尾（支持**自动装箱**）

   ```java
   c.add(1200);	// 自动装箱
   c.add(new Object());
   ```

2. int size()

   返回集合中的元素个数

   ```java
   System.out.println(c.size());	// 2
   ```

3. void clear()

   清空集合

   ```java
   c.clean;
   System.out.println(c.size());	// 0
   ```

4. boolean contains(Object o)

   判断当前集合中是否包含元素 o

   - 放在集合中的元素，需重写 equals() 方法，否则默认使用 Object 中的 equals()，仅比较内存地址

   ```java
   c.add("a");
   c.add("b");
   c.add("c");
   System.out.println(c.contains("a"));	// true
   System.out.println(c.contains("d"));	// false
   ```

5. boolean remove(Object o)
   删除集合中某个元素

   - 同样调用了 equals() 方法
   
   ```java
   c.remove("c");
   System.out.println(c.size());	// 2
   ```
   
6. boolean isEmpty()

   判断集合是否为空

   ```java
   System.out.println(c.isEmpty());	// false
   c.clear();
   System.out.println(c.isEmpty());	// true
   ```

7. *Object[] toArray()*

   将集合转换为数组

   ```java
   c.add("a");
   c.add("b");
   c.add("c");
   
   Object[] o = c.toArray();
   for (int i = 0; i < o.length; i++) {
       System.out.print(o[i] + " ");	// a b c
   }
   ```

### *集合遍历/迭代器

用于集合迭代

以下方法所有 Collection 通用，在 Map 中不能使用

```java
Collection c = new ArrayList();
c.add("a");
c.add("b");
c.add(100);
c.add(new Object());
```

对集合进行迭代

1. 获取集合对象的迭代器对象 Iterator

   ```java
   Iterator it = c.iterator();
   ```

2. 通过以上获取的迭代器对象开始迭代/遍历集合

   Interator 迭代器中的方法：

   1. boolean hasNext()

      如果仍有元素可以迭代，则返回 true

   2. Object next()

      返回迭代的下一个元素
      
      > 存入什么类型，取出什么类型，打印转换为 String

   ```java
   while (it.hasNext()) {
   	System.out.println(it.next());
   }
   /*
       a
       b
       100
       java.lang.Object@1489e44c
   */
   ```

   - 集合结构只要发生变化，迭代器必须**重新获取**，所以应使用迭代器删除方法

3. default void remove()

   从底层集合中删除此迭代器返回的最后一个元素

   ```java
   Iterator it = c.iterator();
   it.remove();
   ```

### List 接口特有方法

创建 List 类型的集合：

```java
List list = new ArrayList();
```

1. void add(int index, E element)

   在列表 index 处插入元素 element

   ```java
   list.add(1, "v");
   // a v b c d
   ```

   > 使用不多，数组指定位置插入效率较低

2. Object set(int index, Object element)

   修改 index 处元素为 element

   ```java
   list.set(1, "V");	// a V b c d
   ```

3. E get(int index)

   根据下标获取元素

   ```java
   System.out.println(list.get(1));	// v
   ```

   可以借此特有方式遍历集合：

   ```java
   for (int i = 0; i < list.size(); i++) {
       System.out.println(list.get(i));
   }	// a v b c d
   ```

4. int indexOf(Object o)

   获取指定对象第一次出现处的索引

   ```java
   System.out.println(list.indexOf("v"));	// 1
   ```

5. int lastIndexOf(Object o)

   获取指定对象最后一次出现处的索引

   ```java
   // a v b c d v
   System.out.println(list.lastIndexOf("v"));	// 5
   ```

6. E remove(int index)

   删除下标为 index 的元素

   ```java
   list.remove(5);	// a v b c d
   ```

### ArrayList

ArrayList 集合底层是 Object 类型的数组

构造方法：

```java
new ArrayList();	// 初始化容量为10
new ArrayList(20);	// 指定初始化容量为20
Collection c = new HashSet();
new ArrayList(c);	// 将 HashSet 转换为 ArrayList
```

> 构造一个初始容量为10的空列表，先创建一个长度为0的数组，当添加第一个元素时初始化容量

ArrayList 扩容是**原容量的1.5倍**（底层是数组，优化则尽可能少的扩容）

### LinkedList

是一个双向列表，没有初始容量

随机增删效率较高，检索效率较低

在空间存储上，内存地址不连续

### Vector

底层是一个数组

初始化容量为10，扩容之后是**原容量的2倍**

所有方法线程安全，效率较低，使用较少

## Collections

`java.util.Collections`

Collection 工具类

1. Collections.synchronizedList(list)

   将非线程安全的 ArrayList 转换为线程安全的：

   ```java
   Collections.synchronizedList(ArrayList);
   ```

2. Collections.sort(list)

   排序（同样需实现 Comparable 接口）

   ```
   Collections.sort(list);
   ```

   同样支持比较器

   Collections.sort(list, Comparator())

## 泛型

数据类型参数化

### JDK 5.0

新特性：泛型（Generic）

使用泛型 `List<E>` 之后，表示 List 集合中只允许存储 E 类型的数据：

```java
List<E> list = new ArrayList<E>();
```

迭代器也支持添加 `Iterator<E>`，返回的都是 E 类型的数据：

```java
Iterator<E> it = c.iterator();
```

这样在迭代器迭代时将返回 E 类型数据而不是默认的 Object

- 集合中元素数据更加统一，不需要大量的“向下转型”
- 泛型属于编译阶段特性

缺点：

- 集合中存储元素缺乏多样性

### JDK 8.0

引入了自动类型推断机制（钻石表达式），从而 new 对象时不用指定泛型

```java
List<E> list = new ArrayList<>();
```

### 自定义泛型

在类的名称后使用 `<名称随意>` 来标识泛型，类体中使用该标识符运用所选泛型

```java
// 一般使用 <E>（Element）或 <T>（Type）
public class GenericTest02<E> {
    // 此处的 void 也可使用泛型
    public void doSome(E e) {
        System.out.println(e);
    }

    public static void main(String[] args) {
        // 创建引用时选择泛型具体类型
        GenericTest02<String> gt = new GenericTest02<>();
        gt.doSome("E is String");	// E is String
    }
}
```

- 使用了泛型，创建对象时却未标识具体类型，则默认为 Object 类型

## foreach

JDK 5.0 新特性，新增 foreach（增强 for 循环）

`for (A B : C)`

A：元素类型（数组/集合/泛型中的类型）

B：变量名（集合中的元素）

C：数组/集合/泛型名

```java
List<Integer> list = new ArrayList<>();
for (Integer i : list) {
    System.out.println(i);	// 1 2 3
}
```

缺点：没有下标

## Set 集合

### HashSet

无序不可重复，集合是 HashMap 的 key 部分

若传入纯数字，则 key 部分会自动按大小排序

```java
// 4 3 2 1
Set<String> str = new HashSet<>();
for (String s : str) {
    System.out.println(s);	// 1 2 3 4
}
```

- 同 HashMap，初始化容量是16，加载因子是0.75，扩容后容量是原容量的两倍

### TreeSet

底层实际是一个 TreeMap（最底层是二叉树），集合是 TreeMap 的 key 部分

无序不可重复，但存储元素可自动按大小顺序排序（可排序集合）

```java
// D C B A
Set<String> str = new TreeSet<>();
for (String s : str) {
    System.out.println(s);	// A B C D
}
```

#### 自动排序

1. 默认无法对自定义类型排序，需实现 `java.lang.Comparable` 接口（equals 可以不必重写），并重写 `Comparable()` 方法。示例如下：

  ```java
  class Customer implements Comparable<Customer> {
      int age;
      public Customer(int age) {
          this.age = age;
      }
      
      @Override
      public int compareTo(Customer c) {
          /*
          if (this.age == c.age) {
          	return 0;
          }
          else if (this.age > c.age) {
          	return 1;
          }
          else
          	return -1;
          */
          
          // 可一行实现
          return this.age - c.age;
      }
  }
  ```

2. 使用 `java.util.Comparator` 比较器

   ```java
   class MyComparator implements Comparator<E> {
       @Override
       public int compare(E e1, E e2) {
           return e1.age - e2.age;
       }
   }
   ```

   比较器需要通过构造方法传递

   ```java
   TreeSet<E> es = new TreeSet<>(new MyComparator());
   ```

   比较器支持匿名内部类的方式（没有名字，直接 new 接口）

   ```java
   TreeSet<E> es = new TreeSet<>(new MyComparator() {
       @Override
       public int compare(E e1, E e2) {
           return e1.age - e2.age;
       }
   });
   ```

比较规则固定时，建议实现 Comparable 接口

比较规则有多个，并且需要频繁切换时，建议使用 Comparator 接口

## Map 集合

### 常用方法

`java.util.Map`

```java
Map<Integer, String> map = new HashMap<>();
```

1. V put(K key, V value)

   向 Map 集合中添加键值对

   ```java
   map.put(1, "vv");	// 自动装箱
   map.put(2, "ee");
   map.put(3, "xx");
   map.put(4, "yy");
   ```

2. V get(Object key)

   通过 key 获取 value

   ```java
   System.out.println(map.get(2));	// ee
   ```

3. void clear()

   清空 Map 集合

   ```java
   map.clear();
   System.out.println(map.size());	// 0
   ```

4. boolean containsKey(Object key)

   判断 Map 中是否包含某个 key

   ```Java
   System.out.println(map.containsKey(1));	// true
   ```

5. boolean containsValue(Object value)

   判断 Map 中是否包含某个 value

   ```java
   System.out.println(map.containsValue("e"));	// false
   ```

   > contains 底层调用 equalse() 方法

6. boolean isEmpty()

   判断 Map 集合中元素个数是否为0

   ```java
   System.out.println(map.isEmpty());	// true
   ```

7. Set<K> keySet()

   获取 Map 集合中所有的 key（所有的键是一个 set 集合）

   ```java
   Set<Integer> keys = map.keySet();
   ```

8. V remove(Object key)

   通过 key 删除键值对

   ```java
   map.remove(3);
   System.out.println(map.size());
   ```

9. int size()

   获取 Map 集合中键值对的个数

   ```java
   System.out.println(map.size());	// 4
   ```

10. Collection<V> values()

    获取 Map 集合中所有的 value，返回一个 Collection

    ```java
    Collection<String> values = map.values();
    for (String s : values) {
        System.out.println(s);	// vv ee yy
    }
    ```

11. Set<Map.Entry<K, V>> entrySet()

    将 Map 集合转换为 Set 集合（源码中元素存储为一个 **Node 类型**）

    ```java
    Set<Map.Entry<Integer, String>> set = map.entryMap();
    ```

    > `Map.Entry` 是一种元素类型（1=vv），这种类型是 Map 中的静态内部类
    >
    > 静态内部类示例如下：
    >
    > ```java
    > package io.github.wataaaame.javase.map;
    > 
    > import java.util.HashSet;
    > import java.util.Set;
    > 
    > public class StaticInnerClassTest01 {
    >     // 静态内部类
    >     private static class InnerClass {
    >         // 静态方法 m1
    >         public static void m1() {
    >             System.out.println("m1");
    >         }
    > 
    >         // 静态实例方法 m2
    >         public void m2() {
    >             System.out.println("m2");
    >         }
    >     }
    > 
    >     public static void main(String[] args) {
    >         StaticInnerClassTest01.InnerClass.m1();
    > 
    >         StaticInnerClassTest01.InnerClass sc = new StaticInnerClassTest01.InnerClass();
    >         sc.m2();
    > 
    >         // set 集合中示例泛型，类见上
    >         Set<StaticInnerClassTest01.InnerClass> set = new HashSet<>();
    > 
    >         // Map 集合中示例泛型，类见下
    >         Set<MyMap.MyEntry<Integer, String>> set1 = new HashSet<>();
    >     }
    > }
    > 
    > class MyMap {
    >     public static class MyEntry<K, V> {
    > 
    >     }
    > }
    > 
    > ```
    >

### 遍历

1. keySet() 方法获取所有的 Key，通过 Key 遍历键值

   ```java
   Set<Integer> keys = map.keySet();
   
   // 使用迭代器遍历 keys
   Iterator<Integer> it = keys.iterator();
   while (it.hasNext()) {
       System.out.println(map.get(it.next()));	// vv ee xx yy
   }
   
   // 使用 foreach 遍历 keys
   for (Integer key : keys) {
       System.out.println(map.get(key));	// vv ee xx yy
   }
   ```

2. 将 Map 集合转换为 Set 集合，直接遍历 Set 集合

   ```java
   Set<Map.Entry<Integer, String>> set = map.entrySet();
   
   // 使用迭代器遍历
   Iterator<Map.Entry<Integer, String>> it2 = set.iterator();
   while (it2.hasNext()) {
       // Map.Entry<Iteger, String> node = it2.next();
       // System.out.println(node.getKey() + "=" + node.getValue);
       System.out.println(it2.next());	// 1=vv 2=ee 3=xx 4=yy
   }
   
   // *使用 foreach 遍历
   // （效率高，适合大数据量）
   for (Map.Entry<Integer, String> node : set) {
       // System.out.println(node.getKey() + "=" + node.getValue);
       System.out.println(me);	// // 1=vv 2=ee 3=xx 4=yy
   }
   ```

### HashMap

底层是 哈希表/散列表 的数据结构

#### 哈希表/散列表

一维数组，数组中每个元素是一个单向链表

数组和单向链表的结合体，充分发挥二者的特点

源代码：

```java
public class HashMap {
	// HashMap 底层就是一个一维数组
    Node<K, V>[] table;
    
    // 静态内部类 HashMap.Node
    static vlass Node<K, V> {
        // 哈希值(key 的 hashCode() 方法的执行结果，hash 值通过哈希函数/算法转换为数组的下标)
        final int hash;	// 转换数组下标
        final K key;	// 比较同一数组链表
        V value;		// 存储值
        Node<K, V> next	// 下一链表地址
    }
}
```

- **同一个单向链表上**，因数组下标一致，**所有节点的 hash 相同**，但 **k 的 equals 方法一定不相同**

- HashMap 集合的默认初始化容量是16（官方推荐**2的倍数**，也是提高 HashMap 集合的存取效率，以达到散列均匀所必需的），默认加载因子是0.75（当底层数组容量达到75%时开始扩容），扩容后容量是原容量的两倍

- 对于哈希表数据结构来说

  如果 o1 和 o2 的 hash 值相同，一定放到同一个单向链表

  如果不同，由于哈希算法结束后转换的数组下标也可能相同（取模运算），发生“哈希碰撞”

#### 存取原理

1. map.put(k, v)

   1. 先将 k、v 封装到 Node 对象中

   2. 底层调用 k 的 hashCode() 方法（Object 自带），得出 hash 值

   3. 通过哈希函数/算法，将 hash 值转换为数组下标

      若下标处无元素，便把 Node 添加到这个位置上；若下标处有元素，则将 k 与该下标对应的所有节点 k 进行 equals

      若都返回 false，则添加至链表末尾；若返回 true，则将该节点覆盖

2. map.get(k)

   1. 先调用 k 的 hashCode() 方法，得出 hash 值

   2. 通过哈希算法转换成数组下标，通过数组下标快速定位

      若下标处无元素，放回 null；若有，则将 k 与单向链表每一个 k 进行 equals
      若全为 false，则返回 null；否则 get 返回结果为 true 的 value

#### Key 特点

无序、不可重复

无序：不能保证挂在哪一个单向链表

不可重复：equals 方法保证 HashMap 集合的 Key 不可重复

- HashMap 集合中的 key 元素，会先后调用 hashCode() 与 equals() 方法，这两个方法都需要重写

  由于 HashSet 本质就是 HashMap 集合 Key 部分，所以同样需要重写 hashCode() 与 equals() 方法

> 散列分布不均匀问题：假设所有 hashCode() 方法返回固定值，会导致哈希表变成纯单向列表；所有 hashCode() 方法返回不同值，会导致哈希表变成一维数组
>
> key 与集合都允许为 null

#### 重写 hashCode() 与 equalse()

放在 HashMap 集合 key 部分，以及放在 HashSet 集合中的元素，需要同时重写 hashCode() 方法与 equals() 方法

向 Map 集合中存取，都是先调用 key 的 hashCode 方法，再调用 equals 方法（数组下标上元素为 null 则 equals 都不执行）

如果一个类的 equals 方法重写，那么 hashCode 方法必须重写；且 equals 返回 true，hashCode 方法返回值必须相同

> IDEA 工具支持自动生成

#### JDK 8

在 JDK 8 后，若哈希表单向链表中元素超过8个，单向链表数据结构就会变成红黑树数据结构，当红黑树上的节点数量小于6时，会重新退回单向链表数据结构

### HashTable

底层为哈希表数据结构，线程安全，目前使用较少

- 初始化容量为11，默认加载因子是0.75，扩容是原容量两倍加一

> HashTable 中的键值不能为 null（NullPointerException）

### Properties

是一个 Map 集合，线程安全，继承 HashTable，key 和 value 都是 String 类型

#### 常用方法

```java
Properties pro = new Properties()
```

1. String setProperty(String K, String V)

   底层调用 HashTable 中的 put 方法进行数据的存储

   ```
   pro.setProperty("a", "www");
   ```

2. String getProperty(String key)

   通过 key 取出对应的值

   ```java
   System.out.println(pro.getProperty("a"));	// www
   ```

### TreeMap

TreeSet 集合与 TreeMap 集合采用的是**中序遍历**（即 Iterator 采用的遍历方式）

#### 自平衡二叉树

遵循左小右大原则存放

##### 遍历方式

1. 前序遍历：根左右
2. 中序遍历：左根右
3. 后序遍历：左右根

> 前中后说的是根的位置，如前序遍历根在最前面
>
> 遍历时若根有子树，则向下延伸，最后回溯
