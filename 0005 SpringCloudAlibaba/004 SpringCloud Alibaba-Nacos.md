# Spring Cloud Alibaba-Nacos

主页

https://github.com/spring-cloud-incubator/spring-cloud-alibaba/blob/master/README-zh.md

## 组件一 Nacos

安装说明：

https://nacos.io/zh-cn/docs/what-is-nacos.html

说明：

​		Spring Cloud Alibaba Nacos Config 是 Config Server 和 Client 
的替代方案，客户端和服务器上的概念与 Spring Environment 和 PropertySource 有着一致的抽象，在特殊的 bootstrap 阶段，配置被加载到 Spring 
环境中。当应用程序通过部署管道从开发到测试再到生产时，您可以管理这些环境之间的配置，并确保应用程序具有迁移时需要运行的所有内容。

搭建：

单机版：看说明文档即可

### 关键特性

 服务发现和服务健康监测

 动态配置服务，带管理界面

 动态 DNS 服务

 服务及其元数据管理

## 下载

 默认下载 jar 包，本人采用 docker 一键安装部署

```
#nacos
docker run -p 8848:8848 \
--name nacos-server \
-e MODE=standalone \	# 单节点启动
--privileged=true \
-v /data/docker/var/lib/nacos/conf/application.properties:/home/nacos/conf/application.properties \
-d docker.io/nacos/nacos-server:1.0.0
```

 默认路径 : http://localhost:8848/nacos

 默认账户密码均为 nacos

## 生产级

 注意 : 现在全部采用Nacos1.0.0为兼容SpringClou以及Boot2.1

 创建文件夹挂载目录

```
mkdir -p /docker/nacos/conf
mkdir -p /docker/nacos/data
```

 需 5.7 以上版本 MySQL

```
docker run -p 3306:3306 \
--name nacos-mysql \
-e MYSQL_ROOT_PASSWORD=agefades \ # 密码请自定义
--privileged=true \
-v /docker/nacos/data:/var/lib/mysql \
-v /docker/nacos/conf/my.cnf:/etc/mysql/conf.d/mysql.cnf \
-d docker.io/mysql:5.7
```

 修改字符编码（MySQL 默认拉丁文）

```
vim /docker/nacos/conf/my.cnf

# 加入下面内容
[mysqld]
character-set-server=utf8
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

 进入容器，创建数据库

```
docker exec -it nacos-mysql bash

mysql -u root -p
# 输入自定义密码
# 创建数据库
create database nacos;
```

 创建 nacos 配置文件

```
# 首先创建配置文件
vim /docker/nacos/conf/application.properties

# spring
server.servlet.contextPath=${SERVER_SERVLET_CONTEXTPATH:/nacos}
server.contextPath=/nacos
server.port=${NACOS_SERVER_PORT:8848}
spring.datasource.platform=${SPRING_DATASOURCE_PLATFORM:""}
nacos.cmdb.dumpTaskInterval=3600
nacos.cmdb.loadDataAtStart=false


spring.datasource.platform=mysql
db.num=1
# ip : port 看是否需要修改
db.url.0=jdbc:mysql://localhost:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&serverTimezone=Asia/Shanghai
db.user=root
# 密码需要修改
db.password=agefades


server.tomcat.accesslog.enabled=true
server.tomcat.accesslog.pattern=%h %l %u %t "%r" %s %b %D
# default current work dir
server.tomcat.basedir=
## spring security config
### turn off security

