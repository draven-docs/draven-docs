# PulsarMQ

```shell
docker run -it \
  -p 6650:6650 \
  -p 8080:8080 \
  --mount source=pulsardata,target=/pulsar/data \
  --mount source=pulsarconf,target=/pulsar/conf \
  apachepulsar/pulsar:2.7.1 \
  bin/pulsar standalone
  
 docker run -it \
  -p 6650:6650 \
  -p 8080:8080 \
  apachepulsar/pulsar:2.7.1 \
  bin/pulsar standalone
  
  
  
  
docker run -it     -p 9527:9527 -p 7750:7750     -e SPRING_CONFIGURATION_FILE=/pulsar-manager/pulsar-manager/application.properties     apachepulsar/pulsar-manager:v0.2.0




curl    -H 'X-XSRF-TOKEN: $CSRF_TOKEN'    -H 'Cookie: XSRF-TOKEN=$CSRF_TOKEN;'    -H "Content-Type: application/json"    -X PUT http://localhost:7750/pulsar-manager/users/superuser    -d '{"name": "admin", "password": "apachepulsar", "description": "test", "email": "username@test.org"}'
```