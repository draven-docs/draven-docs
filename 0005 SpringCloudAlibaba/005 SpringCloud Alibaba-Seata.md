# Seata

## 环境搭建

```shell
# 参考链接
https://github.com/seata/seata/tree/1.2.0/script
http://seata.io/zh-cn/docs/overview/what-is-seata.html
http://seata.io/zh-cn/docs/dev/mode/at-mode.html

# 修改脚本参数
# 脚本 nacos-config.sh
# 脚本参数 config.txt

# 数据库
CREATE SCHEMA `seata` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_bin ;
# 参加参数-连接nacos的相关配置
sh nacos-config.sh -h 127.0.0.1 -p 8849 -g SEATA_GROUP -t 4dad9a92-3a8e-4456-992f-03433d9320ad

# 默认值-localhost 8848 SEATA_GROUP
sh ${SEATAPATH}/script/config-center/nacos/nacos-config.sh -h 127.0.0.1 -p 8849 -g SEATA_GROUP -t 4dad9a92-3a8e-4456-992f-03433d9320ad
```

## nacos相关

```shell
# 采用nacos作为配置中心
  

```

## seata服务端

```shell
# 采用nacos作为注册中心
# 改造服务端
# 修改registry.conf
# 修改为配置中心
# 修改为注册中心
# 见附录 registry.conf
```

## 版本依赖

```xml
  <!--注 当前版本整合seata 1.1版本 需要修改为 1.2-->
		<dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-seata</artifactId>
        <version>2.2.1.RELEASE</version>
        <exclusions>
          <exclusion>
            <groupId>io.seata</groupId>
            <artifactId>seata-spring-boot-starter</artifactId>
          </exclusion>
        </exclusions>
      </dependency>
      <dependency>
        <groupId>io.seata</groupId>
        <artifactId>seata-spring-boot-starter</artifactId>
        <version>1.2.0</version>
	  </dependency>
```



```yaml
seata:
  enabled: true
  application-id: applicationName
  tx-service-group: my_test_tx_group
  enable-auto-data-source-proxy: true
  config:
    type: nacos
    nacos:
      namespace:
      serverAddr: 127.0.0.1:8848
      group: SEATA_GROUP
      userName: "nacos"
      password: "nacos"
  registry:
    type: nacos
    nacos:
      application: seata-server
      server-addr: 127.0.0.1:8848
      namespace:
      userName: "nacos"
      password: "nacos"
```



# 工作模式

## AT

## TCC

## Saga



# 附录

## nacos改造

### config.txt

```
transport.type=TCP
transport.server=NIO
transport.heartbeat=true
transport.enableClientBatchSendRequest=false
transport.threadFactory.bossThreadPrefix=NettyBoss
transport.threadFactory.workerThreadPrefix=NettyServerNIOWorker
transport.threadFactory.serverExecutorThreadPrefix=NettyServerBizHandler
transport.threadFactory.shareBossWorker=false
transport.threadFactory.clientSelectorThreadPrefix=NettyClientSelector
transport.threadFactory.clientSelectorThreadSize=1
transport.threadFactory.clientWorkerThreadPrefix=NettyClientWorkerThread
transport.threadFactory.bossThreadSize=1
transport.threadFactory.workerThreadSize=default
transport.shutdown.wait=3
service.vgroupMapping.my_test_tx_group=default
service.default.grouplist=127.0.0.1:8091
service.enableDegrade=false
service.disableGlobalTransaction=false
client.rm.asyncCommitBufferLimit=10000
client.rm.lock.retryInterval=10
client.rm.lock.retryTimes=30
client.rm.lock.retryPolicyBranchRollbackOnConflict=true
client.rm.reportRetryCount=5
client.rm.tableMetaCheckEnable=false
client.rm.sqlParserType=druid
client.rm.reportSuccessEnable=false
client.rm.sagaBranchRegisterEnable=false
client.tm.commitRetryCount=5
client.tm.rollbackRetryCount=5
store.mode=db
store.file.dir=file_store/data
store.file.maxBranchSessionSize=16384
store.file.maxGlobalSessionSize=512
store.file.fileWriteBufferCacheSize=16384
store.file.flushDiskMode=async
store.file.sessionReloadReadSize=100
store.db.datasource=druid
store.db.dbType=mysql
store.db.driverClassName=com.mysql.cj.jdbc.Driver
store.db.url=jdbc:mysql://127.0.0.1:3306/seata?characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=GMT%2B8&allowMultiQueries=true&allowPublicKeyRetrieval=true
store.db.user=root
store.db.password=12345678
store.db.minConn=5
store.db.maxConn=30
store.db.globalTable=global_table
store.db.branchTable=branch_table
store.db.queryLimit=100
store.db.lockTable=lock_table
store.db.maxWait=5000
server.recovery.committingRetryPeriod=1000
server.recovery.asynCommittingRetryPeriod=1000
server.recovery.rollbackingRetryPeriod=1000
server.recovery.timeoutRetryPeriod=1000
server.maxCommitRetryTimeout=-1
server.maxRollbackRetryTimeout=-1
server.rollbackRetryTimeoutUnlockEnable=false
client.undo.dataValidation=true
client.undo.logSerialization=jackson
server.undo.logSaveDays=7
server.undo.logDeletePeriod=86400000
client.undo.logTable=undo_log
client.log.exceptionRate=100
transport.serialization=seata
transport.compressor=none
metrics.enabled=false
metrics.registryType=compact
metrics.exporterList=prometheus
metrics.exporterPrometheusPort=9898

```



