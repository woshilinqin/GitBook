###### 1.线上linux性能排查

查内存

```shell
free -m
```

查负载 `M`按内存排序，`P`按使用率排序

```
top
```

查磁盘

```shell
df -h
```

###### 2.得到负载最高的进程`PID`

使用命令查看子线程负载情况

```shell
top -Hp PID
```

获取线程最高负载的线程`pid`，转化为16进制 xxxx，对应堆栈打印的应该就是`tid=0x00xx`

```shell
printf "%x\n" pid
00xx
```

###### 3.使用`jstack`查看堆栈信息

打印对应堆栈信息关键字后20行，`-l`表示打印额外的锁信息。

```shell
jstack -l pid | grep '00xx' -A 20 >> dump
```

