# Jenkins-Weblogic

```shell
# ǰ��֪ʶ
# ��װJenkins
# ���� JDK
# ���� Maven


# ���
# Weblogic Deploy Plugin
```



## ��װ���

### WebLogic Deployment Plugin

```shell
# ��װ Weblogic Deploy Plugin
# ���������ٶ�
```

## ȫ������

```shell
# ����weblogic���
# Additional classpath ������weblogicͨ��
	wlthint3client.jar
# Configuration File ����weblogic������Ϣ
	configuration.xml
# Disable ��Ҫ��ѡ
```



## ��Ŀ����

```shell
# >����maven����
# ѡ�� Deploy the artifact to any WebLogic environments
# ��д����
# Task name
	# �������� ����Ҫ��
# Environment
	# ���������ļ���ȡ
# Name
	# ���������
# Base directory where the resource to deploy can be found	
	# �����Դ·��
# Built resource to deploy
	# ����λ��
# Targets
	# ��֪�������
# WebLogic deployment plan		
	# δ֪
# WebLogic libraries	
	# ��Ϊ���

# ������
Command Line	
-adminurl http://ip:port/console -user weblogic -password weblogic123 -debug -remote -verbose  -upload -name xxx.war -source sources/path/xxx.war -targets targets/xxx.war -deploy
```



# ����

## ͨ�Ű�

wlthint3client.jar

## �����ļ�

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

