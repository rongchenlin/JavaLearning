## 一、集合框架的概述

集合、数组都是对多个数据进行存储操作的结构，简称**Java容器**。

1. 数组在存储多个数据方面的特点：
   1. 一旦初始化以后，其长度就确定了。
   2. 数组一旦定义好，其元素的类型也就确定了。我们也就只能操作指定类型的数据了。

2. 数组在存储多个数据方面的缺点：
   1. 一旦初始化以后，其长度就不可修改。
   2. 数组中提供的方法非常有限，对于添加、删除、插入数据等操作，非常不便，同时效率不高。
   3. 获取数组中实际元素的个数的需求，数组没有现成的属性或方法可用。
   4. 数组存储数据的特点：有序、可重复。对于无序、不可重复的需求，不能满足。

## 二、集合框架

### 框架

 * Collection接口：单列集合，用来存储一个一个的对象
      * List接口：存储有序的、可重复的数据。  -->**“动态”数组**
           * ArrayList、LinkedList、Vector
      * Set接口：存储无序的、不可重复的数据   -->高中讲的“**集合**”
           *              HashSet、LinkedHashSet、TreeSet
      
 * Map接口：双列集合，存储一对(key - value)的数据   -->高中的“**函数**”
      *              HashMap、LinkedHashMap、TreeMap、Hashtable、Properties

### Collection接口常用方法

```Java
Collection coll = new ArrayList();
// 添加：add（）/ addAll()
coll.add("hello");
coll.add(123);
coll.add(false);
// 个数：size()
System.out.println(coll.size());
// 清空：clear()
coll.clear();
// 判空：isEmpty()
System.out.println(coll.isEmpty());
```

### Iterator遍历元素以及删除元素

```Java
/**
 * 集合元素的遍历操作，使用迭代器Iterator接口
 * 1.内部的方法：hasNext() 和  next()
 * 2.集合对象每次调用iterator()方法都得到一个全新的迭代器对象，
 * 默认游标都在集合的第一个元素之前。
 * 3.内部定义了remove(),可以在遍历的时候，删除集合中的元素。此方法不同于集合直接调用remove()
 */
Collection coll = new ArrayList();
coll.add("hello");
coll.add(123);
coll.add(false);
// 获取指针
Iterator iterator = coll.iterator();
while (iterator.hasNext()){
    System.out.println(iterator.next());
}

// 删除元素
Iterator iterator1 = coll.iterator();
while (iterator1.hasNext()){
    // 一定先获取当前元素，再使用remove()
    Object obj = iterator1.next();
    if("hello".equals(obj)){
        iterator1.remove();
    }
}
```

### foreach增强遍历

```Java
// foreach 增强遍历
for (Object obj : coll){
    System.out.println(obj);
}
```

## List接口框架

### 框架

- Collection接口：单列集合，用来存储一个一个的对象
   - List接口：存储有序的、可重复的数据。  -->“动态”数组,替换原有的数组
      - ArrayList：作为List接口的主要实现类；**线程不安全的，效率高；底层使用Object[] elementData存储**
      - LinkedList：**对于频繁的插入、删除操作，使用此类效率比ArrayList高；底层使用双向链表存储**
      - Vector：作为List接口的古老实现类；线程安全的，效率低；底层使用Object[] elementData存储

### ArrayList的源码分析：

<u>**jdk 7情况下**</u>

```
// 底层创建了长度是10的Object[]数组elementData
ArrayList list = new ArrayList();

// elementData[0] = new Integer(123);
list.add(123);

// 如果此次的添加导致底层elementData数组容量不够，则扩容。
list.add(11);

// 默认情况下，扩容为原来的容量的1.5倍，同时需要将原有数组中的数据复制到新的数组中。

// 结论：建议开发中使用带参的构造器：
 ArrayList list = new ArrayList(int capacity)
```

**<u>jdk 8中ArrayList的变化：</u>**

```
// 底层Object[] elementData初始化为{}.并没有创建长度为10的数组
ArrayList list = new ArrayList();

//第一次调用add()时，底层才创建了长度10的数组，并将数据123添加到elementData[0]
list.add(123);

// 后续的添加和扩容操作与jdk 7 无异。
```

小结：jdk7中的ArrayList的对象的创建类似于单例的饿汉式，而jdk8中的ArrayList的对象的创建类似于单例的懒汉式，延迟了数组的创建，节省内存。

### LinkedList的源码分析：

```
// 内部声明了Node类型的first和last属性，默认值为null
LinkedList list = new LinkedList(); 

// 将123封装到Node中，创建了Node对象。
list.add(123);

// Node定义为：体现了LinkedList的双向链表的说法
private static class Node<E> {
      E item;
      Node<E> next;
      Node<E> prev;
      Node(Node<E> prev, E element, Node<E> next) {
      this.item = element;
      this.next = next;
      this.prev = prev;
    }
}
```

