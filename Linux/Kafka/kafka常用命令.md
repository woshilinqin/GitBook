## 常用命令

###### 查看日志组列表

```shell
bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server 127.0.0.1:9092 --list
```

###### 查看堆积

```shell
./bin/kafka-consumer-groups.sh --bootstrap-server 192.168.13.25:9092 --group ivory --describe
```



###### 动态修改队列数据保存时间ms（不用重启）默认7天 604800000

```shell
 ./bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 -topic test --alter --config retention.ms=1000
```

修改后数据就保存时间短了。不会消费之前的数据。但是查看堆积还是有那么多数据量的。



###### producer:

```shell
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```



###### comsumer:

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```



###### 删除topic

```shell
./bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic test
```



###### 创建topic：或者直接查看，其他参数就默认。也会新建

```shell
.bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```



###### 查看topic

```shell
bin/kafka-topics.sh --list --zookeeper localhost:2181
```



###### 启动：

```shell
./bin/kafka-server-start.sh config/server.properties
cd /root/linqin;nohup sh ./bin/kafka-server-start.sh ./config/server.properties > kafka.log & 2>&1
```



###### 停止：

```shell
./bin/kafka-server-stop.sh ./config/server.properties 
```



###### 集群配置：

```shell
config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dirs=/tmp/kafka-logs-1

config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dirs=/tmp/kafka-logs-2
```

