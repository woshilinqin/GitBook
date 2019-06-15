## zookeeper的安装使用

#### 1.下载zookeeper

[点击下载](https://archive.apache.org/dist/zookeeper/zookeeper-3.3.6/zookeeper-3.3.6.tar.gz)

#### 2.解压修改配置

我们进入到conf文件夹下面，将zoo_sample.cfg这个文件复制为zoo.cfg，必须是这个名字
加上配置：

```shell
dataDir=/tmp/zookeeper
dataLogDir=/tmp/zookeeper/log
```

前者表示数据文件，后者是日志，也可以修改一下默认端口。

![zoo.cfg配置](https://i.loli.net/2019/06/13/5d02367b3fc7c75630.jpg)

**添加环境变量**：

> `vim /etc/profile`

![环境变量](https://i.loli.net/2019/06/13/5d02367b6e58048762.jpg)

> 重载：source /etc/profile
> 查看：echo $PATH

**启动**：bin目录下

>   启动   ./zkServer.sh start
>   看状态 ./zkServer.sh status
>   看日志 cat zookeeper.out
>   启动客户端：./zkCli.sh	



#### 集群配置：

如果你是集群的话，请在clientPort下面添加服务器的ip。如
集群配置：在zookeeper目录下，创建data目录，对应`dataDir`配置。在3个zookeeper节点中data目录下分别创建mypid[^1]

[^1]: echo 1 >/tmp/mypid

文件，并分别添加内容0、1、2等。识别哪个机器

```shell
server.1=192.168.180.132:2888:3888
server.2=192.168.180.133:2888:3888
...
```

> server.A=B：C：D：其中 A 是一个数字，表示这个是第几号服务器；B 是这个服务器的 ip 地址；C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口