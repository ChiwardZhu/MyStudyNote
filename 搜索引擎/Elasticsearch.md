## ElasticSearch

> 了解目录：

![image-20200624003116318](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624003116318.png)

* bin	启动文件
* config	配置文件
  * log4j2	日志配置文件
  * jvm.options	java 虚拟机相关的配置
  * elasticsearch.yml	elasticsearch 的配置文件！   默认9200端口！ 跨域！
* lib	相关jar包
* logs	日志
* modules	功能模块
* plugins	插件



> 启动并访问

1. 运行elasticsearch.bat
2. 访问9200端口![image-20200624004145683](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624004145683.png)
3. ![image-20200624004208970](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624004208970.png)



> 安装可视化界面 es head插件（需要有nodejs环境）

1. 下载：https://github.com/mobz/elasticsearch-head

2. 安装并启动

   ```bash
   cnpm install
   npm run start
   ```

3. 连接测试发现，存在跨域问题：更改elasticsearch.yml配置文件

   ```yml
   # 在配置文件末尾添加以下配置信息
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   ```

4. 重启es服务器，再次连接

   ![image-20200624110932453](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624110932453.png)

   索引相当于一个数据库（可以建立索引(库)，文档(库中的数据)）

5. 可视化界面 es head 只当做数据展示工具，所有的查询都用Kibana 做



> 安装kibana

Kibana 是一款开源的数据分析和可视化平台，它是 Elastic Stack 成员之一，设计用于和 Elasticsearch 协作。您可以使用 Kibana 对 Elasticsearch 索引中的数据进行搜索、查看、交互操作。您可以很方便的利用图表、表格及地图对数据进行多元化的分析和呈现。

Kibana 可以使大数据通俗易懂。它很简单，基于浏览器的界面便于您快速创建和分享动态数据仪表板来追踪 Elasticsearch 的实时数据变化。

搭建 Kibana 非常简单。您可以分分钟完成 Kibana 的安装并开始探索 Elasticsearch 的索引数据 — 没有代码、不需要额外的基础设施。



1. 下载、解压并运行`kibana.bat`

2. 访问(http://localhost:5601/)

   ![image-20200624185815946](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624185815946.png)

3. 开发工具（Post、crul、head、谷歌浏览器插件测试）

   ![image-20200624193919725](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624193919725.png)

4. 汉化==》修改kibana.yml后重启kibana

   ![image-20200624194257827](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200624194257827.png)

   ![image-20200701191833461](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701191833461.png)



# ES核心概念

> ​	ES 是面向文档	关系型数据库 和 ES的对比		==一切都是JSON==

| 关系型数据库    | es                                    |
| --------------- | ------------------------------------- |
| 数据库 dataBase | 索引 indeice                          |
| 表 table        | 索引 type(index)     ==慢慢会被弃用== |
| 行 row          | 文档 document                         |
| 列 column       | 字段 field                            |
| 约束 schema     | 映射 mapping                          |

elasticsearch（集群）中科院包含多个索引（数据库），每个索引中科院包含多个类型（表），每个类型下又包含多个文档（行），每个文档中又包含多个字段（列）



**物理设计：**

elasticsearch 在后台把每个**索引划分成多个分片**，每个分片可以在集群中的不同服务器间迁移。

它就是一个集群，集群名称为elasticsearch。

![image-20200701222533389](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701222533389.png)

**逻辑设计：**

一个索引类型中，包含多个文档。当检索一篇文档时，可以通过这样的顺序来查找：<索引>/<类型>/<文档id>，通过这样的组合，就可以找到具体的文档。注意：id不一定是整数，实际上它是个字符串。

## 文档

就是我们的一条条数据

之前说elasticsearch是面向文档的，那么就意味着索引和搜索数据的最小单位是文档，elasticsearch 中，文档有几个重要属性 :

* 自我包含: 一篇文档同时包含字段和对应的值，也就是同时包含 key:value
* 可以是层次型的，一个文档中包含自文档，复杂的逻辑实体就是这么来的！ {就是一个json对象！fastjson进行自动转换！}
* 灵活的结构，文档不依赖预先定义的模式，我们知道关系型数据库中，要提前定义字段才能使用，在elasticsearch中，对于字段是非常灵活的，有时候，我们可以忽略该字段，或者动态的添加一个新的字段。

尽管我们可以随意的新增或者忽略某个字段，但是，每个字段的类型非常重要，比如一个年龄字段类型，可以是字符串也可以是整形。因为elasticsearch会保存字段和类型之间的映射及其他的设置。这种映射具体到每个映射的每种类型，这也是为什么在elasticsearch中，类型有时候也称为映射类型。

## 类型

类型是文档的逻辑容器，就像关系型数据库一样，表格是行的容器。 类型中对于字段的定义称为映射， 比如 name 映射为字符串类型。 我们说文档是无模式的，它们不需要拥有映射中所定义的所有字段， 比如新增一个字段，elasticsearch会自动的将新字段加入映射，但是这个字段的不确定它是什么类型，elasticsearch就开始猜，如果这个值是18，那么elasticsearch会认为它是整形。 但是elasticsearch也可能猜不对， 所以最安全的方式就是提前定义好所需要的映射，这点跟关系型数据库殊途同归了，先定义好字段，然后再使用。

## 索引

就是数据库！
索引是映射类型的容器，elasticsearch中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。 然后它们被存储到了各个分片上了。

物理设计 ：节点和分片 如何工作

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627135029502.png)

