# Skywalking

官网：http://skywalking.apache.org/

## 什么是链路追踪

## 什么是skywalking

目前主要的一些 APM 工具有: Cat、Zipkin、Pinpoint、SkyWalking；Apache SkyWalking 是观察性分析平台和应用性能管理系统。提供分布式追踪、服务网格遥测分析、度量聚合和可视化一体化解决方案。

![Skywalking-01](imageskywalking-01.png)



- **Skywalking Agent：** 使用 JavaAgent 做字节码植入，无侵入式的收集，并通过 HTTP 或者 gRPC 方式发送数据到 SkyWalking Collector。

- **SkyWalking Collector：** 链路数据收集器，对 agent 传过来的数据进行整合分析处理并落入相关的数据存储中。

- **Storage：** SkyWalking 的存储，时间更迭，SW 已经开发迭代到了 6.x 版本，在 6.x 版本中支持以 ElasticSearch(支持 6.x)、Mysql、TiDB、H2、作为存储介质进行数据存储。

- **UI：** Web 可视化平台，用来展示落地的数据。

  

## SkyWalking 功能特性

- 多种监控手段，语言探针和服务网格(Service Mesh)
- 多语言自动探针，Java，.NET Core 和 Node.JS
- 轻量高效，不需要大数据
- 模块化，UI、存储、集群管理多种机制可选
- 支持告警
- 优秀的可视化方案



# 服务端

## 环境搭建

```shell
# 获取服务包
http://skywalking.apache.org/zh/downloads/
https://www.apache.org/dyn/closer.cgi/skywalking/6.6.0/apache-skywalking-apm-6.6.0.tar.gz
```

### 使用Elasticsearch

```yaml
# '9200'端口号为SkyWalking配置ElasticSearch所需端口号，
# 'cluster.name'为SkyWalking配置ElasticSearch 集群的名称
version: '3.3'
services:
  elasticsearch:
    image: wutang/elasticsearch-shanghai-zone:6.3.2
    container_name: elasticsearch
    restart: always
    ports:
      - 9200:9200
      - 9300:9300
    environment:
      cluster.name: elasticsearch
```

```shell
# 测试
http://elasticsearchIP:9200/ 
```

```shell
# 修改一系列配置接入Elasticsearch
# apache-skywalking-apm-incubating/config/application.yml

# 注释H2存储方案
# 启用Elasticsearch存储方案
# 修改 ElasticSearch 服务器地址
```

```shell
# 启动 SkyWalking 运行 `startup.bat` `startup.sht`
apache-skywalking-apm-incubating\bin

# 通过浏览器访问 启动成功
http://localhost:8080 
```



### 使用内存数据库

```shell
# 直接启动即可
```



# 客户端

## 三种部署方式

https://github.com/apache/skywalking/blob/master/docs/en/setup/service-agent/java-agent/README.md

```shell
# 配置探针 copy以下全部文件到idea指定位置
apache-skywalking-apm-incubating/agent

```

### Idea配置

```shell
# 注意此处的路径

# `-javaagent`：用于指定探针路径
# `-Dskywalking.agent.service_name`：用于重写 `agent/config/agent.config` 配置文件中的服务名
# `-Dskywalking.collector.backend_service`：用于重写 `agent/config/agent.config` 配置文件中的服务地址

-javaagent:/Users/draven/Documents/documents/java/document/draven-spring/draven-spring-cloud-alibaba/draven-spring-cloud-alibaba-greenwich/noxus-draven-alibaba-greenwich-skywalking/agent/skywalking-agent.jar -Dskywalking.agent.service_name=alibaba-consumer
-Dskywalking.collector.backend_service=localhost:11800
```

### Java启动方式

```shell
java -javaagent:/path/to/skywalking-agent/skywalking-agent.jar -Dskywalking.agent.service_name=nacos-provider -Dskywalking.collector.backend_service=localhost:11800 -jar yourApp.jar
```

## Docker部署

### Assembly

​	Assembly 插件目的是提供一个把工程依赖元素、模块、网站文档等其他文件存放到单个归档文件里

 支持文件 
    zip、tar.gz、tar.bz2、jar、dir、war



项目构建为maven项目

#### maven插件

```shell
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <executions>
                <!-- 配置执行器 -->
                <execution>
                    <id>make-assembly</id>
                    <!-- 绑定到 package 生命周期阶段上 -->
                    <phase>package</phase>
                    <goals>
                        <!-- 只运行一次 -->
                        <goal>single</goal>
                    </goals>
                    <configuration>
                        <finalName>skywalking</finalName>
                        <descriptors>
                            <!-- 配置描述文件路径 -->
                            <descriptor>src/main/resources/assembly.xml</descriptor>
                        </descriptors>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

#### assembly.xml

```shell
# 新建文件
src/main/resources/assembly.xml
```



```xml
<assembly>
    <id>6.0.0-Beta</id>
    <formats>
        <!-- 打包的文件格式，支持 zip、tar.gz、tar.bz2、jar、dir、war -->
        <format>tar.gz</format>
    </formats>
    <!-- tar.gz 压缩包下是否生成和项目名相同的根目录，有需要请设置成 true -->
    <includeBaseDirectory>false</includeBaseDirectory>
    <dependencySets>
        <dependencySet>
            <!-- 是否把本项目添加到依赖文件夹下，有需要请设置成 true -->
            <useProjectArtifact>false</useProjectArtifact>
            <outputDirectory>lib</outputDirectory>
            <!-- 将 scope 为 runtime 的依赖包打包 -->
            <scope>runtime</scope>
        </dependencySet>
    </dependencySets>
    <fileSets>
        <fileSet>
            <!-- 设置需要打包的文件路径 -->
            <directory>agent</directory>
            <!-- 打包后的输出路径 没有值为当前目录-->
            <outputDirectory></outputDirectory>
        </fileSet>
    </fileSets>
</assembly>
```

#### mvn构建

```shell
mvn clean package
mvn clean install

# package：会在 target 目录下创建名为 `skywalking-6.0.0-Beta.tar.gz` 的压缩包
# install：会在本地仓库目录下创建名为 `hello-spring-cloud-external-skywalking-1.0.0-SNAPSHOT-6.0.0-Beta.tar.gz` 的压缩包
```



