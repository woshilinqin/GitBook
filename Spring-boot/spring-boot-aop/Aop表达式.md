### 切入点指示符

参考：https://blog.csdn.net/qq_23167527/article/details/78623639

@target(com.service.interface)
表示实现了括号里面的接口或者是继承了对应的类都会被匹配的到

@annotation(com.xxx.RequestMapper)
表示匹配@RequestMapper这个注解

execution(public * com.controller.*.*(..))
匹配路径

within：使用“within(类型表达式)”匹配指定类型内的方法执行



1) JoinPoint：提供访问当前被通知方法的目标对象、代理对象、方法参数等数据

```java
public interface JoinPoint {  
    String toString();         //连接点所在位置的相关信息  
    String toShortString();     //连接点所在位置的简短相关信息  
    String toLongString();     //连接点所在位置的全部相关信息  
    Object getThis();         //返回AOP代理对象  
    Object getTarget();       //返回目标对象  
    Object[] getArgs();       //返回被通知方法参数列表  
    Signature getSignature();  //返回当前连接点签名  
    SourceLocation getSourceLocation();//返回连接点方法所在类文件中的位置  
    String getKind();        //连接点类型  
    StaticPart getStaticPart(); //返回连接点静态部分  
}
```

2.获取目标对象(cast Object)

RequestHandle handle = (RequestHandle)joinPoint.getTarget();