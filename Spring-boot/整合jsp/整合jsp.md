##### 1.增加依赖

```xml
 <!--servlet的支持-->
 <dependency>
     <groupId>javax.servlet</groupId>
     <artifactId>javax.servlet-api</artifactId>
 </dependency>

 <!-- 开启对jsp的支持 -->
 <dependency>
     <groupId>org.apache.tomcat.embed</groupId>
     <artifactId>tomcat-embed-jasper</artifactId>
 </dependency>
 <!--jstl-->
<dependency>
     <groupId>javax.servlet</groupId>
     <artifactId>jstl</artifactId>
 </dependency>
 <!-- Tomcat的支持 -->
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-tomcat</artifactId>
     <!--  <scope>provided</scope>-->
 </dependency>
```



##### 2.application.properties

```properties
spring.mvc.view.prefix:WEB-INF/jsp/
spring.mvc.view.suffix:.jsp
```



##### 3.新建文件夹

![1549090961408](https://i.loli.net/2019/06/15/5d0488e2a92af10547.jpg)



##### 4.创建IndexController

```java
/**
 * Description:
 * author: LinQin
 * date: 2018/07/03
 */
@Controller
public class Index {

    @RequestMapping("/jsp")
    public String index() {
        System.out.println("进入到jsp");
        return "index";
    }
}
```



##### 5.启动项目

点击run

页面访问：http://localhost:8080/jsp



![1549093333322](https://i.loli.net/2019/06/15/5d0488e2e110742481.jpg)



异常情况：有时我们创建项目是使用model创建的。正常启动还是无法访问到jsp页面。如果main方法所在的项目是maven的子模块项目（如 Main-Parent项目中，包含maven-A子模块，正好main方法就在maven-A 模块中）则main方法运行，无法访问jsp。

![1549093522934](https://i.loli.net/2019/06/15/5d0488e32840e79057.jpg)

解决方法：

1. 设置idea的工作路径。

   ![1549093884246](https://i.loli.net/2019/06/15/5d0488e39281237886.jpg)

2. 使用maven的插件启动

![1549093962474](https://i.loli.net/2019/06/15/5d0488e408abc60075.jpg)



###### 打 jar 包 maven 配置

```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <!--其他版本的报bug-->
                <version>1.4.2.RELEASE</version>
            </plugin>
        </plugins>

        <finalName>spring-boot</finalName>
        <resources>
            <!-- 打包时将jsp文件拷贝到META-INF目录下-->
            <resource>
                <!-- 指定resources插件处理哪个目录下的资源文件 -->
                <directory>${basedir}/src/main/webapp</directory>
                <!--注意此次必须要放在此目录下才能被访问到-->
                <targetPath>META-INF/resources</targetPath>
                <includes>
                    <include>**/**</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <!--关闭过滤-->
                <filtering>false</filtering>
                <includes>
                    <include>**/**</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <excludes>
                    <exclude>**/*.java</exclude>
                </excludes>
            </resource>
        </resources>
    </build>
```