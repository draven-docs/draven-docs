# SpringCloudAlibaba

## 上线项目

IT牧场

## 环境准备

maven 

http://maven.apache.org/download.cgi

https://downloads.apache.org/maven/maven-3/

Wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz

idea 

jdk1.8

```
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn/java/jdk/8u241-b07/1f5b5a70bf22433b84d0e960903adac8/jdk-8u241-linux-x64.tar.gz


https://www.oracle.com/webapps/redirect/signon?nexturl=https://download.oracle.com/otn/java/jdk/8u241-b07/1f5b5a70bf22433b84d0e960903adac8/jdk-8u241-linux-x64.tar.gz


export JAVA_HOME=/docker/jdk/jdk1.8.0_241
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH
```

# 微服务拆分

## 方法论

### 领域驱动设计

Domain Driven Design

推荐书籍《领域驱动设计》《实现领域驱动设计》

### 面向对象

by name./by verb.（行为）

## 划分纬度

### 职责划分

### 通用性划分

## 合理粒度

良好的满足业务

增量迭代

持续进化

# 架构图

见 3.5 3.6 需要补充

# 数据库设计

## 工具

MySQlWorkbench![0001 mysqlworkbench建模](0001%20mysqlworkbench建模.png)

# 项目文档

Https://t.itmuch.com/doc.html



# 小程序

Https://mp.weixin.qq.com

注册流程需整理 本人 18335927508@163.com

# 前端代码

Https://github.com/eacdy/itmuch-miniapp

todo: 里面的知识点需要整理

包括小程序工具简单的使用



## 技术选型

spring+mybatis+mvc+Spring cloud Alibaba

## 工程结构规划

Dto

entity

Enums

## 项目框架整合





@resource 与@Autowire的区别

https://www.imooc.com.article

https://www.imooc.com/article/287865





整合SpringCloudAlibaba

```xml
<properties>
  <java.version>1.8</java.version>
  <spring-cloud.version>Greenwich.RELEASE</spring-cloud.version>
  <spring-cloud-alibaba.version>2.1.1.RELEASE</spring-cloud-alibaba.version>
  <spring-boot-admin.version>2.1.1</spring-boot-admin.version>
  <lombok.version>1.18.10</lombok.version>
  <zipkin.version>2.11.8</zipkin.version>
</properties>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>${spring-cloud.version}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>

    <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-alibaba-dependencies</artifactId>
      <version>${spring-cloud-alibaba.version}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>

    <dependency>
      <groupId>de.codecentric</groupId>
      <artifactId>spring-boot-admin-starter-server</artifactId>
      <version>${spring-boot-admin.version}</version>
    </dependency>
    <dependency>
      <groupId>de.codecentric</groupId>
      <artifactId>spring-boot-admin-starter-client</artifactId>
      <version>${spring-boot-admin.version}</version>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>${lombok.version}</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-server</artifactId>
      <version>${zipkin.version}</version>
    </dependency>
    <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-autoconfigure-ui</artifactId>
      <version>${zipkin.version}</version>
      <!--<scope>runtime</scope>-->
    </dependency>
  </dependencies>
</dependencyManagement>
```



# nacos

概念：服务提供者 服务消费者

服务发现讲解 注册中心

https://nacos.io/zh-cn/docs/quick-start.html

```shell
# 获取安装包
wget https://github.com/alibaba/nacos/releases/download/1.1.4/nacos-server-1.1.4.tar.gz
```

http://172.16.244.128:8848/nacos/

问题一：各种环境的搭建以及各种集群的搭建

将用户以及内容中心注册到nacos

领域模型

见官方文档

```yaml
sspring:
  cloud:
    nacos:
      config:
        server-addr: 172.16.244.128:8848
        file-extension: yaml
        namespace: 379341d6-02ee-4700-ace5-fa3e3a486c4a
        #group: NDEFAULT_GROUP
      discovery:
        server-addr: 172.16.244.128:8848
        service: ${spring.application.name}
        group: NDEFAULT_GROUP
        namespace: 379341d6-02ee-4700-ace5-fa3e3a486c4a
        cluster-name: NJ
        ip: 172.16.244.128
        port: 8848
        metadata: # 元数据设置
          instance: a
          haha: b
          version: v1
```

