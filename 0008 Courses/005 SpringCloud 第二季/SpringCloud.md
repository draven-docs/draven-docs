# SpringCloud

# 技术要求

java8+maven+git+Github+nginx+rabitMQ+SpringBoot

# 说明

一站式解决方案

# 版本选型

SpringCloud H版本 Hoxton SR2 CURRENT GA

SpringBoot  2.2.2.RELEASE

# 版本维护

## 注册中心

eureka

zookeeper

Consul

Nacos

## 服务调用

Ribbon

Loadbalancer



Fegin

OpenFegin

## 服务熔断

Hystrix

resillience4j

sentinel

## 服务网关

zuul

zuul2

gateway

## 服务配置

config

appllo

nacos

## 服务总线

bus

nacos

# 开始学习

pom文件

要形成自己的笔记，拿过来就用





# NACOS

支持CA+CP切换

CP 支持注册持久化模式 以RAFT协议为集群运行模式 ，服务不存在返回错误



curl -X PUT ''$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?ebtity=serverMode$value=CP'





# 热部署

添加jar

插件

```
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

# EUREKA

单机 注册微服务

集群

自我保护机制 一定时间内 不会剔除服务



## 服务发现



# ZOOKEEPER

# Consul



# Ribbon

自定义规则

```shell
@RibbonClient(name="your service",configuration=MyRule.class)
```

轮循原理

```java
com.netflix.loadbalancer.RoundRobinRule
```

```shell
# 42 手写轮训算法
```



# OpenFegin

超时控制

1. 服务提供方 耗时

```yaml
# 客户端默认等待1秒
ribbon:
  ReadTimeout: 5000
  ConnecTimeout: 5000
# 需要总结全部配置
```

日志

```java
@Configuration
public class FeignConfiguration {
    @Bean
    Logger.Level feignLoggerLevel() {
        //这里记录所有，根据实际情况选择合适的日志level
        return Logger.Level.FULL;
    }
}




@Component
@FeignClient(name = "cloud-payment-provider", configuration = FeignConfiguration.class)
public interface PaymentInterface {

    @GetMapping(value = "/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
}




```

```yaml
logging:
  level:
    com.noxus.draven.order.service.fegin.clients.PaymentInterface: debug
```

# Hytrix

延迟和容错。

避免线程不用 不会被长时间占用

半开状态



图形化服务监控

http://localhost:9001/hystrix

http://localhost:8001/hystrix.stream





服务提供方降级







超时怎么解决-不再等待

服务提供者宕机-服务降级

消费 小于业务处理时间 自己主动熔断





## 全局降级









## 结合Fegin降级







## 重要概念

服务降级

假设服务不可用 需要返回友情提示 备选响应

程序异常 超时  服务熔断触发



服务熔断

直接拒绝访问

降级-熔断  还能恢复链路调用

当检测到 微服务调用响应正常后 恢复链路



三种状态：

半开状态

关闭

打开状态

总结：





服务限流

排队有序进行



# 链路调用

客户端



zipkin 直接安装即可

Spring-cloud-starter-zipkin.  整合了链路。



监控地址

```yaml
spring:  
  zipkin:
    base-url: http://116.196.77.250:9411/
  sleuth:
    sampler:
      probability: 1
      
logging:
  level:
    root: INFO
    org.springframework.web.servlet.DispatcherServlet: DEBUG
    org.springframework.cloud.sleuth: DEBUG
```



# GateWay

zuul

## 概述

webflux

熔断 

限流

监控/指标





技术特点

支持断言和过滤器。集成hystrix断路器功能。请求限流 支持路径重写

zuul： servlet 2.5 性能相对较差





## 路由

## 断言

## 过滤器







# Config

配置文件集中配置