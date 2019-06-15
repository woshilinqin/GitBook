### 事务的传播级别
`七种传播级别` 

- propagation_requierd：如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中，这是最常见的选择。（**默认**，加入事务后未提交的事务数据可以正常读取到） 
- propagation_supports：支持当前事务，如果没有当前事务，就以非事务方法执行。 
- propagation_mandatory：使用当前事务，如果没有当前事务，就抛出异常。 
- propagation_required_new：新建事务，如果当前存在事务，把当前事务挂起。 
- propagation_not_supported：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。 
- propagation_never：以非事务方式执行操作，如果当前事务存在则抛出异常。 
- propagation_nested：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与propagation_required类似的操作

### 情况A
有事务的A方法调用没事务的B方法，都能成功插入，
B有异常都回滚。
A使用`REQUIRED`，B会加入到A的事务当中。
```java
@Transactional(propagation=Propagation.REQUIRED)
	public void A() {
		User user=new User();
		user.setUserName("张三");
		mapper.save(user);
		
		B();
	}

	public void B() {
		User user=new User();
		user.setUserName("李四");
		mapper.save(user);
	}
```

### 情况B
B单独加上事务`REQUIRES_NEW`：创建一个新的事务，挂起原来的事务。
无异常时都能添加。
B有异常，都不能添加。


问题就来了，为什么都不能添加呢？按道理应该B有异常B回滚，A正常添加才对。这里就涉及了JDK动态代理和spring事务的一个坑点。在Spring事务中嵌套调用其他的事务方法，事务都不能生效。
```java
@Transactional(propagation=Propagation.REQUIRED)
	public void A() {
		User user=new User();
		user.setUserName("张三");
		mapper.save(user);
		
		B();
	}
	@Transactional(propagation=Propagation.REQUIRES_NEW)
	public void B() {
		User user=new User();
		user.setUserName("李四");
		mapper.save(user);
		int i=1/0;
	}
```

### 动态代理的特性[^参考]
首先我们看一下一个简单的动态代理实现方式：
![这里写图片描述](https://i.loli.net/2019/06/15/5d048781e319b63895.jpg)

`接口`

```java
public interface OrderService {
	void test1();
	void test2();
}

```
`实现类`

```java
public class OrderServiceImpl implements OrderService {
	@Override
	public void test1() {
		 System.out.println("--执行test1--");

	}
	@Override
	public void test2() {
		 System.out.println("--执行test2--");
	}
}
```
`代理类`

```java
public class ObjectProxy implements InvocationHandler {
	private static final String METHOD_PREFIX = "test";
	private Object target;
	public ObjectProxy(Object target) {
		this.target = target;
	}
	
	@Override
	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {
		// 我们使用这个标志来识别是否使用代理还是使用方法本体
		if (method.getName().startsWith(METHOD_PREFIX)) {
			System.out.println("========分隔符========");
		}
		return method.invoke(target, args);
	}

	public Object getProxy() {
		return Proxy.newProxyInstance(Thread.currentThread()
				.getContextClassLoader(), target.getClass().getInterfaces(),
				this);
	}
}
```
`测试类`

```java
public class Test {
	public static void main(String[] args) {
	    OrderService orderService = new OrderServiceImpl();
        ObjectProxy proxy = new ObjectProxy(orderService);
        orderService = (OrderService) proxy.getProxy();
        orderService.test1();
        orderService.test2();
	}
}

```

获取的代理分别代理执行test1方法和test2方法。

> ==分隔符==
>
> --执行test1--
>
> ==分隔符==
>
> --执行test2--

代理执行了两次

----------


修改一下实现类嵌套执行test2

```java
public class OrderServiceImpl implements OrderService {
	@Override
	public void test1() {
		 System.out.println("--执行test1--");
		 test2();
	}
	@Override
	public void test2() {
		 System.out.println("--执行test2--");
	}
}

```
`测试类`

```java
public class Test {
	public static void main(String[] args) {
	    OrderService orderService = new OrderServiceImpl();
        ObjectProxy proxy = new ObjectProxy(orderService);
        orderService = (OrderService) proxy.getProxy();
        orderService.test1();
//        orderService.test2();
	}
}

```
`结果：`

> ==分隔符==
>
> --执行test1--
>
> --执行test2--

代理只了一次

**总结：**

**只有代理对象proxy直接调用的那个方法才是真正的走代理的，嵌套的方法实际上就是 直接把嵌套的代码移动到代理的方法里面。** 所以，嵌套的事务都不能生效。

注：只要是以代理方式实现的声明式事务，无论是 JDK 动态代理，还是 CGLIB 直接写字节码生成代理，都只有`public`方法上的事务注解才起作用。通过 spring 管理 bean，进行注入后再调用的嵌套事务才有效。

## **解决方案：** 

 - 传统mvc项目spring配置里面增加：
```xml
<!-- 开启暴露Aop代理到ThreadLocal支持，解决事务嵌套不生效问题。 -->
<aop:aspectj-autoproxy expose-proxy="true"/><!—注解风格支持-->  
<aop:config expose-proxy="true"><!—xml风格支持-->   
```
 配置约束要加上以下配置， 同时加入aop类的jar。
 ![这里写图片描述](https://i.loli.net/2019/06/15/5d0487824ad3174997.jpg)
使用代理对象调用B方法。

```java
	@Transactional(propagation=Propagation.REQUIRED)
	public void A() {
		User user=new User();
		user.setUserName("张三");
		mapper.save(user);
		try {
			((UserService) AopContext.currentProxy()).B();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	@Transactional(propagation=Propagation.REQUIRES_NEW)
	public void B() {
		User user=new User();
		user.setUserName("李四");
		mapper.save(user);
		throw new RuntimeException("child Exception....................");
	}

```
`注意`：调用嵌套的方法需要用try-catch处理，不然异常继续向上抛，导致A也异常回滚了。

 - SpringBoot配置方式

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```
添加注解：
![这里写图片描述](https://i.loli.net/2019/06/15/5d04878285d9861510.jpg)
修改原有代码的执行方式为：
![这里写图片描述](https://i.loli.net/2019/06/15/5d048782da84320338.jpg)

 - 通过ApplicationContext上下文进行解决

获取到当前类的实例对象直接调用。

```java

	/**
     * Spring应用上下文
     */
    @Autowired
    private ApplicationContext context;
    private UserService proxy;
    @PostConstruct
    public void init() {
        //从Spring上下文中获取AOP代理对象
        proxy = context.getBean(UserService.class);
    }
	
	@Transactional(propagation=Propagation.REQUIRED)
	public void A() {
		User user=new User();
		user.setUserName("张三");
		mapper.save(user);
		try {
			proxy.B();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	@Transactional(propagation=Propagation.REQUIRES_NEW)
	public void B() {
		User user=new User();
		user.setUserName("李四");
		mapper.save(user);
		throw new RuntimeException("child Exception....................");
	}

```


无论是 获取代理对象调用内嵌方法，还是从上下文对象获取对象调用。本质就是重新的调用一次内嵌的方法，避免了动态代理 只代理外面方法的坑。重新调用就重新产生事务了。

[^参考]: 参考：http://blog.csdn.net/xlgen157387/article/details/79026285
