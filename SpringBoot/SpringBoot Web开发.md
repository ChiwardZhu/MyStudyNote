## SpringBoot Web开发

jar：webapp

自动装配

springboot到底配置了什么？能不能进行修改？能修改哪些东西？能不能扩展？

* XXXAutoConfiguration..向容器总自动配置组件
* xxxProperties：自动配置类，装配配置文件中自定义的一些内容



**要解决的问题：**

* 导入静态资源
* 首页
* jsp，模板引擎Thymeleaf
* 装配扩展SpringMVC
* 增删改查
* 拦截器
* 国际化





```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
    CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
    if (!registry.hasMappingForPattern("/webjars/**")) {
        customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
                                             .addResourceLocations("classpath:/META-INF/resources/webjars/")
                                             .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
    String staticPathPattern = this.mvcProperties.getStaticPathPattern();
    if (!registry.hasMappingForPattern(staticPathPattern)) {
        customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
                                             .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
                                             .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
}
```

* 总结：
  1. 在springboot，可以用以下方式处理静态资源
     * webjars       localhost:8080/webjars/...
     * public，static，/**，resources     localhost:8080/
  2. 优先级：resources>static（默认）>public