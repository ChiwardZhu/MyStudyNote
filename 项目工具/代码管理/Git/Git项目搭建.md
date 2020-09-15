![image-20200321183130830](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200321183130830.png)



#### 创建本地仓库：

创建本地仓库的两种方法：一种是创建全新的仓库，另一种是克隆远程仓库。

1. 创建全新的仓库，需要用Git管理的项目的根目录执行：

   ```shell
   $ git init
   ```

2. 执行后可以看到，在项目目录多出了一个.git目录，关于版本等的所有信息都在这个目录里面

   

#### 克隆远程仓库：

1. 另一种方式是克隆远程目录，将远程服务器上的仓库完全镜像一份到本地

   ```shell
   $ git clone [url] 
   ```

   例如：$ git clone https://gitee.com/SnailClimb/JavaGuide.git

   



