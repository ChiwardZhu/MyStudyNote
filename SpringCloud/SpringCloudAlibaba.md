# Spring CloudAlibaba

> 为什么要使用Spring CloudAlibaba？

1. 因为Spring Cloud Netflix项目进入==维护模式==,不更新了,不会开发新组件了

2. 具有更加丰富和一体化的功能

   ![image-20200712151523900](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712151523900.png)

3. 具体组件：

   ![image-20200712152213525](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712152213525.png)



> Spring CloudAlibaba的使用

## Nacos(Naming and Configuration Service)

> 概念

​		一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台

Nacos=Eureka+Config+Bus

> 作用

* 替代Eureka做服务注册中心
* 替代Config做服务配置中心

> 下载与安装并启动



* ==启动：==到bin目录下cmd，执行`startup.cmd`

  ![image-20200712153722406](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712153722406.png)

  ![image-20200712153736289](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712153736289.png)

* ==访问：==http://localhost:8848/nacos，默认账号密码都是nacos

  ![image-20200712153917274](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712153917274.png)

  ![image-20200712153954912](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712153954912.png)

* 



> 使用

### 创建提供者Payment模块

1. 创建`cloudalibaba-provider-payment9001`

2. 导入依赖

   * 在父工程导入

     ```xml
     <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-alibaba-dependencies</artifactId>
         <version>2.1.0.RELEASE</version>
         <type>pom</type>
         <scope>import</scope>
     </dependency>
     ```

   * 在此工程导入

     ```xml
     <dependencies>
         <!-- SpringCloud ailibaba nacos-->
         <dependency>
             <groupId>com.alibaba.cloud</groupId>
             <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
         <!--监控-->
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-actuator</artifactId>
         </dependency>
         <!--热部署-->
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-devtools</artifactId>
             <scope>runtime</scope>
             <optional>true</optional>
         </dependency>
         <dependency>
             <groupId>org.projectlombok</groupId>
             <artifactId>lombok</artifactId>
             <optional>true</optional>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-test</artifactId>
             <scope>test</scope>
         </dependency>
     </dependencies>
     ```

3. `application.yml`

   ```yml
   server:
     port: 9001
   
   spring:
     application:
       name: nacos-payment-provider
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848 # 配置nacos地址
   
   management:
     endpoints:
       web:
         exposure:
           include: '*'
   ```

4. 主启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class PaymentMain9001 {
       public static void main(String[]args){
           SpringApplication.run(PaymentMain9001. class,args);
       }
   }
   ```

5. controller

   ```java
   @RestController
   public class PaymentController {
       @Value("${server.port}")
       private String serverPort;
   
       @GetMapping(value = "/payment/nacos/{id}")
       public String getPayment(@PathVariable("id") Integer id){
           return "nacos register, serverPort："+serverPort+"\t id："+id;
       }
   }
   ```

6. 启动nacos8848与9901测试：访问http://localhost:9001/payment/nacos/1与http://localhost:8848/nacos

   ![image-20200712163248200](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712163248200.png)

   ![image-20200712163755914](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712163755914.png)

7. 创建`cloudalibaba-provider-payment9002`模块，内容基本一样



### Nacos服务注册中心功能

1. 创建`cloudalibaba-consumer-nacos-order83`模块

2. 导入依赖

   ```xml
   <dependencies>
       <!-- SpringCloud ailibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <dependency><!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
           <groupId>com.zhu.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   ```

3. `application.yml`

   ```yml
   server:
     port: 83
   
   spring:
     application:
       name: nacos-order-consumer
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848
   # 消费者将要去访问的微服务名称（注册成功进nacos的微服务提供者）
   service-url:
     nacos-user-service: http://nacos-payment-provider
   ```

4. 主启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class OrderNacosMain83 {
       public static void main(String[]args){
           SpringApplication.run(OrderNacosMain83. class,args);
       }
   }
   ```

5. controller

   ```java
   @RestController
   @Slf4j
   public class OrderNacosController {
       @Resource
       private RestTemplate restTemplate;
   
       @Value("${service-url.nacos-user-service}")
       private String serverURL;
   
       @GetMapping(value = "/consumer/payment/nacos/{id}")
       public String paymentInfo(@PathVariable("id") Integer id){
           return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
       }
   }
   ```

6. 启动9001、9002、83测试：访问http://localhost:83/consumer/payment/nacos/1

   ![image-20200712215741622](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712215741622.png)

   ![image-20200712215748894](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712215748894.png)



> Nacos与其他注册中心特性对比

![image-20200712221933467](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712221933467.png)

![image-20200712221652859](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712221652859.png)

![image-20200712222038815](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712222038815.png)



### Nacos服务配置中心功能

> Nacos作为配置中心--基础配置

1. 创建`cloudalibaba-config-nacos-client3377`配置客户端