nacos.security.ignore.urls=/,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-fe/public/**,/v1/auth/login,/v1/console/health/**,/v1/cs/**,/v1/ns/**,/v1/cmdb/**,/actuator/**,/v1/console/server/**
# metrics for elastic search
management.metrics.export.elastic.enabled=fasle
management.metrics.export.influx.enabled=false

nacos.naming.distro.taskDispatchThreadCount=10
nacos.naming.distro.taskDispatchPeriod=200
nacos.naming.distro.batchSyncKeyCount=1000
nacos.naming.distro.initDataRatio=0.9
nacos.naming.distro.syncRetryDelay=5000
nacos.naming.data.warmup=true
```

 启动容器

```
docker run -p 8848:8848 \
--name nacos-server \
-e MODE=standalone \
--privileged=true \
-v /docker/nacos/conf/application.properties:/home/nacos/conf/application.properties \
-d docker.io/nacos/nacos-server:1.0.0
```

[![UTOOLS1561962757897.png](https://camo.githubusercontent.com/75aaf38c3305850f44f83161425202706a498db9/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f30312f3564313961393038613066646231303432372e706e67)](https://camo.githubusercontent.com/75aaf38c3305850f44f83161425202706a498db9/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f30312f3564313961393038613066646231303432372e706e67)

## 

## 服务注册和发现

 目前主流的服务注册和发现组件有 :

 Consul

 Eureka（闭源）

 Etcd ..

### 

### 构建服务提供者

```
<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
			<version>0.9.0.RELEASE</version>
</dependency>

server:
  port: 8762
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848	# nacos 服务地址
@SpringBootApplication
@EnableDiscoveryClient 	// 启用服务注册发现
public class NacosProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(NacosProviderApplication.class, args);
	}

}
```

### 

###  构建服务消费者

 同上，略

[![UTOOLS1561963052041.png](https://camo.githubusercontent.com/6b4124161da83b83ace3f8abafeec54d15b90105/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f30312f3564313961613264333064633131363937392e706e67)](https://camo.githubusercontent.com/6b4124161da83b83ace3f8abafeec54d15b90105/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f30312f3564313961613264333064633131363937392e706e67)

## 

## 服务调用

 nacos 实现了 Spring Cloud 服务注册和发现的相关接口，所以与其他服务注册与发现组件无缝切换。

 RestTemplate

 Feign

### 

### 提供服务

```
@RestController
@Slf4j
public class ProviderController {

@GetMapping("/hi")
public String hi(@RequestParam(value = "name",defaultValue = "forezp",required = false)String name){

        return "hi "+name;
    }
}
```



### 消费服务



#### RestTemplate

 使用 Ribbon 作为负载均衡组件

```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-ribbon</artifactId>
</dependency>
	@LoadBalanced	// 启用 Ribbon
	@Bean
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
@RestController
public class ConsumerController {

    @Autowired
    RestTemplate restTemplate;

 	@GetMapping("/hi-resttemplate")
    public String hiResttemplate(){
        return restTemplate.getForObject("http://nacos-provider/hi?name=resttemplate",String.class);
    }
```

#### 

#### FeignClient

```
<dependency>
	<groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
@EnableFeignClients		// 开启 Feign
public class NacosConsumerApplication {

	public static void main(String[] args) {
		SpringApplication.run(NacosConsumerApplication.class, args);
	}
@FeignClient("nacos-provider")
public interface ProviderClient {

    @GetMapping("/hi")
    String hi(@RequestParam(value = "name", defaultValue = "forezp", required = false) String name);
}
@RestController
public class ConsumerController {


    @Autowired
    ProviderClient providerClient;

    @GetMapping("/hi-feign")
    public String hiFeign(){
       return providerClient.hi("feign");
    }
}
```

## 

## 配置中心

```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-alibaba-nacos-config</artifactId>
	<version>0.9.0.RELEASE</version>
</dependency>
```

 bootstrap.yml (不是 application.yml!)

```
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        file-extension: yaml	# 配置的扩展名
        prefix: nacos-provider	# 配置文件前缀 默认为 spiring.application.name
  profiles:
    active: dev
    # 完整格式 ${prefix}-${spring.profile.active}.${file-extension}
```

[![UTOOLS1561963845758.png](https://camo.githubusercontent.com/29483ec4a2a7bb10cda8b059d297d5c4d47bda17/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f30312f3564313961643438643132643237313530372e706e67)](https://camo.githubusercontent.com/29483ec4a2a7bb10cda8b059d297d5c4d47bda17/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f30312f3564313961643438643132643237313530372e706e67)

```
@RestController
@RefreshScope	// 配置热加载
public class ConfigController {

    @Value("${username:lily}")
    private String username;

    @RequestMapping("/username")
    public String get() {
        return username;
    }
}
```

# 集群

## Linux集群搭建

首先我们在一个目录下新建一个集群文件夹

```shell
mkdir nacos-cluster
```

然后解压三个nacos的文件放进去分别命名为nacos1，nacos2，nacos3

然后我们先来将数据库新建好（集群版基于数据库）

首先新建一个名叫nacos的数据库

然后在里面执行sql文件，后面的用户名可以更改，密码采用的是加密算法需要去重新生成加密算法密码然后添加

### MySQL数据

密码默认为nacos(默认加密的算法)

数据库修改之后修改配置文件

进入他的conf目录下找到

cluster.conf.example这里只是一个模板我们把它复制一下然后改个名字,将他改为cluster.conf

```
cp cluster.conf.example cluster.conf
```

然后进入编辑修改集群地址，（最少三个）(这里配成我们的ip和端口号)

```
vim cluster.conf
```

然后去修改他的mysql路径

首先编辑配置文件application.properties

```
vim application.properties
```

然后在里面加上mysql的配置信息（路径和用户名密码写自己的还有注意数据库名一致）(三个都要改还有端口)

```
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://39.108.158.33:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&serverTimezone=Asia/Shanghai
db.user=root
db.password=bigkang
```

然后修改他的端口号（因为我们在一台机器中进行集群所以需要修改端口号）

分别修改

```
nacos1的配置文件
server.port=8801

nacos2的配置文件
server.port=8802

nacos3的配置文件
server.port=8803
```

这样我们就编写好了集群然后我们来启动吧

直接进入bin目录下找到启动脚本直接启动

```
 sh startup.sh 
```

我们一个一个地启动就完成了（注：可能会出现很多问题）

# 问题描述

首先是秒退那么肯定就是Jvm的内存不够了，由于集群模式默认的启动大小是2个g所以我们需要去进行堆内存的调整具体如下

我们可以看到单机版启动512m，集群的将他已经修改默认是2g，我们把它修改为

```
-server -Xms256m -Xmx512m  -XX:PermSize=128m -XX:MaxPermSize=256m
```

然后再启动就行了

 如果还是有问题那么我们就先去查看他的日志文件

 进入logs然后查看文件

这个原因可能是因为数据源没写对，还有就是使用了8.0的数据库作为nacos的数据存储，只要将他改成5.6或者修改mysql8的一些配置文件就能实现

如果出现了各种错误首先我们先排查两个日志文件

```
vim nacos.log 

vim start.out 
```

通过这两个日志文件我们就能定位他的错误了

# 环境搭建完毕一键清理

## 清理单机版生产级Docker

停止以及删除容器

```
docker stop nacos-server
docker stop nacos-mysql

docker rm nacos-server
docker rm nacos-mysql
```

删除镜像（可以不用删除留着以后用）

```
docker rmi docker.io/nacos/nacos-server
docker rmi docker.io/mysql:5.7
```

删除挂载目录（删除后不可恢复）

```
rm -rf /docker/nacos
```

重新快速部署

```
docker run -p 3306:3306 \
--name nacos-mysql \
-e MYSQL_ROOT_PASSWORD=bigkang \
--privileged=true \
-v /docker/nacos/data:/var/lib/mysql \
-v /docker/nacos/conf/my.cnf:/etc/mysql/conf.d/mysql.cnf \
-d docker.io/mysql:5.7


--------------------------------------------------

docker run -p 8848:8848 \
--name nacos-server \
-e JAVA_TS='-Xmx256m -Xms256m -XX:+UseG' \
-e MODE=standalone \
--privileged=true \
-v /docker/nacos/conf/application.properties:/home/nacos/conf/application.properties \
-d docker.io/nacos/nacos-server
```

## 

## 清理集群版生产级Docker

停止容器

```
docker stop  nacos-node1
docker stop  nacos-node2
docker stop  nacos-node3
```

删除容器

```
docker rm  nacos-node1
docker rm  nacos-node2
docker rm  nacos-node3
```







# 附录

```mysql
/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info   */
/******************************************/
CREATE TABLE `config_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) DEFAULT NULL,
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  `c_desc` varchar(256) DEFAULT NULL,
  `c_use` varchar(64) DEFAULT NULL,
  `effect` varchar(64) DEFAULT NULL,
  `type` varchar(64) DEFAULT NULL,
  `c_schema` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_aggr   */
/******************************************/
CREATE TABLE `config_info_aggr` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) NOT NULL COMMENT 'group_id',
  `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
  `content` longtext NOT NULL COMMENT '内容',
  `gmt_modified` datetime NOT NULL COMMENT '修改时间',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_beta   */
