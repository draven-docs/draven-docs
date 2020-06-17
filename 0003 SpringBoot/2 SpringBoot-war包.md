## SpringBoot2.0.0改造为war

由于要求jar包改用war部署

### 1.pom.xml

将pom.xml文件首部的jar改成war

```
<packaging>war</packaging>
<!--<packaging>jar</packaging>-->
```

排除内置tomcat

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!--该配置排除tomcat start-->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
    <!--该配置排除tomcat end-->
</dependency>
```

添加servlet-api的依赖

```
方式一（推荐）：
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>

方式二：
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>tomcat-servlet-api</artifactId>
    <version>8.0.36</version>
    <scope>provided</scope>
</dependency>

```

war包插件

```
<build>
    <finalName>managerment</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
		<!-- 解决[ERROR] Failed to execute goal org.apache.maven.plugins:maven-war-plugin:3.1.0:war (default-war) on project management_bjs: Error assembling WAR: webxml attribute is required (or pre-existing WEB-INF/web.xml if executing in update mode) -> [Help 1]  -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <failOnMissingWebXml>false</failOnMissingWebXml>
            </configuration>
        </plugin>
    </plugins>
    <!-- mybatisPlus存在无法找到mapper的情况 -->
    <resources>
        <!--拷贝xml文件到classpath下-->
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

### 2.修改启动类

```
我们需要类似于web.xml的配置方式来启动spring上下文了，在Application类的同级添加一个SpringBootStartApplication类

@SpringBootApplication
public class DataInterfaceApplication extends SpringBootServletInitializer {
    public static void main(String[] args) {
        SpringApplication.run(DataInterfaceApplication.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(DataInterfaceApplication.class);
    }
}
```

### 3.使用maven打包

```
mvn clean package
```