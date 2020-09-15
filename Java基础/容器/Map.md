### **Map**

#### Map集合的特点：

1. Map集合用来存储==键值对==。
2. Map集合中，元素的key和value的数据类型可以相同也可以不同
3. Map集合中，元素的key是不允许重复的，value是可以重复的
4. Map集合中，元素的key和value是一一对应的



#### HashMap集合的特点：

1. HashMap集合底层是哈希表：查询的速度快
   * JADK1.8之前：数组+单向链表
   * JDK1.8之后：数组+单向链表/红黑树（链表的长度超过8）：提高查询的速度
2. hashMap集合是一个无序的集合，存储元素和取出元素的顺序有可能不一致



#### LinkedHashMap的特点：

1. LinkedHashMap集合底层是哈希表+链表（保证迭代的顺序）
2. LinkedHashMap集合是一个有序的集合，存储元素和取出元素的顺序是一致的



#### hashmap的方法：

* **将键值对添加到map集合中，返回值：v（当key不重复时返回null，否则返回被替换的value值）**

  public	V	put(K key, V value)

* **把指定的键所对应的键值对从map集合中删除，返回被删除元素的值（key存在则返回被删除的值，不存在则返回null）**

  public	V	remove(Object key)

* **获取指定的键的值，key存在则返回该键的值，不存在则返回null**

  public	V	get(Object key)

* **判断集合中是否包含指定的键**

  public	boolean	containsKey(Object key)

* **把map集合中所有的key取出来存储到set集合中**

  public	Set<K>	keySet()

  ![image-20200321135231832](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200321135231832.png)

* **将map集合中的键值对存放到Entry对象中，并返回**

  public	Set<Map.Entry<K,V>>	entrySet()

  ![image-20200321140738348](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200321140738348.png)



* **LinkedHashMap特点：**哈希表+链表（记录元素的顺序）

* Hashtable继承Map接口：

  Hashtable底层是一个哈希表，是一个线程安全的集合，是单线程集合，速度慢

  HashMap底层是一个哈希表，是一个线程不安全的集合，是多线程集合，速度快

  HashMap集合：可以存储null键、null值

  Hashtable集合：不可以存储null键、null值

  Hashtable和Vector集合一样，在jdk1.2版本之后被更先进的集合（HashMap、ArrayList）所取代

  Hashtable的子类Properties依然活跃在历史舞台，Properties集合是一个唯一和IO流相结合的集合

### HashMap底层源码分析：

![image-20200314102849697](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200314102849697.png)

![image-20200314103103036](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200314103103036.png)