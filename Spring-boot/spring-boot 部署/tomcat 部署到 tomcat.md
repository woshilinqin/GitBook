###### 一、修改打包形式

在 pom.xml 里设置

```xml
<packaging>war</packaging>
```

###### 二、移除嵌入式tomcat插件

在 pom.xml 里找到 spring-boot-starter-web 依赖节点，在其中添加如下代码，

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- 移除嵌入式tomcat插件 -->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

###### 三、添加servlet-api的依赖

下面两种方式都可以，任选其一

```xml
// 1
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
// 2
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>tomcat-servlet-api</artifactId>
    <version>8.0.36</version>
    <scope>provided</scope>
</dependency>
```

###### 四、修改启动类，并重写初始化方法

我们平常用main方法启动的方式，都有一个App的启动类，代码如下：

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

我们需要类似于web.xml的配置方式来启动spring上下文了，在Application类的同级添加一个SpringBootStartApplication类，其代码如下:

```java
/**
  * 修改启动类，继承 SpringBootServletInitializer 并重写 configure 方法
  */
  @SpringBootApplication
  public class Application extends SpringBootServletInitializer {

  @Override
  protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
  	return builder.sources(Application.class);
  }

  public static void main(String[] args) {
  	SpringApplication.run(Application.class, args);
  }
}
```

发现：只是简单的继承一下就可以了，重写部分删除。

###### 五、打包部署

配置文件：

```properties
#发布路径
server.context-path=/spring-boot1 
server.port= 8080
```

maven 打包配置


```xml
<build>
	<finalName>spring-boot1</finalName>[打包项目名]
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

在项目根目录下（即包含 pom.xml 的目录），在命令行里输入： 
mvn clean package 即可， 等待打包完成，出现 [INFO] BUILD SUCCESS 即为打包成功。 
然后把 target 目录下的 war 包放到 tomcat 的webapps 目录下，启动 tomcat，即可自动解压部署。 
最后在浏览器中输入

http://localhost:[端口号]/[打包项目名]/

发布成功