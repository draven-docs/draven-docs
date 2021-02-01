# Jenkins-Weblogic

```shell
# 前置知识
# 安装Jenkins
# 配置 JDK
# 配置 Maven


# 插件
# Weblogic Deploy Plugin
```



## 安装插件

### WebLogic Deployment Plugin

```shell
# 安装 Weblogic Deploy Plugin
# 如果不会请百度
```

## 全局配置

```shell
# 启用weblogic插件
# Additional classpath 配置与weblogic通信
	wlthint3client.jar
# Configuration File 配置weblogic基本信息
	configuration.xml
# Disable 不要勾选
```



## 项目配置

```shell
# >构建maven工程
# 选择 Deploy the artifact to any WebLogic environments
# 填写配置
# Task name
	# 任务名称 不做要求
# Environment
	# 根据配置文件读取
# Name
	# 部署包名称
# Base directory where the resource to deploy can be found	
	# 部署包源路径
# Built resource to deploy
	# 部署位置
# Targets
	# 不知道干嘛的
# WebLogic deployment plan		
	# 未知
# WebLogic libraries	
	# 作为组件

# 命令行
Command Line	
-adminurl http://ip:port/console -user weblogic -password weblogic123 -debug -remote -verbose  -upload -name xxx.war -source sources/path/xxx.war -targets targets/xxx.war -deploy
```



# 配置

## 通信包

wlthint3client.jar

## 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<config xmlns="http://org.jenkinsci.plugins/WeblogicDeploymentPlugin"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://org.jenkinsci.plugins/WeblogicDeploymentPlugin plugin-configuration.xsd"> 
    <weblogic-targets>
              <weblogic-target>
                     <name>domainName</name>
                     <target></target>
                     <host>ip</host>
                     <port>port</port>
                     <login>username</login>
                     <password>password</password>
                     <authMode>BY_LOGIN</authMode>
              </weblogic-target>
       </weblogic-targets>
</config>  
```



# FQA

```shell
task completed abnormally (exit code = 1). Check your Weblogic Deployment logs.
```

