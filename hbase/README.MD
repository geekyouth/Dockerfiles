# 1、启动 hbase 的方式（二选一）：

## 1.1、docker image 方式
此方式运行时，不要修改端口号映射规则，并且 Java/scala 项目需要如下配置，来映射 hostname 和 ip 关系，可以省去修改 hosts 文件：  

```xml
<dependency>
   <groupId>io.leopard</groupId>
    <artifactId>javahost</artifactId>
    <version>0.9.6</version>
</dependency>
```

```java
import io.leopard.javahost.JavaHost

// 代码放在靠前的位置执行
JavaHost.updateVirtualDns("hbase", "127.0.0.1");
```

```shell
docker run -d --name hbase --hostname hbase -p 2181:2181 -p 8080:8080 -p 8085:8085 -p 9090:9090 -p 9095:9095 -p 16000:16000 -p 16010:16010 -p 16020:16020 -p 16030:16030 geekyouth/hbase:2.3.3.1
```

## 1.2、docker-compose 方式

hbase-compose.yaml
```yaml
version: "3.3"
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:5.5.0
    hostname: zookeeper
    container_name: zookeeper
    ports:
      - 2181:2181
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  hbase:
    image: geekyouth/hbase:2.3.3.1
    hostname: hbase
    container_name: hbase
    depends_on:
      - zookeeper
    ports:
      - 8080:8080
      - 8085:8085
      - 9090:9090
      - 9095:9095
      - 16000:16000
      - 16010:16010
      - 16020:16020
      - 16030:16030
    environment:
      ZK_SERVER: zookeeper:2181
```
启动方式：
```shell
# 后台启动
docker-compose -f ./hbase-compose.yaml up -d
# 停止并删除 container
docker-compose -f ./hbase-compose.yaml kill
docker-compose -f ./hbase-compose.yaml rm -f
```

同样也需要在项目中配置如下：
```xml
<dependency>
   <groupId>io.leopard</groupId>
    <artifactId>javahost</artifactId>
    <version>0.9.6</version>
</dependency>
```

```java
import io.leopard.javahost.JavaHost
// 代码放在靠前的位置执行
JavaHost.updateVirtualDns("hbase", "127.0.0.1");
JavaHost.updateVirtualDns("zookeeper", "127.0.0.1");
```

