## String基础

* String类又称作==不可变字符序列==
* String位于java.lang包中，java程序默认导入java.lang包下的所有类
* java字符串就是**Unicode字符序列**，例如字符串“Java”就是4个Unicode字符'J'、'a'、'v'、'a'组成的
* Java没有内置的字符串类型，而是在标准Java类库中停工了一个预定义的类String，每个用双引号括起来的字符串都是String类的一个实例。



**字符串比较：**

```java
public static void main(String[] args) {
        String str1=new String("aaa");
        String str2="aaa";
        System.out.println(str1==str2);
        System.out.println(str1.equals(str2));
    }
```

![image-20200312232626186](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200312232626186.png)

* ==是比较对象的地址
* equals是比较类型与值





![image-20200319200137596](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200319200137596.png)

* 字符串常量池：程序当中直接写上的双引号字符串就在池中

![image-20200319200729624](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200319200729624.png)

![image-20200319201258335](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200319201258335.png)