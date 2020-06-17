# docker安装

# Docker安装Oracle

### 搜索镜像

```bash
$ docker search oralce
```

选择 `alexeiled/docker-oracle-xe-11g` 镜像

### 拉取镜像

```bash
$ docke pull alexeiled/docker-oracle-xe-11g
```

### 启动镜像

```bash
$ docker run -d -p 49161:1521 -e ORACLE_ALLOW_REMOTE=true  alexeiled/docker-oracle-xe-11g
```

上面命令中 `-e ORACLE_ALLOW_REMOTE=true` 的作用是开启远程连接，如果只是本地使用可以写成这样

```bash
$ docker run -d -p 49161:1521 alexeiled/docker-oracle-xe-11g
```

```
docker run -d -v /data/kongchao/docker_volume/oracle_data:/data/oracle_data -p 49160:22 -p 49161:1521 -e ORACLE_ALLOW_REMOTE=true alexeiled/oracle-xe-11g
```

### 远程链接镜像

连接信息：

- 连接类型：Basic
- 主机地址：ip
- 端口：49161
- DIS：xe
- 用户名：system
- 密码：oracle




# 连接工具

https://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.htmlcccvc