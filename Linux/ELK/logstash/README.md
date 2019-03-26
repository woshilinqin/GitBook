###### 1.下载

[官方文档](https://www.elastic.co/guide/en/logstash/current/advanced-pipeline.html)

下载解压即可。

###### 2.介绍

logstash配置包括3个部分：input输入部分、filter过滤部分、output输出部分。

output部分查看日志：

```shell
output {
    stdout { codec => rubydebug }
}
```

启动

```shell
bin/logstash -r
```

常见参数

> -w # 指定线程,默认是 cpu 核数 
> -f # 指定配置文件 
> -t # 测试配置文件是否正常 
> -r # 热加载配置文件（往往配合管道进行控制）

管道配置文件`pipelines.yml`

```shell
- pipeline.id: my-pipeline_1
  path.config: "/etc/path/to/p1.config"
  pipeline.workers: 3
```

使用管道要注意：如果所有数据都是一个kafka队列的，每个配置有一个input，是否会存在重复数据的问题。解决方案，把input抽出来即可。例如新建一个global.conf

```shell
input { ... }
```

