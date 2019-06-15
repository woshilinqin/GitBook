### linux常用命令：

###### 远程下载文件：`wget`

```shell
wget [url] -P [下载目录]
```
###### curl

-L 重定向发送请求

-O 使用URL中默认的文件名保存文件到本地

-o 下载，需要指定下载文件名

-i 显示全部信息（头信息，返回内容）

```shell
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.6.2-linux-x86_64.tar.gz
```

```
curl -L -o xxx.tar.gz https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.6.2-linux-x86_64.tar.gz
```



###### post发送xml

```shell
curl -X POST -H 'Content-type:application/xml' -d @/tmp/data.xml https://apitest.mch.weixin.qq.com/sandboxnew/pay/getsignkey
```

-d :--data参数后面 @为路径
-X :指定什么类型请求get/post/put。。。
-H :--hearder增加头信息 

![xml信息](https://i.loli.net/2019/06/13/5d0235295e4fe33478.jpg)

###### post发送json

```shell
curl -i -X POST -H 'Content-type':'application/json' -d '{json}' http://wx.sp-hicard.com/publicCount/menu/messageNotify
```

###### 两个主机传输文件(需要密码)scp

```shell
scp -P 22 -r tomcat7 root@192.168.1.110:/home
```

两个机器都要支持scp命令
-P是 对面机器的ssh端口
-r是目录 
root对方用户@ip:/目录
其实不管文件或者目录加 -r 都可以

###### md5sum 

生成md5字符串，比较内容的

###### 查看负载

`w`、`uptime`、`top`

top -i 实时显示进程
top
    P：按%CPU使用率排行
　T：按MITE+排行
　M：按%MEM排行

###### 查看机器几核

```shell
cat /proc/cpuinfo |grep "cores"|uniq
```

###### 查看内存

free -m

free -g

###### 查看文件大小

> du -sh *



###### 同步系统时间

> yum install ntpdate
> ntpdate -u ntp.api.bz  上海时间

###### yum

`-y`表示免确认

```shell
yum install -y zip unzip
```

###### rpm

搜索 jdk 的安装包。

```shell
rpm -qa | grep jdk
```

卸载原来的jdk， rpm -e --nodeps +名称（`--nodeps`强制 `-e`卸载）

```shell
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.99-2.6.5.1.el6.i686
```

安装，注意版本位数。

```shell
rpm -ivh /home/jdk-8u144-linux-x64.rpm
```

```shell
rpm -ivh xxxxx --nodeps --force              不检查依赖关系，强制安装
```

```shell
rpm --force -ivh your-package.rpm  		     强制安装：安装出现...conflict with ... 有冲突可以加上
```