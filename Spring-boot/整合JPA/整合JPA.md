##### 1.导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



##### 2.application.properties增加

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/spring-boot-jpa
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

#create：每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
#create-drop：每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
#update：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
#validate：每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。
#spring.jpa.properties.hibernate.hbm2ddl.auto=create-drop （过时了）
#配置jpa
spring.jpa.hibernate.ddl-auto=create
spring.jpa.show-sql=true
spring.jackson.serialization.indent_output=true
```



##### 3.声明实体对象

```java
@Entity
public class Account {
    @Id
    @GeneratedValue
    private int id ;
    private String name ;
    private double money;
...
```



##### 4.声明dao接口继承jpa

```java
public interface AccountDao  extends JpaRepository<Account,Integer> {
}
```



##### 5.测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
// @WebAppConfiguration  //web项目增加这个 SpringApplicationConfiguration过时了，用下面的代替
@SpringBootTest(classes = SpringBootJpaApplication.class)
public class SpringBootJpaApplicationTests {

    @Autowired
    private AccountDao dao;

    @Test
    public void contextLoads() {
        //懒加载问题,不能使用getOne()
        Optional<Account> account = dao.findById(1);
        System.out.println(account.get().toString());
    }

}
```

