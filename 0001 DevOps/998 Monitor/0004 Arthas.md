# Arthas

```ini
arthas-tunnel-server-3.5.2-fatjar.jar



arthas-boot.jar
```





# Web Console

## arthas-boot

```shell
java -jar arthas-boot
# attach之后
		## arthas只listen 127.0.0.1
# 访问ip：http://127.0.0.1:3658/
# 没有AgentId
```

## arthas tunnel server

```shell
java -jar  arthas-tunnel-server.jar

http://127.0.0.1:8080/

http://127.0.0.1:8080/actuator/arthas

as.sh --tunnel-server 'ws://127.0.0.1:7777/ws'

# 
java -jar  arthas-tunnel-server.jar --tunnel-server 'ws://127.0.0.1:7777/ws'

# --telnet-port 9999 指定暴露ip
$ java -jar arthas-boot.jar --tunnel-server 'ws://127.0.0.1:7777/ws' --http-port -1 --telnet-port 9999



```

