### 1.安装Metricbeat

前提，安装好ES和Kibana先。也可以发送到kafka由logstach切割入库ES。

###### 下载

```shell
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.6.2-linux-x86_64.tar.gz
```

###### 解压

```shell
tar xzvf metricbeat-6.6.2-linux-x86_64.tar.gz
```

###### 启动

```shell
./metricbeat -e -c metricbeat.yml
```



###### 配置