一个集群至少有一个节点，而一个节点就是一个elasricsearch进程，节点可以有多个索引默认的，如果你创建索引，那么索引将会有个5个分片 ( primary shard ,又称主分片 ) 构成的，每一个主分片会有一个副本 ( replica shard ,又称复制分片 )

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627135119670.png)

上图是一个有3个节点的集群，可以看到主分片和对应的复制分片都不会在同一个节点内，这样有利于某个节点挂掉了，数据也不至于丢失。 实际上，一个分片是一个Lucene索引，一个包含倒排索引的文件目录，倒排索引的结构使得elasticsearch在不扫描全部文档的情况下，就能告诉你哪些文档包含特定的关键字。

### 倒排索引

elasticsearch使用的是一种称为倒排索引的结构，采用Lucene倒排索作为底层。这种结构适用于快速的全文搜索， 一个索引由文档中所有不重复的列表构成，对于每一个词，都有一个包含它的文档列表。 例如，现在有两个文档， 每个文档包含如下内容：

```shell
Study every day, good good up to forever	# 文档1包含的内容
To forever, study every day, good good up   # 文档2包含的内容
```

为了创建倒排索引，我们首先要将每个文档拆分成独立的词(或称为词条或者tokens)，然后创建一个包含所有不重 复的词条的排序列表，然后列出每个词条出现在哪个文档 :

| term    | doc_1 | doc_2 |
| ------- | ----- | ----- |
| Study   | √     | ×     |
| To      | x     | ×     |
| every   | √     | √     |
| forever | √     | √     |
| day     | √     | √     |
| study   | ×     | √     |
| good    | √     | √     |
| every   | √     | √     |
| to      | √     | ×     |
| up      | √     | √     |

现在，我们试图搜索 to forever，只需要查看包含每个词条的文档 score

| term    | doc_1 | doc_2 |
| ------- | ----- | ----- |
| to      | √     | ×     |
| forever | √     | √     |
| total   | 2     | 1     |

两个文档都匹配，但是第一个文档比第二个匹配程度更高。如果没有别的条件，现在，这两个包含关键字的文档都将返回。

再来看一个示例，比如我们通过博客标签来搜索博客文章。那么倒排索引列表就是这样的一个结构 :

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627135504888.png)

如果要搜索含有 python 标签的文章，那相对于查找所有原始数据而言，查找倒排索引后的数据将会快的多。只需要查看标签这一栏，然后获取相关的文章ID即可。完全过滤掉无关的所有数据，提高效率！



# IK分词器插件

## 什么是IK分词器？

分词:即把一段中文或者别的划分成一个个的关键字,我们在搜索时候会把自己的信息进行分词,会把数据库中或者索引库中的数据进行分词,然后进行一个匹配操作,
 默认的中文分词是将每个字看成一个词，比如"中国的花"会被分为"中","国","的","花",这显然是不符合要求的，所以我们需要安装中文分词器ik来解决这个问题。

**IK提供了两个分词算法
ik_smart 和 ik_max_word
其中 ik_smart 为最少切分，ik_max_word为最细粒度划分**



## 下载与安装

1. 下载 github地址:https://github.com/medcl/elasticsearch-analysis-ik/releases

   由于github下载较慢, 这里提供 蓝奏云地址:https://manaphy.lanzous.com/igd0qe2ssgd

2. 下载完毕之后，放入到我们的elasticsearch 插件即可

   ![image-20200701234854238](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701234854238.png)

3. 重启es

   ![image-20200701235330292](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701235330292.png)

4. 通过`elasticsearch-plugin list`这个命令来查看加载进来的插件

   ![image-20200701235433965](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200701235433965.png)

