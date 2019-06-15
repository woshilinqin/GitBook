

#### 1.下载

[下载二进制包](http://kafka.apache.org/downloads)

![下载二进制包](https://i.loli.net/2019/06/13/5d0235292fb1430282.jpg)

#### 2.解压

> tar -zxvf kafka_2.11-2.1.1.tgz



#### 3.配置

基本上都使用默认，修改一下kafka自身的地址，zk连接的地址即可。

> listeners=PLAINTEXT://:9092
>
> zookeeper.connect=localhost:2181



#### 4.使用默认的zk

ps：最好不要用kafka自带的，可以先搭建好zookeeper集群

启动：

```shell
./bin/zookeeper-server-start.sh config/zookeeper.properties & 
```