## 元数据

提供描述信息

微服务调用更加灵活

​	版本控制

### 设置元数据

服务 集群 实例级别

#### 控制台 

#### 配置文件

namespace 隔离机制



# Ribbon

服务器负载均衡

客户端负载均衡

## 架构演进

从nacos中获取实例，自身实现均衡算法

nacos中包含ribbon无需额外的配置

```shell
# 读取配置。默认值 com.netflix.client.config.DefaultClientConfigImpl
com.netflix.client.config.IClientConfig
# 负载均衡规则 选择实例 默认值 com.netflix.loadbalancer.ZoneAvoidanceRule
com.netflix.loadbalancer.IRule
# 筛选掉ping不通的实例 默认值 com.netflix.loadbalancer.DummyPing
com.netflix.loadbalancer.IPing
# 交给Ribbon的实例列表 
# Alibaba com.alibaba.cloud.nacos.ribbon.NacosServerList
# Ribbon com.netflix.loadbalancer.ConfigurationBasedServerList
com.netflix.loadbalancer.ServerList
# 过滤掉不符合的实例
# org.springframework.cloud.netflix.ribbon.ZonePreferenceServerListFilter
com.netflix.loadbalancer.ServerListFilter
# Ribbon入口 com.netflix.loadbalancer.ZoneAwareLoadBalancer
com.netflix.loadbalancer.ILoadBalancer
# 更新交给Ribbon的List的策略 com.netflix.loadbalancer.PollingServerListUpdater
com.netflix.loadbalancer.ServerListUpdater
```

## 内置负载均衡规则

结合图片说明 6-5

### 粒度控制

#### java方式

```java
// 谁调用谁配置
/**
* 
*
*/
@Configuration
@RibbonClient(name="service-name",configuration="RibbonConfiguration.class")
public class XXXRibbonConfiguration{
  
}

/**
* 规则配置类
* 
*/
@Configuration
public class  RibbonConfiguration {
	@Bean
  public IRule ribbonRule(){
     return new RandomRule(); 
  }
}
```

注意：此配置类不要被@SpringBootApplication所扫描，会导经典问题Spring上下文重叠，从而引起事物失效问题，对于Rabbon配置类会将某个服务的负载均衡规则作为全局配置而产生影响，父子上下文问题

例如：spring整合springmvc时，包扫描配置，会导致事务不生效

#### 属性配置

```yaml
service-name:
 ribbon:
 # 配置负载均衡的规则 全限定类名
  NFLoadBalancerRuleClassName: com.netflix.loadbalancer.Random
```

#### 全局配置

```java
/**
* 
* 此处替换为全局负载均衡策略生效
*@RibbonClients(deffaultConfiguraion="RibbonConfiguration.c* lass")
*/
@Configuration
@RibbonClients(defaultConfiguraion="RibbonConfiguration.class")
public class XXXRibbonConfiguration{
  
}

/**
* 规则配置类
* 
*/
@Configuration
public class  RibbonConfiguration {
	@Bean
  public IRule ribbonRule(){
     return new RandomRule(); 
  }
}
```

#### 配置项说明

##### java方式

```java
/**
* 规则配置类
* 
*/
@Configuration
public class  RibbonConfiguration {
	@Bean
  public IRule ribbonRule(){
     return new RandomRule(); 
  }
  
  @Bean
  public IPing ping(){
    
  }
  
  /*
    .
    .
    .
    */
}
```

##### 属性方式