5. 使用kibana测试

   **ik_smart**为最少切分

   ![image-20200702000732538](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702000732538.png)

   **ik_max_word**为最细粒度划分！穷尽词库的可能字典！

   ![image-20200702000759571](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702000759571.png)

   

   ## 添加自己需要的词到分词器的字典中

   问题如下: 杰尼龟被拆分了

   ![image-20200702001651969](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702001651969.png)

在分词器`config`文件夹下创建`my.dic`文件，文件里添加我们需要的词，并且在`IKAnalyzer.cfg.xml`中配置

![image-20200702001953133](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702001953133.png)

![image-20200702002006136](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702002006136.png)

### 重启es

已经加载了新的字典my.dic

![image-20200702002130327](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702002130327.png)

再次测试杰尼龟

![image-20200702002407012](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702002407012.png)





# Rest风格

一种软件架构风格，而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

**基本Rest命令说明：**

| method | URL地址                                         | 描述                   |
| ------ | ----------------------------------------------- | ---------------------- |
| PUT    | localhost:9200/索引名称/类型名称/文档id         | 创建文档（指定文档id） |
| POST   | localhost:9200/索引名称/类型名称                | 创建文档（随机文档id） |
| POST   | localhost:9200/索引名称/类型名称/文档id/_update | 修改文档               |
| DELETE | localhost:9200/索引名称/类型名称/文档id         | 删除文档               |
| GET    | localhost:9200/索引名称/类型名称/文档id         | 查询文档通过文档id     |
| POST   | localhost:9200/索引名称/类型名称/_search        | 查询所有数据           |

### 关于索引的基本操作

#### 创建一个索引

```txt
PUT	/索引名/类型名/文档id
{请求体}
```

![image-20200702003145672](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702003145672.png)

在 es head 中查看

![image-20200702003304711](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702003304711.png)



## 字段的类型

* 字符串类型

  text 、 keyword

* 数值类型

  long, integer, short, byte, double, ﬂoat, half_ﬂoat, scaled_ﬂoat

* 日期类型

  date

* te布尔值类型

  boolean

* 二进制类型

  binary

### 指定字段的类型

创建规则完后 可以通过 GET 请求获取具体的信息

![image-20200702004055650](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702004055650.png)

![image-20200702004115016](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200702004115016.png)



### 查看默认的信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020062715375381.png)

如果自己的文档字段没有指定，那么es 就会给我们默认配置字段类型！

**扩展**： 通过GET _cat/ 可以获得es的当前的很多信息！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627154139445.png)

#### 修改索引内容

以前的方法: 还是使用`PUT`命令

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627154346331.png)

现在推荐使用以下方法 `POST`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627154725289.png)



#### 删除索引

通过DELETE 命令实现删除、 根据你的请求来判断是删除索引还是删除文档记录！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200627154834496.png)



### 关于文档的基本操作

#### 添加数据

```js
put /zhu/user/1
{
  "name":"杰尼龟",
  "age":11,
  "desc":"那里挺大的",
  "tags":["大大大","帅帅帅"]
}
```

![image-20200703140031031](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703140031031.png)



#### 获取数据

![image-20200703140111812](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703140111812.png)



#### 修改数据

![image-20200703140351025](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703140351025.png)



#### 简单搜索

![image-20200703142300987](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703142300987.png)



## 复杂搜索(排序、分页、高亮、模糊查询、精准查询)

![image-20200703144744698](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703144744698.png)



### 结果过滤

![image-20200703145602254](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703145602254.png)



### 排序

![image-20200703150206612](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703150206612.png)



### 分页

![image-20200703154629528](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703154629528.png)



### 布尔值查询

**must (and)**，类似 where ... and ...

![image-20200703202343228](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703202343228.png)



**should (or)**,  类似 where ... or ...

![image-20200703202634949](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703202634949.png)



**must_not**

![image-20200703202821697](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703202821697.png)



##### 过滤器 ﬁlter

![image-20200703203923935](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703203923935.png)

* **gt**（great than）：大于
* **gte**（great than equals）：大于等于
* **lt**（less than）：小于
* **lte**（less than equals）：小于等于



### **当属性是数组时，可以匹配多个条件查询**

![image-20200703203539235](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703203539235.png)

多个条件使用空格隔开 只要满足其中一个结果就可以被查出



### 精确查询

* **term：**直接通过倒排索引指定的词条进行精确查找
* **match：**会使用分词器分析（先分析文档，然后再通过分析的文档进行查询）