2. 导入依赖

   ```xml
   <dependencies>
       <!-- nacos config-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
       </dependency>
       <!-- SpringCloud ailibaba nacos-->
       <dependency>
           <groupId>com.alibaba.cloud</groupId>
           <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
       </dependency>
       <dependency><!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
           <groupId>com.zhu.springcloud</groupId>
           <artifactId>cloud-api-commons</artifactId>
           <version>${project.version}</version>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <!--监控-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
       <!--热部署-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-devtools</artifactId>
           <scope>runtime</scope>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   ```

3. 配置文件`bootstrap.yml`和`application.yml`；

   springboot中配置文件的加载存在优先级(boostrap优先级高于application)，因此要保证先根据自己的配置文件再从配置中心拉取配置

   ```yml
   # bootstrap.yml
   server:
     port: 3377
   
   spring:
     application:
       name: nacos-config-client
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848 # Nacos服务注册中心地址
         config:
           server-addr: localhost:8848 # Nacos作为配置中心地址
           file-extension: yaml  # 指定yaml格式的配置
   
   # ${spring.application.name}-${spring.profile.active}${spring.cloud.nacos.config.file-extension}
   # nacos-config-client-dev.yaml
   
   
   # application.yml
   spring:
     profiles:
       active: dev # 表示开发环境
   ```

