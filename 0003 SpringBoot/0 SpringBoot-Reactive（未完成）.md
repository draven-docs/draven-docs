

# WebFlux

Reactive响应式编程

官网：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-webflux
SpingBoot2底层是用spring5,开始支持响应式编程，Spring又是基于Reactor试下响应式。

## 基础

依赖于事件，事件驱动(Event-driven)
		一系列事件称为“流”
		异步
		非阻塞

​	观察者模式

reactive-streams学习资料：http://www.reactive-streams.org/
web-flux相关资料：https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#spring-webflux

Keynote

​	Spring WebFlux是Spring Framework 5.0中引入的新的反应式Web框架与Spring MVC不同，它不需要Servlet API，完全异步和非阻塞，并 通过Reactor项目实现Reactive Streams规范。
推荐：RxJava

```
Spring WebFlux应用程序不严格依赖于Servlet API，因此它们不能作为war文件部署，也不能使用src/main/webapp目录
```

```
可以整合多个模板引擎
除了REST Web服务外，您还可以使用Spring WebFlux提供动态HTML内容。Spring WebFlux支持各种模板技术，包括Thymeleaf，FreeMarker
```



## Mono

包含0或一个元素的异步序列

mono ->单一对象  T   redis->唯一id->唯一的Mono<T>

## Flux

表示的是包含 0 到 N个元素的异步序列

flux->数组列表对象 List<T>   redis->某类标志->Flux<T>



Flux 和 Mono之间进行相互转换

## 风格

### 基于功能和基于注解

基于注解非常接近Spring MVC模型

```java
@RestController 
@RequestMapping（“/ users”）
 public  class MyRestController {

	@GetMapping（“/ {user}”）
	 public Mono <User> getUser（ @PathVariable Long user）{
				 // ...
	 }
	 @GetMapping（“/ {user} / customers”）
	 public Flux <Customer> getUserCustomers（ @PathVariable Long user）{
				 // ...
	 }
	 @DeleteMapping（“/ {user}”）
	 public Mono <User> deleteUser（ @PathVariable Long user）{
				 // ...
	 }
 }
```
### 路由配置与请求的实际处理分开

```
@Configuration
public  class RoutingConfiguration {

	@Bean
	public RouterFunction <ServerResponse> 		    
	monoRouterFunction（UserHandler userHandler）{
		return route（GET（ “/{user}”）
	       .and（accept（APPLICATION_JSON）），userHandler :: getUser）
	     	.andRoute（GET（“/{user}/customers”）
	     	.and（accept（APPLICATION_JSON）），userHandler :: 	   
	     	getUserCustomers）
			.andRoute（DELETE（“/{user}”）
		.and（accept（APPLICATION_JSON）,userHandler::deleteUser）;
	}

}

@Component
public class UserHandler {
	//公共 
	Mono <ServerResponse> getUser（ServerRequest请求）{
					 // ...
	}

	public Mono <ServerResponse> getUserCustomers（ServerRequest request）{
					 // ...
	}
	公共 
	Mono <ServerResponse> deleteUser（ServerRequest请求）{
					 // ...
				}
}
```

## 案例

	1、WebFlux中，请求和响应不再是WebMVC中的ServletRequest和ServletResponse，而是ServerRequest和ServerResponse
	
	2、加入依赖，如果同时存在spring-boot-starter-web，则会优先用spring-boot-starter-web
	
	测试
	localhost:8080/api/v1/user/test
	
	3、启动方式默认是Netty,8080端口
	4、参考：https://spring.io/blog/2016/04/19/understanding-reactive-types

pom.xml

	<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
	</dependency>
```java
@RestCOntroller
@RequestMapping("/")
public class XXXController{
    @GetMapping("")
    public Mono<String> test(){
        return Mono.just("哇咔咔");
    }
}
```

代码暂时略



# 反应式客户端

​	官网地址：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-webclient