# Docker安装

## Docker的基本组成

![img](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1593263848382&di=ced5271fd3c9e3dd3b7e8003ce81acaf&imgtype=0&src=http%3A%2F%2Fseo-1255598498.file.myqcloud.com%2Ffull%2F66ec0965a96e9a6d2d1c191ebcc8807ba71baa93.jpg)

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。



## 安装Docker

**帮助文档：**

```shell
# 第一步：卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 第二步：下载需要的安装包
yum install -y yum-utils

# 第三步：设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo		# 国外的
    
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo	#阿里云
    
# 第四步：更新软件包的索引
yum makecache fast

# 第五步：安装docker   docker-ce 社区版	  docker-ee 企业版
yum install docker-ce docker-ce-cli containerd.io

# 第六步：启动docker
systemctl start docker

# 第七步：查看版本
docker version
```

![image-20200627185949451](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200627185949451.png)

```shell
# 测试：下载hello-world镜像
docker run hello-world

# 查看image镜像
docker images
```

![image-20200627190020955](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200627190020955.png)

![image-20200627190139030](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200627190139030.png)



**了解卸载：**

```shell
# 卸载docker
yum remove docker-ce docker-ce-cli containerd.io

# 删除目录
rm -rf /var/lib/docker		# docker的默认工作路径
```



## 阿里云镜像加速

1. 找到容器镜像服务

   ![image-20200627191809780](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200627191809780.png)

2. 找到centOS

   ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200627191912611.png)

3. 配置使用

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://zw38qthy.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

4. 

