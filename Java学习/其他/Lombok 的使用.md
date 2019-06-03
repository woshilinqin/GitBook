### Lombok 的使用

###### 1.依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.6</version>
</dependency>
```

###### 2.idea 安装插件

[下载地址](https://plugins.jetbrains.com/files/6317/61698/lombok-plugin-0.25-2018.2.zip?updateId=61698&pluginId=6317&family=intellij)

idea 选择离线安装即可。快捷键 ctrl + F12 查看生成的方法。

###### 3.常用注解

- `@data`

  @Data 注解就可以有下面几个注解的功能： @ToString、@Getter、@Setter、@EqualsAndHashCode、@NoArgsConstructor 。

  PS：同时使用@Data 和 @AllArgsConstructor 后 ，默认的无参构造函数失效，如果需要它，要重新设置 @NoArgsConstructor

- `@Getter`

- `@Setter`

- `@ToString`

- `@EqualsAndHashCode`

- `@NoArgsConstructor`

- `@AllArgsConstructor`

- `@Builder`

  ```java
  public class User {
      private String name;
      private Integer id;
  
      public static class Builder {
          private String name;
          private Integer id;
  
          public String getName() {
              return name;
          }
  
          public void setName(String name) {
              this.name = name;
          }
  
          public Integer getId() {
              return id;
          }
  
          public void setId(Integer id) {
              this.id = id;
          }
  
          public Builder name(String name){
              this.name = name;
              return this;
          }
  
          public Builder id(Integer id){
              this.id = id;
              return this;
          }
  
          public User builder(){
              User user = new User();
              user.setName(name);
              user.setId(id);
              return user;
          }
      }
  }
  ```
  建造者模式。

  lombok 调用方式：

  ```
  User user = new User();
  user = user.builder().id(1).name("lin").build();
  ```



- `@RequiredArgsConstructor`
  会生成一个包含常量（final），和标识了@NotNull 的变量 的构造方法。会和`@NoArgsConstructor`，`@AllArgsConstructor`冲突。不能同时使用。

- `@Accessors(chain = true)`
  表示使用链式编程

  ```java
   User user = new User();
   user = user.setId(1).setName("zz");
  ```

- `@Slf4j`
  可以直接使用`log`日志对象。

- `@Cleanup`如果要清理的对象类型没有close()方法，而是其他一些无参数方法，则可以指定此方法的名，必须无参数，@Cleanup(”dispose”)

  ```java
     @Cleanup FileInputStream fis = new FileInputStream(file);
     fis.read();
     
     // 编译后的class文件
     FileInputStream fis = new FileInputStream(file);
          try {
              fis.read();
          } finally {
              if (Collections.singletonList(fis).get(0) != null) {
                  fis.close();
              }
          }
  ```

  