面试题：ArrayList、LinkedList、Vector三者的异同？

 - 相同：三个类都是实现了List接口，存储数据的特点相同：存储有序的、可重复的数据

 - 不同：见上
   

### list常用方法

```Java
ArrayList list = new ArrayList();
list.add(123);
list.add(456);
list.add("hello");
list.add("okok");

// indexOf()
System.out.println(list.indexOf("hello"));

// set()
list.set(1,"new word");

// get()
System.out.println(list.get(2));

// remove()
System.out.println(list.remove(1));

// subList()
System.out.println(list.subList(0, 2));

// lastIndexOf()
System.out.println(list.lastIndexOf("hello"));
```

## Set接口的框架：

 * Set接口：存储无序的、不可重复的数据   -->高中讲的“集合”
    * HashSet：作为Set接口的主要实现类；**线程不安全的；可以存储null值**
       *                  LinkedHashSet：作为HashSet的子类；遍历其内部数据时，**可以按照添加的顺序遍历**。**对于频繁的遍历操作，LinkedHashSet效率高于HashSet.**

    * TreeSet：可以按照添加对象的指定属性**，进行排序。**

Set接口中没有额外定义新的方法，使用的都是Collection中声明过的方法。

要求：向Set(主要指：HashSet、LinkedHashSet)中添加的数据，其所在的类一定要重写hashCode()和equals()，保证无序，但是一般不需要自己重写，可以使用系统写好的。

要求：重写的hashCode()和equals()尽可能保持一致性：相等的对象必须具有相等的散列码。

## Set的底层

一、Set：**存储无序的、不可重复的数据**，   以HashSet为例说明：
    1. 无序性：**不等于随机性**。存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数据的哈希值决定的。

2. 不可重复性：保证添加的元素按照equals()判断时，不能返回true**。即：相同的元素只能添加一个。**

二、添加元素的过程：以HashSet为例：

```Java
HashSet中添加元素a,先调用元素a所在的hashCode()方法，计算a的哈希值，
此哈希值接着通过某种算法计算出在HashSet底层数组中的存放位置（即为：索引位置），进行一下的判断
数组此位置上是否已经有元素：
    如果此位置上没有其他元素，则元素a添加成功。       --->情况1
    如果此位置上有其他元素b(或以链表形式存在的多个元素），则比较元素a与元素b的hash值：
        如果hash值不相同，则元素a添加成功。          --->情况2
        如果hash值相同，进而需要调用元素a所在类的equals()方法：
               equals()返回true,元素a添加失败
               equals()返回false,则元素a添加成功。 --->情况2
                 
注意：对于添加成功的情况2和情况3而言：元素a 与已经存在指定索引位置上数据以链表的方式存储。
                 
jdk 7 :元素a放到数组中，指向原来的元素。
jdk 8 :原来的元素在数组中，指向元素a     
                 
总结：HashSet底层：数组+链表的结构。                 
```

### LinkedHashSet的使用
LinkedHashSet作为HashSet的子类，在添加数据的同时，每个数据还维护了两个引用，记录此数据前一个数据和后一个数据。

优点：对于频繁的遍历操作，LinkedHashSet效率高于HashSet

```Java
Set set = new LinkedHashSet();
set.add(123);
set.add(344);
set.add("hello");
set.add("Thanks");

// 按照添加顺序来遍历
Iterator iterator = set.iterator();
while (iterator.hasNext()){
    System.out.println(iterator.next());
}
```

### TreeSet的使用

1. 向TreeSet中添加的数据，**要求是相同类的对象。**
2. 两种排序方式：自然排序（实现Comparable接口） 和 定制排序（Comparator）
3. .自然排序中，比较两个对象是否相同的标准为：compareTo()返回0.不再是equals().
4. 定制排序中，比较两个对象是否相同的标准为：compare()返回0.不再是equals().

```Java
Set set = new TreeSet();
set.add(23);
set.add(-4);
set.add(123);
set.add(94);

// 升序排序进行遍历
Iterator iterator = set.iterator();
while (iterator.hasNext()){
    System.out.println(iterator.next());
}
```

## Map的实现类的结构：

 *  Map:双列数据，存储key-value对的数据   ---**类似于高中的函数：y = f(x)**
     *  HashMap:作为Map的主要实现类；**线程不安全的，效率高；存储null的key和value**
         *              LinkedHashMap:保证在遍历map元素时，**可以按照添加的顺序实现遍历。**（原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个和后一个元素。）对**于频繁的遍历操作，此类执行效率高于HashMap。**
     *         TreeMap:保证按照添加的key-value对进行排序，**实现排序遍历**。此时考虑key的自然排序或定制排序。（<u>底层使用红黑树</u>）
     *  Hashtable:作为古老的实现类；线程安全的，效率低；不能存储null的key和value
         *  Properties:常用来处理配置文件。key和value都是String类型
 *  HashMap的底层：
     *      数组+链表       （jdk7及之前）
     *                    数组+链表+红黑树 （jdk 8）

