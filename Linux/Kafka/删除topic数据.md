## 删除topic数据



基于kafka2.0版本。

#### 1.命令删除topic

```shell
./bin/kafka-topics  --delete --zookeeper 【zookeeper server】  --topic 【topic name】
```

> [2019-02-25 15:52:00,070] WARN Connected to an old server; r-o mode will be unavailable (org.apache.zookeeper.ClientCnxnSocket)
> Topic test is marked for deletion.
> Note: This will have no impact if delete.topic.enable is not set to true.

提示删除给标记了。要设置参数`delete.topic.enable`才可以删除。一般都不设置。这时，磁盘还是没有释放空间的。

> - 现在默认启用主题删除，因为该功能现在已稳定。希望保留先前行为的用户应将代理配置设置`delete.topic.enable`为`false`。请记住，删除主题会删除数据并且操作不可逆（即没有“取消删除”操作）

新版都默认删除了。可以设置为false不删除。



#### 2.直接删除磁盘

直接查看`server.properties`的日志记录目录。

默认：`log.dirs=/tmp/kafka-logs`

直接清空这个目录。

**缺点：**会删除全部的topic。