### spring-boot 统一异常处理 ExceptionHandler

一般后端出现异常，Spring Boot提供了一个默认的映射：`/error`，当处理中抛出异常之后，会转到该请求中处理，并且该请求有一个全局的错误页面用来展示异常内容。这个是页面的处理方式，下面介绍使用t统一返回 Json 的形式。

###### 1.新建返回实体 Bean

```java
@Getter
@Setter
@ToString
public class ApiResult implements Serializable {
    //消息提示
    private String msg;
    //状态信息
    private boolean flag = true;
    //返回结构
    private Object result;
    //查询出的结构总数
    private int rows;
    //需要跳转的路径
    private String jumpUrl;
    //接口相应时间毫秒单位
    private long time;

    //禁止new创建对象
    private ApiResult() {
    }

    /**
     * 统一创建ApiResultBean对象
     * 方便后期扩展
     *
     * @return
     */
    public static ApiResult newInstance() {
        return new ApiResult();
    }
}
```

###### 2.新建封装类`ApiResultGenerator`

```java
public final class ApiResultGenerator {
    /**
     * 创建普通消息方法
     *
     * @param flag    执行接口状态，true:执行成功,false:执行失败
     * @param msg     返回消息内容
     * @param result  返回结果
     * @param jumpUrl 跳转路径
     * @return
     */
    public static ApiResult result(boolean flag, String msg, Object result, String jumpUrl, int rows, Throwable throwable) {
        //创建返回对象
        ApiResult apiResult = ApiResult.newInstance();
        apiResult.setFlag(flag);
        apiResult.setMsg(msg == "" ? "执行成功" : msg);
        apiResult.setResult(result);
        apiResult.setJumpUrl(jumpUrl);
        apiResult.setTime(System.currentTimeMillis());
        apiResult.setRows(rows);
        return apiResult;
    }

    /**
     * 返回执行成功视图方法
     *
     * @param result 执行成功后的返回内容
     * @return
     */
    public static ApiResult successResult(Object result) {
        //rows默认为0
        int rows = 0;
        //如果是集合
        if (result instanceof List) {
            //获取总数量
            rows = ((List) result).size();
        }
        return result(true, "", result, "", rows, null);
    }

    /**
     * 成功没有内容方法
     *
     * @return
     */
    public static ApiResult successResult(HttpServletRequest request) {
        return successResult("");
    }

    /**
     * 执行失败后返回视图方法
     *
     * @param msg 执行失败后的错误消息内容
     * @return
     */
    public static ApiResult errorResult(String msg, Throwable throwable) {
        return result(false, msg, "", "", 0, throwable);
    }
}
```

###### 3.核心控制器增强类

`@ControllerAdvice`标记增强所有注解为`RestController`的类。异常处理都跳这里面来。

```java
@ControllerAdvice(annotations = RestController.class)
@ResponseBody
public class ExceptionHandler {

    @ResponseStatus(reason = "服务器有bug")
    @org.springframework.web.bind.annotation.ExceptionHandler
    public ApiResult runtimeExceptionHandler(Exception e) {
        e.printStackTrace();
        return ApiResultGenerator.errorResult(e.getMessage(), e);
    }
}

```

###### 4.测试

```java
@RestController
public class Index {
    @RequestMapping("/index/{id}")
    public String Index(@PathVariable int id) {
        System.out.println(20 / id);
        return "成功访问";
    }
}
```