```yaml
service-name:
 ribbon:
    NFLoadBalancerClassName: 配置ILoadBalancer的实现类
    NFLoadBalancerRuleClassName: 配置IRule的实现类
    NFLoadBalancerPingClassName: 配置IPing的实现类
    NIWSServerListClassName: 配置ServerList的实现类
    NIWSServerListFilterClassName: 配置ServerListFilter的实现类
```

## 饥饿加载

```yaml
# 不配置会导致初次调用时，比较慢
ribbon:
 eager-load:
  enabled: true
  clients: service-name1,service-name2,...,xxx
```

## 适应Nacos权重

https://www.imooc.com.article/288660

遵循 spring cloud commons 新版本已经整合

```java
/**
*
*
*/
```

## 基于元数据的版本控制

https://www.imooc.com.article/288674



# Fegin

代码不可读

复杂的url不便于维护

难以相应需求的变化

编程的体验不统一



声明式客户端

```java
@EnableFeignClient
```

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

## 组成

```shell
# feign的入口 默认值 Feign.Builder
org.springframework.cloud.openfeign.FeignClientBuilder

# Feign底层用什么去请求
# 与Ribbon配合时org.springframework.cloud.openfeign.ribbon.LoadBalancerFeignClient
# 不与Ribbon配合 feign.Client.Default
feign.Client

# 契约 注解支持org.springframework.cloud.openfeign.support.SpringMvcContract
feign.Contract

# 编码器 用于将对象转换成HTTP请求消息体 org.springframework.cloud.openfeign.support.SpringEncoder
feign.codec.Encoder

# 解码器 将响应消息体转换成对象org.springframework.cloud.openfeign.support.ResponseEntityDecoder
feign.codec.Decoder

# 日志管理器 feign.slf4j.Slf4jLogger
feign.Logger

# 用于为每个请求添加通用逻辑
feign.RequestInterceptor
```

## 日志级别

```shell
# 这里指定的Level级别是FULL，Feign支持4种级别：
- NONE：不记录任何日志信息，这是默认值。
- BASIC：仅记录请求的方法，URL以及响应状态码和执行时间
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。
```

### java方式

```java
//1. 客户端指定配置类
@FeignClient(value = "service-name", fallback = UserFeignClientFallback.class, configuration = FeignConfig.class)
public interface UserFeignClient {
    @GetMapping("/user/{id}")
    User queryUserById(@PathVariable("id") Long id);
}

// 指定日志级别
@Configuration //注意父子上下文 可以不写
public class FeignConfig {
  @Bean
  public Logger.Level level(){
    return logger.Level.FULL;
  }
}
```

```java
logging:
  level:
    # 此处填写所配置的日志级别类的全限定类，并指定日志级别日志级别必须debug
    com.noxus.draven.config.UserFeignClient: debug
```

### 属性方式

```yaml
# 方式 feign.client.config.<feignName>.loggerLevel: xxx
# 位置 调用方配置此文件
fegin:
 client:
  config:
   # 被调用的微服务名称
   service-name:
    loggerLevel: full # 指定日志级别
```

### 全局配置

#### java方式

```java
// 指定日志级别 这里利用父子上下文问题 但是不推荐（不推荐）
@Configuration //注意父子上下文 可以不写
public class FeignConfig {
  @Bean
  public Logger.Level level(){
    return logger.Level.FULL;
  }
}


// 此处指定日志配置类的配置类
@EnableFeginClients(defaultConfiguration=FeignConfig.class)
public class ServiceApplication{
  psvm();
}
```

#### 属性方式

```yaml
feign:
 client:
  config:
   # 全局配置
   default: # 此处default 则为全局配置生效
    loggerLever: full
```

## 支持配置项

### java方式

```shell
# 指定日志级别 
Logger.Level
# 指定重试策略
Retryer
# 指定错误解码器
ErrorDecoder
# 超时时间
Request.Options
# 拦截器
Collection<RequestInterceptor>
# 用于设置Hystrix的配置属性，Feign整合Hystrix才会用
SetterFactory
```



### 属性方式

