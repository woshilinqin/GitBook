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
InputStream is = ss.getInputStream();

int len = -1;//初始值，起标志位作用
byte buf[] = new byte[128];//缓冲区
ByteArrayOutputStream baos = new ByteArrayOutputStream();
while ((len = is.read(buf)) != -1) {//循环读取内容,将输入流的内容放进缓冲区中
    baos.write(buf, 0, len);//将缓冲区内容写进输出流，0是从起始偏移量，len是指定的字符个数
}

String result = new String(baos.toByteArray());
```

