## Generator

我们将通过一个简单的 Demo 来阐述 MyBatis-Plus 的强大功能，在此之前，我们假设您已经：


现有一张 `User` 表，其表结构如下：

| id   | name   | age  | config             | sex  |
| ---- | ------ | ---- | ------------------ | ---- |
| 1    | Jone   | 18   | test1@baomidou.com | 1    |
| 2    | Jack   | 20   | test2@baomidou.com | 1    |
| 3    | Tom    | 28   | test3@baomidou.com | 1    |
| 4    | Sandy  | 21   | test4@baomidou.com | 0    |
| 5    | Billie | 24   | test5@baomidou.com | 0    |

其对应的数据库 Schema 脚本如下：

```sql
DROP TABLE IF EXISTS `typehandle`;

CREATE TABLE `typehandle` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `name` varchar(30) DEFAULT '""' COMMENT 'name',
  `sex` tinyint(1) DEFAULT NULL COMMENT '性别',
  `config` text COMMENT '配置，保存json格式',
  `age` int(11) DEFAULT NULL COMMENT '年龄',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## [#](https://mp.baomidou.com/guide/quick-start.html#%E5%88%9D%E5%A7%8B%E5%8C%96%E5%B7%A5%E7%A8%8B)初始化工程

创建一个空的 Spring Boot 工程（工程将以 Mysql 作为默认数据库进行演示）

TIP

可以使用 [Spring Initializr](https://start.spring.io/) 快速初始化一个 Spring Boot 工程

## [#](https://mp.baomidou.com/guide/quick-start.html#%E6%B7%BB%E5%8A%A0%E4%BE%9D%E8%B5%96)添加依赖

引入 Spring Boot Starter 父工程：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.4.RELEASE</version>
    <relativePath/>
</parent>
```

引入 `spring-boot-starter`、`spring-boot-starter-test`、`mybatis-plus-boot-starter`、`lombok`、`Mysql` 依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    
    <!-- 引入了这个依赖，不再需要mybatis-spring、MyBatis 依赖 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.1.0</version>
        </dependency>

        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.1.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.1</version>
        </dependency>
</dependencies>
```

## [#](https://mp.baomidou.com/guide/quick-start.html#%E9%85%8D%E7%BD%AE)配置

在 `application.yml` 配置文件中添加 Mysql 数据库的相关配置：

```yaml
# DataSource Config
spring:
  datasource:
    password: root
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8
    username: root
    hikari:
      connection-timeout: 30000
      auto-commit: true
      max-lifetime: 1800000
      pool-name: DatebookHikariCP
      minimum-idle: 5
      connection-test-query: SELECT 1
      idle-timeout: 30000
      maximum-pool-size: 15
mybatis:
  configuration:
    map-underscore-to-camel-case: true
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.pojo
logging:
  level:
    com:
      dao:
        UserMapper: debug

```

在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹：

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(QuickStartApplication.class, args);
    }

}
```

## 代码生成

编写实体类 `CodeGenerator.java`

```java
import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class CodeGenerator {
    public static void main(String[] args) {
        AutoGenerator mpg = new AutoGenerator();

        // 配置数据源
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8");
        dataSourceConfig.setDriverName("com.mysql.jdbc.Driver");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("root");

        // 设置一些全局的配置：
        GlobalConfig gc = new GlobalConfig();
//        System.getProperty("user.dir") project地址，我这是模块路径
        gc.setOutputDir(System.getProperty("user.dir")  + "/spring-boot-mybatis-typehandler/src/main/java");
        gc.setFileOverride(true);
        gc.setActiveRecord(true);
        gc.setEnableCache(false);// XML 二级缓存
        gc.setBaseResultMap(true);// XML ResultMap
        gc.setBaseColumnList(true);// XML columList
        gc.setAuthor("linqin");
        gc.setMapperName("%sMapper");
        gc.setXmlName("%sMapper");
        gc.setServiceName("%sService");
        gc.setServiceImplName("%sServiceImpl");
        gc.setControllerName("%sController");

        StrategyConfig strategy = new StrategyConfig();
        //strategy.setTablePrefix(new String[] { "SYS_" });// 此处可以修改为您的表前缀
        strategy.setNaming(NamingStrategy.underline_to_camel);// 表名生成策略
        strategy.setInclude(new String[]{"typehandle"}); // 需要生成的表

//        xml文件配置
        PackageConfig pc = new PackageConfig();
        pc.setParent("com");
        pc.setController("controller");
        pc.setService("service");
        pc.setServiceImpl("service.impl");
        pc.setEntity("entity");
        pc.setMapper("dao");

        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                Map<String, Object> map = new HashMap<String, Object>();
                map.put("abc", this.getConfig().getGlobalConfig().getAuthor() + "-rb");
                this.setMap(map);
            }
        };

        //xml生成路径
        List<FileOutConfig> focList = new ArrayList<>();
        focList.add(new FileOutConfig("/templates/mapper.xml.vm") {
            @Override
            public String outputFile(TableInfo tableInfo) {
                return System.getProperty("user.dir") + "/spring-boot-mybatis-typehandler/src/main/resources/" + "/mybatis/tables/" + tableInfo.getEntityName() + "Mapper.xml";
            }
        });
        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        // 关闭默认 xml 生成，调整生成 至 根目录
        TemplateConfig tc = new TemplateConfig();
        tc.setXml(null);

        mpg.setDataSource(dataSourceConfig);			//数据源配置
        mpg.setGlobalConfig(gc);		//全局配置
        mpg.setStrategy(strategy);		//生成策略配置
        mpg.setPackageInfo(pc);			//包配置
        mpg.setCfg(cfg);				//xml配置
        mpg.setTemplate(tc);			//
        // 执行生成
        mpg.execute();

    }
}

```

直接 `Run` 运行即可，目录下会生成代码以及 XML。如果修改了数据库再次生成，会重新生成并且覆盖 java 文件和 XML 文件。一般生成到指定目录复制过来。