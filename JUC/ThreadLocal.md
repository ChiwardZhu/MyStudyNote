问题：

![image-20200904082121222](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200904082121222.png)





**强引用**

就是我们最常见的普通对象引用，只要还有强引用指向一个对象，就能表名对象还“活着”，垃圾回收器就不会回收这种对象；即使是OOM也不会回收



**软引用：**

内存够用时，垃圾回收不会回收软引用对象

内存不够时垃圾回收才起作用



**弱引用：**

垃圾回收器一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存



**虚引用：**



**内存溢出：**没有足够的内存提供申请者使用

**内存泄漏：**指程序中已动态分配的堆内存由于某种原因程序未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。内存泄漏的堆积终将导致内存溢出。



![image-20200904081609062](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200904081609062.png)

问题一：

为什么ThreadLocal要作为一个弱引用对象存放在Entry的key中？

![image-20200904084021505](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200904084021505.png)

问题二：

由于key==null，但是这个Entry对象依然存在于map集合中，导致value再也无法访问到，因此依然存在内存泄漏

当set或get时，会将map内key==null的entry对象全部清理掉，还需要手动remove吗？

需要

![image-20200904084512856](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200904084512856.png)





![image-20200904084703236](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200904084703236.png)

如果用的是线程池中的Thread对象，我们需要在归还Thread对象时，将ThreadLocalMap置为null，否则其他应用从线程池中获取Thread对象时，就有可能获取到之前的Thread对象，并使用原来的ThreadLocalMap







可以花时间看看ThreadLocal的源码

主要看4个方法：set()、get()、remove()、initialValue()与ThreadLocalMap内部类