### 1.问题的出现

在`未使用`LocalDateTime 以前，项目中的实体类都是用 Timestamp 对应的。可以在springboot的主配置文件中，统一进行格式化设置

```
# 返回时间戳
spring.jackson.serialization.write-dates-as-timestamps=true
```

使用`@responseBody`返回json数据时，会自动将时间格式化为毫秒值。

但当使用了LocalDateTime后，这种做法就失效了，时间数据会被转成一个类似于`[2019,2,22,15,24,55]`的数组，而并非毫秒值。引用自[**jackson-modules-java8**](https://github.com/FasterXML/jackson-modules-java8/tree/master/datetime)

> [`LocalDate`](https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html), [`LocalTime`](https://docs.oracle.com/javase/8/docs/api/java/time/LocalTime.html), [`LocalDateTime`](https://docs.oracle.com/javase/8/docs/api/java/time/LocalDateTime.html), and [`OffsetTime`](https://docs.oracle.com/javase/8/docs/api/java/time/OffsetTime.html), which cannot portably be converted to timestamps and are instead represented as arrays when `WRITE_DATES_AS_TIMESTAMPS` is enabled.

### 2.解决方案

1. 自定义`JsonSerialize`

```java
/**
 * json序列化，将LocateDateTime转换为时间戳
 *
 * @author zhangxu
 */
public class LocalDateTimeConverter extends JsonSerializer<LocalDateTime> {

    @Override
    public void serialize(LocalDateTime value, JsonGenerator gen, SerializerProvider serializers) throws IOException {
        //gen.writeNumber(value.toInstant(ZoneOffset.of("+8")).toEpochMilli());
        DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        gen.writeString(df.format(value));
    }
}
```

1. 在实体类时间属性上，加入`@JsonSerialize(using = LocalDateTimeConverter.class)`注解即可

### 3.其他问题

服务器返回时间戳的问题解决了，但还会有另一个问题，就是时间戳的接收问题。当接口使用实体类接收数据时，无法解析客户端传过来的时间戳，会报类型转换错误的bug。

这就需要自定义`Converter`解决问题。

```java
/**
 * spring接收参数格式转化 long --> LocalDateTime
 *
 * @author zhangxu
 */
@Configuration
public class DateConfig implements Converter<String, LocalDateTime> {

    @Override
    public LocalDateTime convert(String source) {
        ZoneOffset zoneOffset = ZoneOffset.of("+8");
        return LocalDateTime.ofInstant(Instant.ofEpochMilli(Long.parseLong(source)), zoneOffset);
    }
}
```

再次启动项目，就可以自动将时间戳转换为 LocalDateTime了。