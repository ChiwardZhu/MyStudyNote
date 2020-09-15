> dependencyManagement与dependencies

Maven 使用dependencyManagement 元素提供了一种管理依赖版本号的方式

通常会再一个组织或者项目的最顶层的父POM 中看到dependencyManagement 元素

使用pom.xml 中的dependencyManagement 元素能让所有在子项目中引用一个依赖而不用显式的列出版本号。Maven 会沿着父子层次向上找，直到找到一个拥有dependencyManagement 元素的项目，然后它就会使用这个dependencyManagement 元素中指定的版本号。

例如在父项目中：

![image-20200707201630272](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200707201630272.png)

然后在子项目里就可以添加mysql-connector时可以不指定版本号，例如：

![image-20200707201727940](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200707201727940.png)

这样做的好处就是：如果有多个子项目都引用同一个依赖，则可以避免在每个使用的子项目里都声明一个版本号，这样当想升级或者切换到另一个版本时，只需要在父项目里更新，而不需要一个一个子项目的修改；另外如果某个子项目需要另外的一个版本，只需要在当前子项目声明version即可。