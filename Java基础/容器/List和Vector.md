## 容器

**数组的优势：**

是一种简单的==线性序列==，可以快速地访问数组元素，效率高。如果从效率和类型检查的角度讲，数组是最好的

**数组的劣势：**不灵活。容器需要事先定义好，不能随着需求的变化而扩容。

####  **哈希值：**

是一个十进制的整数，由系统随机给出（就是对象的地址值，是一个逻辑地址，是模拟出来得到的地址，不是数据实际存储的物理地址）；在Object类有一个方法，可以获取对象的哈希值 int hashCode() 返回对象的哈希码值



 **int hashCode()：**方法的源码：

```java
public native int hashCode();
```

native：代表该方法调用的是本地操作系统的方法

![image-20200313144523110](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200313144523110.png)



toString方法的源码：输出的是该类的全限定名+hashcode的16进制值

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```



String类的哈希值：**String类重写Object类的hashCode方法

![image-20200320225249976](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200320225249976.png)



### 泛型：帮助我们建立类型安全的集合

* **泛型的本质：**“数据类型的参数化”

  我们可以把“泛型”理解为数据类型的一个占位符（形式参数），即告诉编译器，在调用泛型时必须传入实际类型



* **List：**List是有序、可重复的容器。
  * 有序：List中每个元素都有索引标记。可以根据元素的索引标记（在List中的位置）访问元素，从而精确控制这些元素
  * 可重复：List允许加入重复的元素。更确切地讲，List通常允许满足e1.equals(e2)的元素重复加入容器
  * 常用的实现类：ArrayList、LinkedList、Vector



* ##### **ArrayList：**底层用==数组==实现的存储。

  * **特点：**查询效率高、增删效率低、线程不安全。长度不受限制
  * **查询快：**数组的地址是连续的，我们通过数组的首地址可以找到数组，再通过数组的索引可以快速查询某一元素
  * **增删慢：**数组的长度是固定的，我们想要增加/书喊出一个元素，必须创建一个新数组，把源数组的数据复制过来

![image-20200319203652921](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200319203652921.png)

* **LinkedList特点和底层实现：**
  
  * **特点：**查询效率低，增删效率高，线程不安全
  * **查询慢：**链表的地址不是连续的，每次查询元素都必须从头开始查询
  * **增删快：**链表结构，增加、删除一个元素，对链表的整体结构没有影响
  * **LinkedList源码分析：**手动实现ArrayList
  
  ![image-20200319204303080](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200319204303080.png)

* **Vector向量：**

  Vector底层是用数组实现的List，相关的方法都加了同步检查，因此“线程安全，效率低”



* 树结构

![image-20200319210206658](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200319210206658.png)