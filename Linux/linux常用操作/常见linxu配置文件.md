### 常见linux系统配置文件

###### 1. /etc/rsyslog.conf 

转发数据到 tcp、udp 端口，`@@`代表tcp，`@`代表udp

```shell
*.* @@192.168.1.1:514
*.*  @192.168.1.1:515
```
重启`rsyslog`服务

```shell
systemctl restart rsyslog	
```

nc 端口发送数据测试

```shell
nc 192.168.1.1 514
```

###### 2. /etc/profile  

环境变量



###### 3. /proc/meminfo  内存信息

查看总内存：grep MemTotal /proc/meminfo 

查看内存剩余：grep MemFree /proc/meminfo



###### 4. /proc/cpuinfo cpu信息

查看位数： getconf LONG_BIT

查看核心数： cat /proc/cpuinfo |grep "cores"|uniq

查看内核 / 操作系统 / CPU信息：uname -a

> Linux `localhost.localdomain` 3.10.0-693.el7.x86_`64` #1 SMP Tue Aug 22 21:09:27 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux



