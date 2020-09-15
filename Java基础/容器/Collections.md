方法：

* **添加n个元素：**

  static <T> boolean	addAll(Collection<? super T> c, T... elements)

* **乱序：**

  static void	shuffle(List<?> list)

* **排序(默认升序)：**

  static <T extends Comparable<? super T>>   void	sort(List<T> list)

  **注意：**

  * sort(List<T> list)使用前提：

    被排序的集合里面存储的元素，必须实现Comparable，重写接口中的方法compareTo定义排序的规则

  * Comparable排序规则：

    this-参数：升序

* **将集合中元素按照指定规则排序：**

  static <T> void	sort(List<T> list, Comparator<? super T> c)

  * Comparator和Comparable区别：

    1. Comparable：本身(this)和别人(参数)比较，自己需要实现Comparable接口，重写比较的规则compareTo方法

    2. Comparator：相当于找一个第三方的裁判，比较两个

       

