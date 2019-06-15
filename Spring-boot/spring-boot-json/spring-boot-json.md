### Jackson配置

###### 1.默认jackjson

除了 Spring Boot 必须自带的 parent 依赖外，仅仅只需要加入这个 `spring-boot-starter-web` 包即可，它会自动包含所有 JSON 处理的包。全局的 json 输出都是使用这个配置。只要 controller 添加注解`@RestController`或者`@ResponseBody` 即可。

![jackson](https://i.loli.net/2019/06/15/5d0488cedb02241537.jpg)

###### 2.entity

```java
public class User {
    @JsonProperty("user-name")
    private String userName;
    private Long id;
    
    private Integer age;
    
    @JsonIgnore
    private String address;
    
    @JsonInclude(JsonInclude.Include.NON_NULL)
    private String memo;
    // get set 略
}
```

- @JsonProperty： 可用来自定义属性标签名称；


- @JsonIgnore： 可用来忽略不想输出某个属性的标签；


- @JsonInclude： 可用来动态包含属性的标签，如可以不包含为 null 值的属性；



#### Fastjson全局设置

###### 1.引用依赖

```xml
<!--引入fastjson依赖-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.41</version>
</dependency>
```

###### 2.增加配置

```java
@Configuration
public class FastJsonConfiguration extends WebMvcConfigurerAdapter
{
	//修改自定义消息转换器
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        //调用父类的配置
        super.configureMessageConverters(converters);
        //创建fastJson消息转换器
        FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
        //创建配置类
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        //修改配置返回内容的过滤
        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.DisableCircularReferenceDetect,
                SerializerFeature.WriteMapNullValue,
                SerializerFeature.WriteNullStringAsEmpty
        );
        fastConverter.setFastJsonConfig(fastJsonConfig);
        //将fastjson添加到视图消息转换器列表内
        converters.add(fastConverter);
    }
}
```

具体设置：

> FastJson SerializerFeatures
> WriteNullListAsEmpty  ：List字段如果为null,输出为[],而非null
> WriteNullStringAsEmpty ： 字符类型字段如果为null,输出为"",而非null
> WriteMapNullValue：是否输出值为null的字段,默认为false。
> WriteNullBooleanAsFalse：Boolean字段如果为null,输出为false,而非null
> DisableCircularReferenceDetect ：消除对同一对象循环引用的问题，默认为false（如果不配置有可能会进入死循环）