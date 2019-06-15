### 快速开始

###### 1.maven依赖

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>

        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.5</version>
            <configuration>
                <configurationFile>${basedir}/src/main/resources/generatorConfig.xml</configurationFile>
                <verbose>true</verbose>
                <overwrite>true</overwrite>
            </configuration>
            <dependencies>
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>5.1.21</version>
                </dependency>
            </dependencies>
        </plugin>

    </plugins>
</build>
```

maven 插件内指定了 mysql 依赖后，generator.properties 中的驱动 url 可以不写，不然填写自己系统的驱动路径。

###### 2.新建 generatorConfig.xml

这个文件的路径对应 maven 插件内的`<configurationFile>`标签。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--导入生成的数据源配置-->
    <properties resource="generator.properties"></properties>

    <!-- 数据库驱动:选择你的本地硬盘上面的数据库驱动包-->
    <!--<classPathEntry location="${jdbc.driverLocation}"/>-->

    <context id="default" targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="${jdbc.driverClass}" connectionURL="${jdbc.url}" userId="${jdbc.username}"
                        password="${jdbc.password}">
        </jdbcConnection>

        <!--非必须，类型处理器，用于java jdbcType和数据库直接的转化控制-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成模型的                    包名          和                          包位置-->
        <javaModelGenerator targetPackage="com.pojo" targetProject="src/main/java">
            <!--是否增加构造函数-->
            <property name="constructorBased" value="true"></property>
            <!-- enableSubPackages:是否让schema作为包的后缀,就是增加一个schemaName的包-->
            <property name="enableSubPackages" value="true"/>
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- 生成xml映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.dao" targetProject="src/main/java">

            <property name="enableSubPackages" value="true"/>

            <!--daoMapper接口要继承的类-->
            <property name="rootInterface" value=""></property>
        </javaClientGenerator>

        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="student" domainObjectName="Student" enableCountByExample="false"
               enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false"
               selectByExampleQueryId="false"></table>

    </context>
</generatorConfiguration>

```

###### 3.generator.properties

记录数据库用户名密码信息

```properties
jdbc.url=jdbc:mysql://localhost:3306/spring-boot-jpa
jdbc.username=root
jdbc.password=root
jdbc.driverClass=com.mysql.jdbc.Driver
#加了插件依赖，这个可以不要了。
jdbc.driverLocation=F:/.m2/repository/mysql-connector-java/mysql-connector-java/5.1.7/mysql-connector-java-5.1.7-bin.jar
```

###### 4.生成

- 直接 maven 插件生成。
  ![mybatis-generator](https://i.loli.net/2019/06/15/5d0488d0631e077517.jpg)

- 建立 maven 运行模式
  直接执行 maven 命令 mybatis-generator:generate -e
  或则生成一个可执行配置
  ![Run/Debug](https://i.loli.net/2019/06/15/5d0488d09ffae14480.jpg)



### 常见问题：

###### 1.MyBatis逆向工程自动生成的xml配置文件片段出现*WithBLOBsjava代码

这是因为生成插件默认会分开大文本字段，如果需要合起来，可以在`generatorConfig.xml`生成 table 时指定格式类型。

```xml
<table tableName="law_index_tag" domainObjectName="LawIndexTag">
    <columnOverride column="title" javaType="java.lang.String" jdbcType="VARCHAR" />
    <columnOverride column="content" javaType="java.lang.String" jdbcType="VARCHAR" /> 
</table>
```

###### 2.多次生成问题

多次生成，java文件会直接被覆盖掉，但是xml是追加。所以添加字段的情况下，先删除 xml，值执行。

规范：一般生成的文件不会执行修改