### Spring-boot 整合 Mybaits

###### 1.依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--<dependency>-->
            <!--<groupId>org.mybatis.spring.boot</groupId>-->
            <!--<artifactId>mybatis-spring-boot-starter</artifactId>-->
            <!--<version>2.0.1</version>-->
        <!--</dependency>-->

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- 引入了这个依赖，不再需要mybatis-spring、MyBatis 依赖 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.1.0</version>
        </dependency>
        <!-- 代码生成 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.1.0</version>
        </dependency>
        <!-- 生成模板 -->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.1</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```

这里使用的是 mybatis-plus 进行代码生成，引入 mybatis-plus-boot-starter 依赖后无需引用 mybatis-spring、MyBatis 依赖。

###### 2.数据库 schema

```sql
DROP TABLE IF EXISTS `typehandle`;

CREATE TABLE `typehandle` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `name` varchar(30) DEFAULT '""' COMMENT 'name',
  `sex` tinyint(4) DEFAULT NULL COMMENT '性别',
  `config` text COMMENT '配置，保存json格式',
  `age` int(11) DEFAULT NULL COMMENT '年龄',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4;
```

表数据类型如下：

|  id  | name | sex  | config  | age  |
| :--: | :--: | :--: | :-----: | :--: |
|  1   | lin  |  1   | “json”  |  19  |
|  2   | blin |  0   | “json2” |  24  |

###### 3.生成代码

- **Mapper**

  ```java
  public interface TypehandleMapper extends BaseMapper<Typehandle> {
      Typehandle getWithId(@Param("id") Integer id);
  }
  ```

- **entity**

  ```java
  @Data
  @Accessors(chain = true)
  @NoArgsConstructor
  @AllArgsConstructor
  public class Typehandle extends Model<Typehandle> {
  
      private static final long serialVersionUID = 1L;
  
      /**
       * id
       */
      @TableId(value = "id", type = IdType.AUTO)
      private Long id;
  
      /**
       * name
       */
      private String name;
  
      /**
       * 性别
       */
      private Sex sex;
  
      /**
       * 配置，保存json格式
       */
      private String config;
  
      /**
       * 年龄
       */
      private Integer age;
      
  }
  ```

- **service 和 Impl**

  ```java
  public interface TypehandleService extends IService<Typehandle> {
  }
  
  @Service
  public class TypehandleServiceImpl extends ServiceImpl<TypehandleMapper, Typehandle> implements TypehandleService {
  
  }
  ```

- **Mapper.xml**

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.dao.TypehandleMapper">
  
      <!-- 通用查询映射结果 -->
      <resultMap id="BaseResultMap" type="com.entity.Typehandle">
          <id column="id" property="id" />
          <result column="name" property="name" />
          <result column="sex" property="sex" />
          <result column="config" property="config" />
          <result column="age" property="age" />
      </resultMap>
  
      <!-- 通用查询结果列 -->
      <sql id="Base_Column_List">
          id, name, sex, config, age
      </sql>
  
      <select id="getWithId" parameterType="INTEGER" resultMap="BaseResultMap">
          select * from typehandle where id = #{id}
      </select>
  </mapper>
  ```

###### 4.配置

启动类：添加 Mapper 扫描 `@MapperScan("com.dao")`

```java
@SpringBootApplication
@MapperScan("com.dao")
public class SpringBootMybatisTypehandlerApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootMybatisTypehandlerApplication.class, args);
    }
}
```

yaml 配置：

其中 springboot 2.0 后默认使用的数据库连接池为 `HikariCP` ，其次配置 mybatis-plu 的选项，基本和 mybatis 的差不多，`type-aliases-package`表示包名可以简写，`mapper-locations`表示 XML 文件的位置。

ps：如果加载的是其他 jar 内的 XML 文件，这里路径为 `classpath*`

```yaml
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
logging:
  level:
    com:
      dao: debug


#mybatis:
#  configuration:
#  mapper-locations: classpath:mybatis/*/*.xml
#  type-aliases-package: com.entity

mybatis-plus:
  type-aliases-package: com.entity
  mapper-locations: classpath:mybatis/*/*.xml
```

###### 5.测试控制器

```java
@RestController
@RequestMapping("/")
public class TypehandleController {
    @Autowired
    private TypehandleService typehandleService;

    @Autowired
    private TypehandleMapper typehandleMapper;

    @RequestMapping("/insert")
    public void test() {
        Typehandle typehandle = new Typehandle();
        typehandle.setAge(24).setConfig("json").setName("blin").setSex(0);
        typehandleService.save(typehandle);
    }

    @RequestMapping("/select")
    public void select() {
        Typehandle byId = typehandleMapper.getWithId(2);
        System.out.println(byId);
    }
}
```

页面访问：http://localhost:8080/insert