```yaml
fegin:
 client:
  <feignName>:
   connectTimeout: 5000 # 连接超时时间
   readTimeout: 5000 # 读取超时时间
   LoggerLevel: full # 日志级别
   errorDecoder: com.example.SimpleErrorDecoder # 错误解码器
   retryer: com.example.SimpleRetryer # 重试策略
   requestinterceptors:
    - com.example.FooRequestInterceptor # 拦截器
   # 是否对404错误码解码
   # 处理逻辑详见feign.SynchronousMethodHandler#executeAndDecode
   decode404: false
   encoder: com.example.SimpleEncoder # 编码器
   decoder: com.example.SimpleDecoder # 解码器
   contract: com.example.SimpleContract # 契约
 
```

## 继承

由于调用方与被调用方代码可以共用，被调用方实现接口，调用方继承该接口

## 多参数构造

Https://www.imooc.com/article/289000

```yaml
spring:
 main:
  allow=bean-definition-overring: true
```

```java
@FeignClient(name = "microservice-provider-user")
public interface UserFeignClient {
  @RequestMapping(value = "/get", method = RequestMethod.GET)
  public User get1(@RequestParam("id") Long id, @RequestParam("username") String username);
}



@FeignClient(name = "microservice-provider-user")
public interface UserFeignClient {
  @RequestMapping(value = "/get", method = RequestMethod.GET)
  public User get2(@RequestParam Map<String, Object> map);
}


/**
* 无法使用feigin的继承特性
*/
@FeignClient(name = "microservice-provider-user")
public interface UserFeignClient {
  @RequestMapping(value = "/get", method = RequestMethod.GET)
  public User get2(@SpringQueryMap object obj);
}



@RestController
public class UserController {
  @PostMapping("/post")
  public User post(@RequestBody User user) {
    ...
  }
}


@FeignClient(name = "microservice-provider-user")
public interface UserFeignClient {
  @RequestMapping(value = "/post", method = RequestMethod.POST)
  public User post(@RequestBody User user);
}
```

## Fegin脱离Ribbon

```java
@FeginClient(name="xxx-Name", url="指定跳转位置")
public interface TestFegin{
  
}
```

## RestTemplate vs Fegin





可读性 可维护性   一般 极佳

开发体验			    欠佳 极佳

性能						很好 中等 性能 r=2f

灵活性					极佳 中等（内置功能可满足大多数）

## 优化

### 引入连接池

```xml
<dependency>
  <groupId>io.github.openfegin</groupId>
  <artifactId>fegin-httpclient</artifactId>
</dependency>
<dependency>
  <groupId>io.github.openfegin</groupId>
  <artifactId>fegin-okhttp</artifactId>
  <version>10.1.0</version>
</dependency>
```

   

```yaml
fegin:
 # 默认 urlconnection  
 httpclient:
  enabled: false
  # 最大连接数
  max-connections: 200
  # feign 单个路径的最大连接数
  max-connections-per-route: 50
# 还支持 okhttp 等 
 okhttp:
  enabled: true
```

### 配置合理的日志级别



# Sentinel

容错方案

超时

限流

仓壁模式

https://github.com/alibaba/Sentinel/releases/download/1.7.0/sentinel-dashboard-1.7.0.jar

```shell
java -Dserver.port=8090 -Dcsp.sentinel.dashboard.server=172.16.244.128:8090 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```



```shell
 # 未解决
 c.a.c.s.dashboard.metric.MetricFetcher   : Failed to fetch metric from <http://93.184.216.34:8719/metric?startTime=1582471563000&endTime=1582471569000&refetch=false> (ConnectionException: 拒绝连接)
```



# FQA

https://www.imooc.com/article289005

jdk8新特性



```bash
error
feign.FeignException: status 405 reading UserFeignClient#get0(User); content:
{"timestamp":1482676142940,"status":405,"error":"Method Not Allowed","exception":"org.springframework.web.HttpRequestMethodNotSupportedException","message":"Request method 'POST' not supported","path":"/get"}
```