/******************************************/
CREATE TABLE `config_info_beta` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_tag   */
/******************************************/
CREATE TABLE `config_info_tag` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_tags_relation   */
/******************************************/
CREATE TABLE `config_tags_relation` (
  `id` bigint(20) NOT NULL COMMENT 'id',
  `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
  `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `nid` bigint(20) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`nid`),
  UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = group_capacity   */
/******************************************/
CREATE TABLE `group_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_group_id` (`group_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = his_config_info   */
/******************************************/
CREATE TABLE `his_config_info` (
  `id` bigint(64) unsigned NOT NULL,
  `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `data_id` varchar(255) NOT NULL,
  `group_id` varchar(128) NOT NULL,
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL,
  `md5` varchar(32) DEFAULT NULL,
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
  `src_user` text,
  `src_ip` varchar(20) DEFAULT NULL,
  `op_type` char(10) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`nid`),
  KEY `idx_gmt_create` (`gmt_create`),
  KEY `idx_gmt_modified` (`gmt_modified`),
  KEY `idx_did` (`data_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = tenant_capacity   */
/******************************************/
CREATE TABLE `tenant_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';

CREATE TABLE `tenant_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `kp` varchar(128) NOT NULL COMMENT 'kp',
  `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
  `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
  `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
  `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
  `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
  `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';

CREATE TABLE users (
  username varchar(50) NOT NULL PRIMARY KEY,
  password varchar(500) NOT NULL,
  enabled boolean NOT NULL
);

CREATE TABLE roles (
  username varchar(50) NOT NULL,
  role varchar(50) NOT NULL
);

INSERT INTO users (username, password, enabled) VALUES ('agefades', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);

INSERT INTO roles (username, role) VALUES ('agefades', 'ROLE_ADMIN');
```

