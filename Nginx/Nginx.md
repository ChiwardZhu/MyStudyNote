# Nginx

## Nginx 的简介

> 什么是 nginx

Nginx 是高性能的HTTP和反向代理的服务器，处理高并发能力是十分强大的，能经受高负载的考验有报告表明能支持高达 50,000 个并发连接数。



> 正向代理

说反向代理之前，我们先看看正向代理，正向代理也是大家最常接触的到的代理模式，我们会从两个方面来说关于正向代理的处理模式，分别从软件方面和生活方面来解释一下什么叫正向代理。

　　在如今的网络环境下，我们如果由于技术需要要去访问国外的某些网站，此时你会发现位于国外的某网站我们通过浏览器是没有办法访问的，此时大家可能都会用一个操作FQ进行访问，FQ的方式主要是找到一个可以访问国外网站的代理服务器，我们将请求发送给代理服务器，代理服务器去访问国外的网站，然后将访问到的数据传递给我们！

　　上述这样的代理模式称为正向代理，==正向代理最大的特点是客户端非常明确要访问的服务器地址；==服务器只清楚请求来自哪个代理服务器，而不清楚来自哪个具体的客户端；正向代理模式==屏蔽或者隐藏了真实客户端信息==。来看个示意图（我把客户端和正向代理框在一块，同属于一个环境，后面我有介绍）：

![image-20200714194142202](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714194142202.png)

==客户端必须设置正向代理服务器==，当然前提是要知道正向代理服务器的IP地址，还有代理程序的端口。如图