4. 主启动类

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class NacosConfigClientMain3377 {
       public static void main(String[]args){
           SpringApplication.run(NacosConfigClientMain3377. class,args);
       }
   }
   ```

5. controller

   ```java
   @RestController
   @RefreshScope
   public class ConfigClientController {
       @Value("${config.info}")
       private String configInfo;
   
       @GetMapping("/config/info")
       public String getConfigInfo(){
           return configInfo;
       }
   }
   ```

6. 在Nacos的UI界面添加配置信息

   * ==配置文件命名规则：==

     ![image-20200713011918108](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200713011918108.png)

   * **创建配置文件：**

     ![image-20200713011950188](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200713011950188.png)

     ![image-20200713012035754](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200713012035754.png)

7. 启动3377测试：访问http://localhost:3377/config/info

   ![image-20200712231739195](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200712231739195.png)

8. 修改配置文件后再次调用查看配置的接口，就会发现配置已经刷新

   ![image-20200713012343959](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200713012343959.png)

   ![image-20200713012509185](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200713012509185.png)

#### 出现问题：

![image-20200715212037661](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200715212037661.png)

> Nacos作为配置中心--分类配置

### 三大属性

Nacos配置中心有3个属性：==NameSpace==、==Group==、==Data ID==类似于包名、类名、方法名；下图是三者关系图

1. **NameSpace：**用于区分部署环境(dev、test、prod)
2. **Group：**用于区分不同的微服务
3. **Data ID：**相同的微服务的集群

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200419010843273.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzk5MzA2NQ==,size_16,color_FFFFFF,t_70)



### 配置不同Data ID

通过指定==spring.profile.active==读取相应的配置文件

![image-20200716015316373](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716015316373.png)



### 配置不同的Group

通过指定==group==读取相应组的配置文件

![image-20200716015512642](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716015512642.png)



### 配置不同的namespace

通过指定==namespace==的ID读取相应组的配置文件

![image-20200716015858173](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716015858173.png)

![image-20200716020004934](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716020004934.png)

![image-20200716020055114](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716020055114.png)





## Nacos集群与持久化配置

Nacos需要保证高可用，因此就需要集群：

Nacos自带嵌入式数据库derby,但是如果做集群的话,每个Nacos都自带一个derby，这样就不能够保证数据的一致性；因此Nacos采用了==集中式存储的方式来支持集群化部署，目前只支持MySQL的存储(数据库，版本要求：5.6.5+)。



> windows单机版：切换mysql数据库

1. 找到nacos默认自带了一个sql文件

   ![image-20200716021759960](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716021759960.png)

   ![image-20200716022048058](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716022048058.png)

   ![](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716022222250.png)

2. 修改conf/application.properties文件，增加支持mysql数据源配置（目前只支持mysql），添加mysql数据源的url、用户名和密码。

   ```
   spring.datasource.platform=mysql db.num=1 db.url.0=jdbc:mysql://11.162.196.16:3306/nacos_devtest?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true db.user=nacos_devtest db.password=youdontknow
   ```

   ![image-20200716023107954](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716023107954.png)

3. 重启Nacos再添加配置并发布

   ![image-20200716023602498](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716023602498.png)

4. 查看数据库发现刚刚添加的配置已经在数据库中

   ![image-20200716023635412](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716023635412.png)



> Linux版：Nacos集群(3台)+nginx

需求：3个Nacos集群+1个nginx+mysql(保证数据的一致性)

1. 下载安装与配置mysql教程https://blog.csdn.net/qq_37598011/article/details/93489404

2. 下载与安装nacos

   * 将nacos的安装包放在/opt目录下
   * 解压`tar -xvf nacos-server-1.1.4.tar.gz`
   * 将解压的目录复制到/mynacos目录下`cp nacos /mynacos/`

3. 在/mynacos/conf目录下找到`nacos-mysql.sql`文件并打开。连接mysql，并在mysql中创建`nacos_config`数据库然后执行`nacos-mysql.sql`文件中的内容

4. 在/mynacos/conf目录下找到`application.properties`并添加以下内容

   ![image-20200716170404021](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716170404021.png)

5. 在/mynacos/conf目录下找到`cluster.conf`，配置集群的端口号

   ![image-20200716170707843](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716170707843.png)

6. 在/mynacos/bin目录下找到`startup.sh`添加以下内容

   ![image-20200716170924177](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716170924177.png)

   ![image-20200716170952136](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716170952136.png)

7. 配置`nginx.conf`

   ![image-20200716171238078](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716171238078.png)

8. 启动nacos集群

   ./startup.sh -p 3333	./startup.sh -p 4444	./startup.sh -p 5555

9. 启动nginx

   ./nginx

10. 测试：访问192.168.159.121:1111；如果可以进入nacos的web界面,就证明安装成功了

11. 将微服务注册到Nacos集群:

    ![image-20200716171429703](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716171429703.png)

    ![image-20200716171440109](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716171440109.png)







## Sentinel

> sentinel是什么？



> sentinel有什么功能？





> sentinel下载运行并测试

1. 下载sentinel的jar包https://github.com/alibaba/Sentinel/releases

   ![image-20200716171733698](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716171733698.png)

2. 运行sentinel

   ![image-20200716171807019](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716171807019.png)

3. 测试是否启动成功：访问http://localhost:8080/

   ![image-20200716171902316](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716171902316.png)

   

### 整合sentinel

1. 启动Nacos

2. 新建一个项目`cloudalibaba-sentinel-service8401`

   * 导入依赖

     ```xml
     <dependencies>
         <!-- SpringCloud ailibaba nacos-->
         <dependency>
             <groupId>com.alibaba.cloud</groupId>
             <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
         </dependency>
         <!-- SpringCloud ailibaba sentinel-datasource-nacos 持久化需要用到-->
         <dependency>
             <groupId>com.alibaba.csp</groupId>
             <artifactId>sentinel-datasource-nacos</artifactId>
         </dependency>
         <!-- SpringCloud ailibaba sentinel-->
         <dependency>
             <groupId>com.alibaba.cloud</groupId>
             <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
         </dependency>
         <dependency><!-- 引用自己定义的api通用包，可以使用Payment支付Entity -->
             <groupId>com.zhu.springcloud</groupId>
             <artifactId>cloud-api-commons</artifactId>
             <version>${project.version}</version>
         </dependency>
         <dependency>
             <groupId>org.springframework.cloud</groupId>
             <artifactId>spring-cloud-starter-openfeign</artifactId>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
         <!--监控-->
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-actuator</artifactId>
         </dependency>
         <!--热部署-->
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-devtools</artifactId>
             <scope>runtime</scope>
             <optional>true</optional>
         </dependency>
         <dependency>
             <groupId>org.projectlombok</groupId>
             <artifactId>lombok</artifactId>
             <optional>true</optional>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-test</artifactId>
             <scope>test</scope>
         </dependency>
     </dependencies>
     ```

   * `application.yml`

     ```yml
     server:
       port: 8401
     
     spring:
       application:
         name: cloudalibaba-sentinel-service
       cloud:
         nacos:
           discovery:
             server-addr: localhost:8848
         sentinel:
           transport:
             # 配置sentinel dashboard地址
             dashboard: localhost:8080
             # 默认8719端口，被占用会自动从8719开始依次+1扫描，直到找到未被占用的端口
             port: 8719
     
     management:
       endpoints:
         web:
           exposure:
             include: '*'
     ```

   * 主启动类

     ```java
     @EnableDiscoveryClient
     @SpringBootApplication
     public class MainApp8401 {
         public static void main(String[]args){
             SpringApplication.run(MainApp8401. class,args);
         }
     }
     ```

   * controller

     ```java
     @RestController
     public class FlowLimitController {
         @GetMapping("/testA")
         public String testA(){
             return "-------testA";
         }
     
         @GetMapping("/testB")
         public String testB(){
             return "-------testB";
         }
     }
     ```

   * 启动8401与sentinel测试：访问http://localhost:8080/#/dashboard发现并8401服务的任何信息；原因：==sentinel是懒加载==,需要我们执行一次访问,才会有信息

     访问http://localhost:8401/testA再刷新http://localhost:8080/#/dashboard会发现

     ![image-20200716172819092](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716172819092.png)

     ![image-20200716172932463](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200716172932463.png)

   * 

3. 





### sentinel流控规则

> 基本介绍











### sentinel降级规则









### sentinel热点规则