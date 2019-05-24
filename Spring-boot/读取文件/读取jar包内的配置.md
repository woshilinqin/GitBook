项目结构

![项目结构](assets/1554094154356.png)

`ResourceUtils`读取的方式只能读到jar包的目录充当classpath。

```java
ResourceUtils.getURL("classpath").getPath()；
```

jar包放到`/data`目录下，对应的结果。上述方式只能适用于`tomcat`的项目。下面是上面代码打印的结果。
> /data/classpath

代码实现读取

```java
// 可以直接读取jar包内的文件。
ClassPathResource ss = new ClassPathResource("banner.txt");
//InputStream is = ss.getInputStream();
InputStream is = HelloWordController.class.getClassLoader().getResourceAsStream("banner.txt");
```