![image-20200703212359969](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703212359969.png)



#### **text 与 keyword 的区别不同**

![image-20200703214249366](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703214249366.png)

![image-20200703214122236](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703214122236.png)



#### 精确查询多个值

![image-20200703215805018](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703215805018.png)



### 高亮查询

![image-20200703220839777](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703220839777.png)

![image-20200703221114678](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703221114678.png)





# Spring boot 集成 ElasticSearch

### 找官方文档

![image-20200703223204382](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703223204382.png)

![image-20200703223436030](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703223436030.png)

![image-20200703223512405](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200703223512405.png)

1. 找到原生maven依赖

   ```xml
   <dependency>
       <groupId>org.elasticsearch.client</groupId>
       <artifactId>elasticsearch-rest-high-level-client</artifactId>
       <version>7.8.0</version>
   </dependency>
   ```

2. 初始化

   ```java
   RestHighLevelClient client = new RestHighLevelClient(
           RestClient.builder(
                   new HttpHost("localhost", 9200, "http"),
                   new HttpHost("localhost", 9201, "http")));
   client.close();
   ```

3. 分析这个类中的方法即可

   > 配置基本的项目

   ![image-20200704145635752](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200704145635752.png)

   源码中提供了对象

   ![image-20200704151530013](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200704151530013.png)

   虽然这里导入3个类，但都是静态内部类，核心类就一个`ElasticsearchRestClientConfigurations`

   ```java
   /*
    * Copyright 2012-2020 the original author or authors.
    *
    * Licensed under the Apache License, Version 2.0 (the "License");
    * you may not use this file except in compliance with the License.
    * You may obtain a copy of the License at
    *
    *      https://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */
   
   package org.springframework.boot.autoconfigure.elasticsearch;
   
   import java.net.URI;
   import java.net.URISyntaxException;
   import java.time.Duration;
   
   import org.apache.http.HttpHost;
   import org.apache.http.auth.AuthScope;
   import org.apache.http.auth.Credentials;
   import org.apache.http.auth.UsernamePasswordCredentials;
   import org.apache.http.client.config.RequestConfig;
   import org.apache.http.impl.client.BasicCredentialsProvider;
   import org.apache.http.impl.nio.client.HttpAsyncClientBuilder;
   import org.elasticsearch.client.RestClient;
   import org.elasticsearch.client.RestClientBuilder;
   import org.elasticsearch.client.RestHighLevelClient;
   
   import org.springframework.beans.factory.ObjectProvider;
   import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
   import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
   import org.springframework.boot.context.properties.PropertyMapper;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.util.StringUtils;
   
   /**
    * Elasticsearch rest client infrastructure configurations.
    *
    * @author Brian Clozel
    * @author Stephane Nicoll
    * @author Vedran Pavic
    * @author Evgeniy Cheban
    */
   class ElasticsearchRestClientConfigurations {
   
   	@Configuration(proxyBeanMethods = false)
   	@ConditionalOnMissingBean(RestClientBuilder.class)
   	static class RestClientBuilderConfiguration {
   
   		@Bean
   		RestClientBuilderCustomizer defaultRestClientBuilderCustomizer(ElasticsearchRestClientProperties properties) {
   			return new DefaultRestClientBuilderCustomizer(properties);
   		}
   
   		@Bean
   		RestClientBuilder elasticsearchRestClientBuilder(ElasticsearchRestClientProperties properties,
   				ObjectProvider<RestClientBuilderCustomizer> builderCustomizers) {
   			HttpHost[] hosts = properties.getUris().stream().map(this::createHttpHost).toArray(HttpHost[]::new);
   			RestClientBuilder builder = RestClient.builder(hosts);
   			builder.setHttpClientConfigCallback((httpClientBuilder) -> {
   				builderCustomizers.orderedStream().forEach((customizer) -> customizer.customize(httpClientBuilder));
   				return httpClientBuilder;
   			});
   			builder.setRequestConfigCallback((requestConfigBuilder) -> {
   				builderCustomizers.orderedStream().forEach((customizer) -> customizer.customize(requestConfigBuilder));
   				return requestConfigBuilder;
   			});
   			builderCustomizers.orderedStream().forEach((customizer) -> customizer.customize(builder));
   			return builder;
   		}
   
   		private HttpHost createHttpHost(String uri) {
   			try {
   				return createHttpHost(URI.create(uri));
   			}
   			catch (IllegalArgumentException ex) {
   				return HttpHost.create(uri);
   			}
   		}
   
   		private HttpHost createHttpHost(URI uri) {
   			if (!StringUtils.hasLength(uri.getUserInfo())) {
   				return HttpHost.create(uri.toString());
   			}
   			try {
   				return HttpHost.create(new URI(uri.getScheme(), null, uri.getHost(), uri.getPort(), uri.getPath(),
   						uri.getQuery(), uri.getFragment()).toString());
   			}
   			catch (URISyntaxException ex) {
   				throw new IllegalStateException(ex);
   			}
   		}
   
   	}
   
   	@Configuration(proxyBeanMethods = false)
   	@ConditionalOnClass(RestHighLevelClient.class)
   	static class RestHighLevelClientConfiguration {
   
   		@Bean
   		@ConditionalOnMissingBean
   		RestHighLevelClient elasticsearchRestHighLevelClient(RestClientBuilder restClientBuilder) {
   			return new RestHighLevelClient(restClientBuilder);
   		}
   
   		@Bean
   		@ConditionalOnMissingBean
   		RestClient elasticsearchRestClient(RestClientBuilder builder,
   				ObjectProvider<RestHighLevelClient> restHighLevelClient) {
   			RestHighLevelClient client = restHighLevelClient.getIfUnique();
   			if (client != null) {
   				return client.getLowLevelClient();
   			}
   			return builder.build();
   		}
   
   	}
   
   	@Configuration(proxyBeanMethods = false)
   	static class RestClientFallbackConfiguration {
   
   		@Bean
   		@ConditionalOnMissingBean
   		RestClient elasticsearchRestClient(RestClientBuilder builder) {
   			return builder.build();
   		}
   
   	}
   
   	static class DefaultRestClientBuilderCustomizer implements RestClientBuilderCustomizer {
   
   		private static final PropertyMapper map = PropertyMapper.get();
   
   		private final ElasticsearchRestClientProperties properties;
   
   		DefaultRestClientBuilderCustomizer(ElasticsearchRestClientProperties properties) {
   			this.properties = properties;
   		}
   
   		@Override
   		public void customize(RestClientBuilder builder) {
   		}
   
   		@Override
   		public void customize(HttpAsyncClientBuilder builder) {
   			builder.setDefaultCredentialsProvider(new PropertiesCredentialsProvider(this.properties));
   		}
   
   		@Override
   		public void customize(RequestConfig.Builder builder) {
   			map.from(this.properties::getConnectionTimeout).whenNonNull().asInt(Duration::toMillis)
   					.to(builder::setConnectTimeout);
   			map.from(this.properties::getReadTimeout).whenNonNull().asInt(Duration::toMillis)
   					.to(builder::setSocketTimeout);
   		}
   
   	}
   
   	private static class PropertiesCredentialsProvider extends BasicCredentialsProvider {
   
   		PropertiesCredentialsProvider(ElasticsearchRestClientProperties properties) {
   			if (StringUtils.hasText(properties.getUsername())) {
   				Credentials credentials = new UsernamePasswordCredentials(properties.getUsername(),
   						properties.getPassword());
   				setCredentials(AuthScope.ANY, credentials);
   			}
   			properties.getUris().stream().map(this::toUri).filter(this::hasUserInfo)
   					.forEach(this::addUserInfoCredentials);
   		}
   
   		private URI toUri(String uri) {
   			try {
   				return URI.create(uri);
   			}
   			catch (IllegalArgumentException ex) {
   				return null;
   			}
   		}
   
   		private boolean hasUserInfo(URI uri) {
   			return uri != null && StringUtils.hasLength(uri.getUserInfo());
   		}
   
   		private void addUserInfoCredentials(URI uri) {
   			AuthScope authScope = new AuthScope(uri.getHost(), uri.getPort());
   			Credentials credentials = createUserInfoCredentials(uri.getUserInfo());
   			setCredentials(authScope, credentials);
   		}
   
   		private Credentials createUserInfoCredentials(String userInfo) {
   			int delimiter = userInfo.indexOf(":");
   			if (delimiter == -1) {
   				return new UsernamePasswordCredentials(userInfo, null);
   			}
   			String username = userInfo.substring(0, delimiter);
   			String password = userInfo.substring(delimiter + 1);
   			return new UsernamePasswordCredentials(username, password);
   		}
   
   	}
   
   }
   ```

   > 具体的API测试

   1. 创建索引

      ![image-20200704154816401](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200704154816401.png)

   2. 判断索引是否存在

      ![image-20200704154822766](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200704154822766.png)

   3. 删除索引

      ![image-20200704154832204](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200704154832204.png)

   4. 创建文档

      

   5. crud文档