### Map结构的理解：

 * Map中的key:无序的、不可重复的，**使用Set存储所有的key**  ---> key所在的类要重写equals()和hashCode() （以HashMap为例）

 * Map中的value:无序的、可重复的，**使用Collection存储所有的value** --->value所在的类要重写equals()

 * **一个键值对：key-value构成了一个Entry对象。**

 * Map中的entry:无序的、不可重复的，使用Set存储所有的entry

### HashMap的底层实现原理？以jdk7为例说明：

```Java
*      HashMap map = new HashMap():
*      在实例化以后，底层创建了长度是16的一维数组Entry[] table。

*      map.put(key1,value1):
*      首先，调用key1所在类的hashCode()计算key1哈希值，此哈希值经过某种算法计算以后，得到在Entry数组中的存放位置。
*      如果此位置上的数据为空，此时的key1-value1添加成功。							    ----情况1
*      如果此位置上的数据不为空，(意味着此位置上存在一个或多个数据(以链表形式存在)),比较key1和已经存在的一个或多个数据
*      的哈希值：
*              如果key1的哈希值与已经存在的数据的哈希值都不相同，此时key1-value1添加成功。----情况2
*              如果key1的哈希值和已经存在的某一个数据(key2-value2)的哈希值相同，继续比较：调用key1所在类的equals(key2)方法，比较：
*                      如果equals()返回false:此时key1-value1添加成功。                 ----情况3
*                      如果equals()返回true:使用value1替换value2。
*
*       补充：关于情况2和情况3：此时key1-value1和原来的数据以链表的方式存储。
       在不断的添加过程中，会涉及到扩容问题，当超出临界值(且要存放的位置非空)时，扩容。默认的扩容方式：扩容为原来容量的2倍，并将原有的数据复制过来
```

 * jdk8 相较于jdk7在底层实现方面的不同：

```
 * 1. new HashMap():底层没有创建一个长度为16的数组
 
 * 2. jdk 8底层的数组是：Node[],而非Entry[]

 * 3. 首次调用put()方法时，底层创建长度为16的数组

 * 4. jdk7底层结构只有：数组+链表。jdk8中底层结构：数组+链表+红黑树。

 * 4.1 形成链表时，七上八下（jdk7:新的元素指向旧的元素。jdk8：旧的元素指向新的元素）
 
   4.2 当数组的某一个索引位置上的元素以链表形式存在的数据个数 > 8 且当前数组的长度 > 64时，此时此索引位置上的所数据改为使用红黑树存储。

 * DEFAULT_INITIAL_CAPACITY : HashMap的默认容量，16

 * DEFAULT_LOAD_FACTOR：HashMap的默认加载因子：0.75

 * threshold：扩容的临界值，=容量*填充因子：16 * 0.75 => 12

 * TREEIFY_THRESHOLD：Bucket中链表长度大于该默认值，转化为红黑树:8

 * MIN_TREEIFY_CAPACITY：桶中的Node被树化时最小的hash表容量:64
```

### Map常用操作

```Java
Map map = new HashMap();
map.put("one",123);
map.put("two",234);
map.put("three",33);

// 修改
map.put("two",222);

// putAll()
Map map1 = new HashMap();
map1.put("four",44);
map1.put("five",55);
map.putAll(map1);
System.out.println(map);

// get()查询
System.out.println(map.get("two"));

// size()
System.out.println(map.size());

// containsKey() 是否包含指定key
System.out.println(map.containsKey("one"));
// contains.Vale
System.out.println(map.containsValue(222));

// remove()移除指定key和value，返回Value
System.out.println(map.remove("three"));

// clear()
map.clear();
```

### 元视图操作

```Java
/**
 * 元视图操作的方法
 */

// 遍历Key：keySet
Set set = map.keySet();
Iterator iterator = set.iterator();
while (iterator.hasNext()){
    System.out.println(iterator.next());
}

//遍历Value：values
Collection values = map.values();
for (Object object : values){
    System.out.println(object);
}

// 遍历Key-Value
Set entrySet = map.entrySet();
Iterator iterator1 = entrySet.iterator();
while (iterator1.hasNext()){
    Object obj = iterator1.next();
    // obj => entry
    Map.Entry en = (Map.Entry)obj;
    System.out.println(en.getKey()+" ----> " + en.getValue());
}
```

