### springboot 整合文档工具 swagger

1. swagger 依赖

```xml
    <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.2.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.2.2</version>
        </dependency>
```



2. 配置

```java
package com.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.ParameterBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.schema.ModelRef;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Parameter;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;
import java.util.List;

/**
 * @Description:
 * @author: LinQin
 * @date: 2019/05/05
 */

@Configuration
@EnableSwagger2
public class SwapperConfig {

    @Bean
    public Docket createRestApi() {

        //添加head参数start
        ParameterBuilder tokenPar = new ParameterBuilder();
        List<Parameter> pars = new ArrayList<Parameter>();
        tokenPar.name("x-access-token").description("令牌").modelRef(new ModelRef("string")).parameterType("header").required(false).build();
        pars.add(tokenPar.build());
        //添加head参数end

        return new Docket(DocumentationType.SWAGGER_2)
//                .apiInfo(apiInfo())
                .select()
//                .apis(RequestHandlerSelectors.any())
                .apis(RequestHandlerSelectors.basePackage("com.controller"))
                .paths(PathSelectors.any())
                .build()
                .globalOperationParameters(pars)
                .apiInfo(apiInfo());
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Spring Boot中使用Swagger2构建RESTful APIs")
                .description("描述")
                .termsOfServiceUrl("www.baidu.com")
                .contact("linin")
                .version("1.0")
                .build();
    }

}
```



3.控制器构建

```java
@RestController
@RequestMapping("/")
public class TestController {

    @ApiOperation(value="测试日志程序", notes="测试日志程序", produces = "application/json")
    @ApiImplicitParams(value = {
            @ApiImplicitParam(name = "school", value = "学校名称", required = true, paramType = "query", dataType = "String"),
            @ApiImplicitParam(name = "name", value = "姓名", required = true, paramType = "query", dataType = "String")
    })
    @RequestMapping(value = "/test", method = RequestMethod.GET)
    public Map test(String school, String name) {
        System.out.println("gg");
        Map map = new HashMap();
        map.put("school", school);
        map.put("name", name);
        return map;
    }
}

```

访问地址 http://localhost:8080/swagger-ui.html 

![swagger页面](https://i.loli.net/2019/06/15/5d0488d01bd2553415.jpg)