### nacos-config.sh

```shell
#!/usr/bin/env bash
# Copyright 1999-2019 Seata.io Group.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at、
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

while getopts ":h:p:g:t:" opt
do
  case $opt in
  h)
    host=$OPTARG
    ;;
  p)
    port=$OPTARG
    ;;
  g)
    group=$OPTARG
    ;;
  t)
    tenant=$OPTARG
    ;;
  ?)
    echo " USAGE OPTION: $0 [-h host] [-p port] [-g group] [-t tenant] "
    exit 1
    ;;
  esac
done

if [[ -z ${host} ]]; then
    host=localhost
fi
if [[ -z ${port} ]]; then
    port=8848
fi
if [[ -z ${group} ]]; then
    group="SEATA_GROUP"
fi
if [[ -z ${tenant} ]]; then
    tenant=""
fi

nacosAddr=$host:$port
contentType="content-type:application/json;charset=UTF-8"

echo "set nacosAddr=$nacosAddr"
echo "set group=$group"

failCount=0
tempLog=$(mktemp -u)
function addConfig() {
  curl -X POST -H "${1}" "http://$2/nacos/v1/cs/configs?dataId=$3&group=$group&content=$4&tenant=$tenant" >"${tempLog}" 2>/dev/null
  if [[ -z $(cat "${tempLog}") ]]; then
    echo " Please check the cluster status. "
    exit 1
  fi
  if [[ $(cat "${tempLog}") =~ "true" ]]; then
    echo "Set $3=$4 successfully "
  else
    echo "Set $3=$4 failure "
    (( failCount++ ))
  fi
}

count=0
for line in $(cat $(dirname "$PWD")/conf/seata/config.txt | sed s/[[:space:]]//g); do
  (( count++ ))
	key=${line%%=*}
  value=${line#*=}
	addConfig "${contentType}" "${nacosAddr}" "${key}" "${value}"
done

echo "========================================================================="
echo " Complete initialization parameters,  total-count:$count ,  failure-count:$failCount "
echo "========================================================================="

if [[ ${failCount} -eq 0 ]]; then
	echo " Init nacos config finished, please start seata-server. "
else
	echo " init nacos config fail. "
fi
```

### 创建table

```mysql
-- -------------------------------- The script used when storeMode is 'db' --------------------------------
-- the table to store GlobalSession data
CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store BranchSession data
CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME(6),
    `gmt_modified`      DATETIME(6),
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store lock data
CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(96),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(32),
    `pk`             VARCHAR(36),
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;
```

## 服务端

### registry.conf

