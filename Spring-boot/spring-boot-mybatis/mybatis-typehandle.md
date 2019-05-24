### 类型处理器（typeHandlers）

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器。

一些常用的情景很有用，无须多次执行重复动作。如数据库字段标记为 0 代表女性，1 代表男性。可以自定义类型进行数据出入解析。还有数据库保存 json 统一处理，数据库时间戳统一转化等。下面例子处理转化枚举类型。

###### 1.定义枚举

```java
//用于SexTypeHandler的性别转换器枚举
public enum Sex {
    //每一个类型都是一个枚举类（Sex）的实例
    MALE(1, "男"),
    FMALE(0, "女");

    //用于保存在数据库
    private int SexCode;
    //用于UI展示
    private String SexName;

    Sex(int sexCode, String sexName) {
        SexCode = sexCode;
        SexName = sexName;
    }

    public int getSexCode() {
        return SexCode;
    }

    //通过SexCode的值来获取Sex枚举类型，数据库只需保存code，通过代码解析成Sex类型
    public static Sex getSexFromCode(int code) {
        for (Sex sex : Sex.values()) {
            if (sex.getSexCode() == code) {
                return sex;
            }
        }
        return null;
    }
}
```

###### 2.定义实体 entity

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

其中注意到，性别参数类型为定义的枚举类 Sex，

###### 3.自定义处理器

注解 @MappedTypes({Sex.class}) 表示遇到类型为 Sex 类型的数据类型统一走该方法进行处理，也可以在 XML 中指定，一个是全局声明，一个是单个。

自定义 Handler 和`实现 TypeHandler<T>` 接口或则`继承 BaseTypeHandler<T>` 接口。

```java
@MappedTypes({Sex.class})
public class SexTypeHandle implements TypeHandler<Sex> {
    @Override
    public void setParameter(PreparedStatement preparedStatement, int i, Sex sex, JdbcType jdbcType) throws SQLException {
        //设置第i个参数的值为传入sex的code值，preparedStatement为执行数据库操纵的对象
        //传值的时候是一个sex对象，但是当进行映射插入的时候就会转化为sex的code值进行存储
        preparedStatement.setInt(i, sex.getSexCode());
    }

    @Override
    public Sex getResult(ResultSet resultSet, String s) throws SQLException {
        //获取数据库存储的sex的code值
        int result = resultSet.getInt(s);
        return Sex.getSexFromCode(result);
    }

    @Override
    public Sex getResult(ResultSet resultSet, int i) throws SQLException {
        int result = resultSet.getInt(i);
        return Sex.getSexFromCode(result);
    }

    @Override
    public Sex getResult(CallableStatement callableStatement, int i) throws SQLException {
        int result = callableStatement.getInt(i);
        return Sex.getSexFromCode(result);
    }
}
```

###### 4.配置

以下配置二选一

- yaml 配置
  增加配置 type-handlers-package 指定类型处理的包

  ```yaml
  mybatis-plus:
    check-config-location: true
    type-aliases-package: com.entity
    mapper-locations: classpath:mybatis/*/*.xml
    config-location: classpath:mybtais-config.xml
    type-handlers-package: com.typehandle
  ```

- mybatis-config.xml 配置
  package 标签指定包 和 typeHandler 标签指定具体类型。

  ```
   <typeHandlers>
          <!--<package name="com.typehandle"></package>-->
          <typeHandler handler="com.typehandle.SexTypeHandle"></typeHandler>
      </typeHandlers>
  ```

  

###### 5.测试

访问 insert 后在访问 select，页面显示的是枚举的值。

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
        typehandle.setAge(24).setConfig("json").setName("blin").setSex(Sex.FMALE);
        typehandleService.save(typehandle);
    }

    @RequestMapping("/select")
    public void select() {
        Typehandle typehandle = typehandleMapper.selectById(2);
        System.out.println(typehandle.toString());
    }
}
```

###### 结果：

> Typehandle(id=2, name=blin, sex=FMALE, config=json, age=24)



### 处理 list 集合转换

###### ListTypeHandle

```java
@MappedTypes({List.class})
public class ListTypeHandle extends BaseTypeHandler<List> {

    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, List list, JdbcType jdbcType) throws SQLException {
        preparedStatement.setString(i, JSON.toJSONString(list));
    }

    @Override
    public List getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return JSON.parseArray(resultSet.getString(s));
    }

    @Override
    public List getNullableResult(ResultSet resultSet, int i) throws SQLException {
        // i 从1开始
        return JSON.parseArray(resultSet.getString(i));
    }

    @Override
    public List getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return JSON.parseArray(callableStatement.getString(i));
    }
}
```

###### 对应的实体类修改为 list

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
    private List config;

    /**
     * 年龄
     */
    private Integer age;
    
}
```

配置已经配置为扫描 typehandle 包。所有目录下的类型处理器都会注册，此外，注解 `@MappedTypes`全局声明处理 List 集合，也无需在 XML 文件指定对应的处理器，（ps：xml 文件声明的优先）