![img](https://img2018.cnblogs.com/blog/1202586/201812/1202586-20181211121039404-1910765480.png)

总结来说：正向代理，"它代理的是客户端，代客户端发出请求"，是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。

　　正向代理的用途：
（1）访问原来无法访问的资源，如Google
（2）可以做缓存，加速访问资源
（3）对客户端访问授权，上网进行认证
（4）代理可以记录用户访问记录（上网行为管理），对外隐藏用户信息

> 反向代理

明白了什么是正向代理，我们继续看关于反向代理的处理方式，举例如我大天朝的某宝网站，每天同时连接到网站的访问人数已经爆表，单个服务器远远不能满足人民日益增长的购买欲望了，此时就出现了一个大家耳熟能详的名词：分布式部署；也就是通过部署多台服务器来解决访问人数限制的问题；某宝网站中大部分功能也是直接使用Nginx进行反向代理实现的，并且通过封装Nginx和其他的组件之后起了个高大上的名字：Tengine，有兴趣的童鞋可以访问Tengine的官网查看具体的信息：http://tengine.taobao.org/。那么反向代理具体是通过什么样的方式实现的分布式的集群操作呢，我们先看一个示意图（我把服务器和反向代理框在一块，同属于一个环境，后面我有介绍）：

![img](https://images2018.cnblogs.com/blog/1202586/201804/1202586-20180406175939873-925019958.png)

通过上述的图解大家就可以看清楚了，多个客户端给服务器发送的请求，Nginx服务器接收到之后，按照一定的规则分发给了后端的业务处理服务器进行处理了。此时~请求的来源也就是客户端是明确的，但是请求具体由哪台服务器处理的并不明确了，Nginx扮演的就是一个反向代理角色。

　　客户端是无感知代理的存在的，反向代理对外都是透明的，访问者并不知道自己访问的是一个代理。因为客户端不需要任何配置就可以访问。

　　反向代理，"它代理的是服务端，代服务端接收请求"，主要用于服务器集群分布式部署的情况下，反向代理隐藏了服务器的信息。

　　反向代理的作用：
（1）保证内网的安全，通常将反向代理作为公网访问地址，Web服务器是内网
（2）负载均衡，通过反向代理服务器来优化网站的负载

### 项目场景

　　通常情况下，我们在实际项目操作时，正向代理和反向代理很有可能会存在在一个应用场景中，正向代理代理客户端的请求去访问目标服务器，目标服务器是一个反向单利服务器，反向代理了多台真实的业务处理服务器。具体的拓扑图如下：

![img](https://images2018.cnblogs.com/blog/1202586/201804/1202586-20180406180130452-1246060303.png)

## 二者区别

　　截了一张图来说明正向代理和反向代理二者之间的区别，如图。

![img](https://img2018.cnblogs.com/blog/1202586/201812/1202586-20181211122806997-940664368.png)

　　图解：

在正向代理中，Proxy和Client同属于一个LAN（图中方框内），隐藏了客户端信息；

在反向代理中，Proxy和Server同属于一个LAN（图中方框内），隐藏了服务端信息；

实际上，Proxy在两种代理中做的事情都是替服务器代为收发请求和响应，不过从结构上看正好左右互换了一下，所以把后出现的那种代理方式称为反向代理了。

> 负载均衡

我们已经明确了所谓代理服务器的概念，那么接下来，Nginx扮演了反向代理服务器的角色，它是以依据什么样的规则进行请求分发的呢？不用的项目应用场景，分发的规则是否可以控制呢？

　　这里提到的客户端发送的、Nginx反向代理服务器接收到的请求数量，就是我们说的负载量。

　　请求数量按照一定的规则进行分发到不同的服务器处理的规则，就是一种均衡规则。

　　所以，将服务器接收到的请求按照规则分发的过程，称为负载均衡。

　　负载均衡在实际项目操作过程中，有硬件负载均衡和软件负载均衡两种，硬件负载均衡也称为硬负载，如F5负载均衡，相对造价昂贵成本较高，但是数据的稳定性安全性等等有非常好的保障，如中国移动中国联通这样的公司才会选择硬负载进行操作；更多的公司考虑到成本原因，会选择使用软件负载均衡，软件负载均衡是利用现有的技术结合主机硬件实现的一种消息队列分发机制。

![img](https://images2018.cnblogs.com/blog/1202586/201804/1202586-20180406180405961-333776342.png)

　　Nginx支持的负载均衡调度算法方式如下：

1. weight轮询（默认，常用，具有HA功效！）：接收到的请求按照权重分配到不同的后端服务器，即使在使用过程中，某一台后端服务器宕机，Nginx会自动将该服务器剔除出队列，请求受理情况不会受到任何影响。 这种方式下，可以给不同的后端服务器设置一个权重值(weight)，用于调整不同的服务器上请求的分配率；权重数据越大，被分配到请求的几率越大；该权重值，主要是针对实际工作环境中不同的后端服务器硬件配置进行调整的。
2. ip_hash（常用）：每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，这也在一定程度上解决了集群部署环境下session共享的问题。
3. fair：智能调整调度算法，动态的根据后端服务器的请求处理到响应的时间进行均衡分配，响应时间短处理效率高的服务器分配到请求的概率高，响应时间长处理效率低的服务器分配到的请求少；结合了前两者的优点的一种调度算法。但是需要注意的是Nginx默认不支持fair算法，如果要使用这种调度算法，请安装upstream_fair模块。
4. url_hash：按照访问的url的hash结果分配请求，每个请求的url会指向后端固定的某个服务器，可以在Nginx作为静态服务器的情况下提高缓存效率。同样要注意Nginx默认不支持这种调度算法，要使用的话需要安装Nginx的hash软件包。

#  几种常用web服务器对比

| **对比项\服务器** | **Apache** | **Nginx** | **Lighttpd** |
| ----------------- | ---------- | --------- | ------------ |
| Proxy代理         | 非常好     | 非常好    | 一般         |
| Rewriter          | 好         | 非常好    | 一般         |
| Fcgi              | 不好       | 好        | 非常好       |
| 热部署            | 不支持     | 支持      | 不支持       |
| 系统压力          | 很大       | 很小      | 比较小       |
| 稳定性            | 好         | 非常好    | 不好         |
| 安全性            | 好         | 一般      | 一般         |
| 静态文件处理      | 一般       | 非常好    | 好           |
| 反向代理          | 一般       | 非常好    | 一般         |



> 动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速
度。降低原来单个服务器的压力。

![image-20200715210734553](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715210734553.png)

## Nginx下载与安装

1. 下载组件以及Nginx：

   * gcc安装

     ```shell
     yum install gcc-c++
     ```

   * PCRE pcre-devel 安装==nginx需要【解析正则】==

     ```shell
     yum install -y pcre pcre-devel
     ```

   * zlib 安装==nginx需要【用来压缩】==

     ```shell
     yum install -y zlib zlib-devel
     ```

   * OpenSSL 安装==nginx需要【用加密】==

     ```shell
     yum install -y openssl openssl-devel
     ```

2. 下载安装配置Nginx

   * Nginx下载与安装：官网http://nginx.org/en/download.html

     ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714195303494.png)

   * 下载linux版并将其放到`/usr/local/src`目录下并解压

     ```shell
     tar -xvf nginx-1.18.0.tar.gz
     ```

   * 执行

     ```shell
     ./configure		#
     make && make install		# 编译安装
     ```

     安装完之后到`/usr/local`下我们会发现多了一个nignx目录

     ![image-20200714195933309](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714195933309.png)

3. 启动

   * 安装完后`/usr/local/nginx/sbin`目录下有个nginx目录，这是Nginx命令目录

   * 切换到sbin目录下并执行启动命令

     ```shell
     cd sbin
     ./nginx
     ps -ef | grep nginx		# 查看Nginx进程是否启动
     ```

     ![image-20200714201304428](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714201304428.png)

   * 进入到配置目录并查看`nginx.conf`文件，可知端口号为80，因此只需输入本机公网地址即可进入Nginx页面

     ```shell
     cd /usr/local/nginx/conf
     vim nginx.conf
     ```

     ![image-20200714201956399](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714201956399.png)

     ![image-20200714202129661](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714202129661.png)



## Nginx常用命令

**切换到命令目录：**

```shell
/usr/local/nginx/sbin
```

1. 查看版本号

   ```shell
   ./nginx -v
   ```

   ![image-20200714202710636](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714202710636.png)

2. 启动Nginx

   ```shell
   ./nginx
   ```

   ![image-20200714202914142](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714202914142.png)

3. 关闭Nginx

   ```shell
   ./nginx -s stop
   ```

   ![image-20200714202944750](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714202944750.png)

4. 重新加载Nginx：

   当我们修改了Nginx的配置文件`nginx.conf`之后，我们可以使用以下命令让配置文件生效而不需要重启Nginx

   ```shell
   ./nginx -s reload
   ```



## Nginx配置文件`nginx.conf`

### 配置文件中的三部分内容

 1. **全局块：**配置服务器整体运行的配置指令

    ```shell
    worker_processes 1; 	# 处理并发数的配置；值越大代表支持并发处理数就越多
    ```

2. **events 块 ：**影响 Nginx 服务器与用户的网络连接

   ```shell
   worker_connections 1024; 		# 支持的最大连接数为 1024
   ```

3. **http 块(http 全局块、server 块)：**

   * ==http 全局块：==http全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

   * ==server 块：==这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。

     ​		每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。 而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

   * 





## Nginx 配置实例 反向代理 实例 1

**实现效果：**打开浏览器，在浏览器地址栏输入地址 www.123.com ，跳转到 liunx 系统 tomcat 主页
面中

![image-20200714213231507](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714213231507.png)

1. 下载并安装tomcat，然后启动

   下载地址：https://archive.apache.org/dist/tomcat/找到自己需要的版本，下载后放到`/usr/local/src`目录下

   ```shell
   tar -xvf apache-tomcat-8.5.51.tar.gz	# 解压
   cd apache-tomcat-8.5.51/bin		# 切换到bin目录下
   ./startup.sh 	# 启动 tomcat默认端口 8080，因此需要先开放端口
   
   systemctl status firewalld		# 查看防火墙状态
   systemctl start firewalld		# 启动防火墙
   firewall-cmd --list-all		# 查看防火墙开放的端口号
   firewall-cmd --add-port=8080/tcp --permanent	# permanent代表永久开放
   firewall-cmd -reload	# 重启防火墙
   ```

   ![image-20200714205600983](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714205600983.png)

   分别开放8081、8082、9001

   ![image-20200714210713799](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714210713799.png)

2. 访问http://39.107.142.16:8080/

   ![image-20200714210814630](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714210814630.png)

3. 在windows系统中，打开hosts文件：将ip地址对应域名

   ![image-20200714211745020](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714211745020.png)

   ![image-20200714211919008](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714211919008.png)

4. 进入到`nginx.conf`配置文件中，修改配置信息

   ![image-20200714212628329](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714212628329.png)

5. 启动Nginx

   ```shell
   cd /usr/local/nginx/sbin
   ./nginx
   ```

6. 测试：访问www.123.com

   ![image-20200714213126109](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714213126109.png)



## Nginx 配置实例 反向代理 实例 2

**实现效果：**使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中nginx 监听端口为 9001

1. 创建2个目录并将tomcat的安装包复制进去然后解压

   ```shell
   mkdir tomcat8080
   mkdir tomcat8081
   cp apache-tomcat-8.5.51.tar.gz tomcat8080
   cp apache-tomcat-8.5.51.tar.gz tomcat8081
   tar -xvf apache-tomcat-8.5.51.tar.gz
   ```

2. 修改tomcat配置文件`server.xml`

   ```shell
   cd /usr/local/src/tomcat8081/apache-tomcat-8.5.51/conf
   vim server.xml
   
   ```

   ![image-20200714220101715](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714220101715.png)

   ![image-20200714220205164](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714220205164.png)

3. 关闭已有的tomcat进程

   ```shell
   ps -ef | grep tomcat	# 查看是否还有tomcat进程
   ```

4. 启动8080与8081并测试

   ![image-20200714225101969](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714225101969.png)

   ![image-20200714225108217](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714225108217.png)

5. 进入两个webapps目录下分别创建`tom80`与`tom81`目录，并且创建`a.html`文件并写下以下代码

   ![image-20200714225751779](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714225751779.png)

   ![image-20200714225628115](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714225628115.png)

   ![image-20200714225903439](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714225903439.png)

   ![image-20200714225846217](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714225846217.png)

   

6. 测试：

   ![image-20200714230002307](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714230002307.png)

   ![image-20200714230007913](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714230007913.png)

7. 配置Nginx

   ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714231044117.png)

8. 测试

   ![image-20200714231112543](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714231112543.png)

   ![image-20200714231119999](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714231119999.png)



## 正则表达式

location 指令说明
该指令用于匹配 URL。 语法如下：

![image-20200714231337158](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714231337158.png)

1、= ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。 

2、~：用于表示 uri 包含正则表达式，并且区分大小写。 

3、~*：用于表示 uri 包含正则表达式，并且不区分大小写。

4、^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配。 注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~* 标识。





## nginx 配置实例负载均衡

1. 分别在8080与8081的webapps目录下新建`tom`目录与`a.html`文件

2. 修改`nginx.conf`配置文件

   ![image-20200714233235662](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714233235662.png)

3. 测试：发现每次访问都不一样

   ![image-20200714233508606](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714233508606.png)

   ![image-20200714233524601](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200714233524601.png)

### nginx 分配服务器策略

1. 轮询（默认）
   每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。
2. weight
   weigh t 代表权重默认为 1, 权重越高被分配的客户端越多
3. ip_hash
   每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器
4. fair （第三方）
   按后端服务器的响应时间来分配请求，响应时间短的优先分配。





## Nginx 配置实例 动静分离

1 . 什么是动静分离

![image-20200715190450149](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715190450149.png)

通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给一个资源设定一个过期时间，也就是说无需去 服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，不建议使用 Expires 来缓存），我这里设置 3d ，表示在这 3 天之内访问这个 URL ，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码 304如果有修改，则直接从服务器重新下载，返回状态码 200 。

2. 准备工作

   * 先在根目录下创建data目录，并在该目录下创建以下目录，并在www中放进一个html文件，image中放进一张图片

     ![image-20200715191634798](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715191634798.png)

   * 修改p配置文件`nginx.conf`

     ![image-20200715185944148](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715185944148.png)

   * 重启nginx后进行测试

![image-20200715190324967](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715190324967.png)

![image-20200715190346111](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715190346111.png)



* 因为配置文件 autoindex on，因此访问会有目录

![image-20200715192135532](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715192135532.png)



## Nginx 配置高可用的集群

1. 什么是 nginx 高可用

   ![image-20200715202650858](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715202650858.png)

   1 ）需要两台 nginx 服务器
   2 ）需要 keepalived
   3 ）需要虚拟 ip

2. 配置高可用的准备工作

   1 ）需要两台服务器 192.168.17.129 和 192.168.17.131
   2 ）在两台服务器安装 nginx
   3 ）在两台服务器安装 keepalived

3. 在两台服务器安装 keepalived

   1 ）使用 yum 命令进行安装

   ```shell
   cd /usr/local/src
   yum install keepalived -y
   ```

   2 ）安装之后，在 etc 目录里生成目录 keepalived ，有文件 keepalived.conf

4. 完成高可用配置（主从配置）

   1 ）修改 /etc/keepalived/keepalivec.conf 配置文件

   ```c
   global_defs {
       notification_email {
           acassen@firewall.loc
           failover@firewall.loc
           sysadmin@firewall.loc
       }
       notification_email_from Alexandre.Cassen@firewall.loc
           smtp_server 1 92.168.17.129
           smtp_connect_timeout 30
           router_id LVS_DEVEL
   }
   vrrp_script chk_http_port {
       script "/usr/local/src/nginx_check.sh"
           interval 2 # #（检测脚本执行的间隔
           weight 2
   }
   vrrp_instance VI_1 {
       state BACKUP # 备份服务器上将 MASTER 改为 BA CKUP
           interface ens33 // 网卡
           virtual_router_id 51 # 主、备机的 virtual_router_id 必须相同
           priority 90 # 主、备机取不同的优先级，主机值较大，备份机值较小
           advert_int 1
           authentication {
           auth_type PASS
               auth_pass 1111
       }
       virtual_ipaddress {
           192.168.17.50 // VRRP H 虚拟地址
       }
   }
   ```

   2 ）在 /usr/local/src 添加检测脚本

   ```sh
   #!/bin/bash
   A=`ps C nginx no header |wc l`
   if [ $A eq 0 ];then
   	/usr/local/nginx/sbin/nginx
   	sleep 2
   	if [ `ps C nginx no header |wc l` eq 0 ];then
   		killall keepalived
   	fi
   fi
   ```

   3 ）把两台服务器上 nginx 和 keepalived 启动
   启动 nginx ：./nginx
   启动 keepalived systemctl start keepalived.service

5. 最终测试
   1 ）在浏览器地址栏输入 虚拟 ip 地址 192.168.17.50

   ![image-20200715204002608](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204002608.png)

   ![image-20200715204013578](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204013578.png)

   2 ）把主服务器 192.168.17.129 nginx 和 keepalived 停止，再输入 192.168.17.50

   ![image-20200715204033113](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204033113.png)

   ![image-20200715204038858](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204038858.png)



## Nginx的原理

1. mater 和 worker

   ![image-20200715204120261](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204120261.png)

   ![image-20200715204125121](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204125121.png)

2. worker 如何进行工作的

   ![image-20200715204148373](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715204148373.png)

3. 一个 master 和多个 woker 有好处

   1 ）可以使用 nginx s reload 热部署，利用 nginx 进行热部署操作
   2 ）每个 woker 是独立的进程，如果有其中的一个 woker 出现问题，其他 woker 独立的，
   继续进行争抢，实现请求过程，不会造成服务中断

4. 设置多少个 woker 合适

   worker 数和服务器的 cpu 数相等是最为适宜的

5. 连接数 worker_connection

   第一个：发送请求，占用了 woker 的几个连接数？
   答案： 2 或者 4 个
   第二个： nginx 有一 个 master ，有四个 woker ，每个 woker 支持最大的连接数 1024 ，支持的
   最大并发数是多少？

   * 普通的静态访问最大并发数是： worker_connections * worker_processes /2
   *  而如果是 HTTP 作 为反向代理来说，最大并发数量应该是 worker_connections *
     worker_processes/4 。