```shell
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8849"
    group = "SEATA_GROUP"
    namespace = "4dad9a92-3a8e-4456-992f-03433d9320ad"
    cluster = "default"
    username = "nacos"
    password = "nacos"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    application = "default"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = 0
    password = ""
    cluster = "default"
    timeout = 0
  }
  zk {
    cluster = "default"
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    cluster = "default"
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    cluster = "default"
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    application = "default"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    cluster = "default"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}



config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "nacos"

  nacos {
    serverAddr = "127.0.0.1:8849"
    namespace = "4dad9a92-3a8e-4456-992f-03433d9320ad"
    group = "SEATA_GROUP"
    username = "nacos"
    password = "nacos"
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file.conf"
  }
}

```



# FQA

## Mysql连接配置问题

```shell
CONVERT_TO_NULL


JDBC driver for MySQL连接提示"The connection property 'zeroDateTimeBehavior' acceptable values are: 'CONVERT_TO_NULL', 'EXCEPTION' or 'ROUND'. The value 'convertToNull' is not acceptable."解决方案
1、使用了8.0以上版本的JDBC driver for MySQL，降回5.x版本。

2、不对JDBC driver for MySQL降级。修改数据库连接字符串，添加serverTimezone属性。例如：

zeroDateTimeBehavior=convertToNull&serverTimezone=UTC
```

## 版本问题

```
nested exception is io.seata.common.exception.FrameworkException: No available service

no available service 'default' found, please make sure registry config correct

lsof -i:9999
```

## 注册到nacos非常慢

暂未解决





**1.xid未传递** 

一般常见于springcloud的用户,请注意是否只引入了seata-all或者seata-spring-boot-starter.如果是,请换为本文介绍给springcloud的依赖即可.

如果是自己实现了WebMvcConfigurer,那么请参考com.alibaba.cloud.seata.web.SeataHandlerInterceptorConfiguration#addInterceptors把xid传递拦截器加入到你的拦截链路中

**2.数据源未代理**

一般分为2种情况

数据源自己创建后,代理数据源的beanname为DataSourceProxy而不是dataSource,导致sqlsessionfactory注入的并不是被代理后的.

如果是已经开启了自动代理的用户,请确认是否手写了sqlsessionfactory此时注入的DataSource并未显示是DataSourceproxy代理的情况,请进行调整,保证注入是代理的数据源.

**3.事务分组配置出错导致无法找到tc**

一般由于对事务分组的理解出现偏差导致的,请仔细阅读官网的参数配置中的介绍.

TM端: seata.tx-service-group=自定分组名 seata.service.vgroup-mapping(配置中心中是叫:service.vgroupMapping).自定分组名=服务端注册中心配置的cluster/application的值

拿nacos举例子

比如我server中nacos的cluster

```
  nacos {
    application = "seata-server"
    serverAddr = "localhost"
    namespace = ""
    cluster = "testCluster"
    username = "nacos"
    password = "nacos"
  }
```

我的事务分组为

```
seata:
  tx-service-group: test
```

那么nacos中需要配置test事务分组

```
service.vgroupMapping.test=testCluster
```

**4.Global lock acquire failed**

一般这种情况也是分为两种

常见的就是并没有对select语句进行forupdate,如果你不开启forupdate,seata默认是遇到并发性竞争锁并不会去尝试重试,导致拿不到lock当前事务进行回滚.不影响一致性,如果你想没forupdate也开启竞争锁client.rm.lock.retryPolicyBranchRollbackOnConflict设置为false(有死锁风险)

还有一种就是由于大并发下对同一个数据并发操作时,前一个事务还未提交,后续的事务一直在等待,而seata默认的超时周期为300ms,此时超时后就会抛出Global lock acquire failed,当前事务进行回滚,如果你想保住竞争锁的周期足够长,请更改client.rm.lock.retryTimes和client.rm.lock.retryInterval来保证周期的合理性.

**5.Could not found global transaction xid**

一般出现这个提示与服务重试及超时有关,比如A->B此时A调B超时,A默认是重试的,B等于被调了2遍,第二次被调用的B进行了响应,A发起者接收到结果后进行了提交/回滚,这次超时的B因为网络问题现在才被调用,他也收到了一样的全局事务id,进行业务处理,直到注册分支,此时全局事务已经被提交/回滚,导致当前超时的分支事务B无法注册上.

这种问题一般保证你的业务不会去超时重试,如果你需要,请确认全局事务状态,做好幂等,反正已经做过的处理重复操作



