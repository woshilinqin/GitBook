## Spring事务[^footnote1] ##

[^footnote1]: 参考：http://blog.csdn.net/qq_32331073/article/details/76508147

在一般的项目中，开启了声明式事务就是在Service调用方法头上加一个注解`@Transactional`，
但是其实里面有很多异常的情况。

 - ###### 正常情况

    当删除操作出现了异常，能正常回滚。


```
	@Transactional(propagation=Propagation.REQUIRED)
	public boolean delete(int id) {
		boolean result = mapper.delete(id);
		return result;
	}
```

 - ###### 已有try-catch处理

  该增加操作不能回滚，原因是抛出的异常被自己内部的try-catch处理掉了，没有往外抛出。spring当作成功的例子直接插入数据库了。
```
@Transactional(propagation=Propagation.REQUIRED)
	public void save(User user) {
		try {
			mapper.save(user);
			throw new Exception("异常");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```
 - ###### 特定异常回滚

  `rollbackFor`可以指定异常的类型回滚。
  `noRollbackFor`指定异常不回滚

  生产中，经常有全局异常统一管理，而 spring 默认的事务回滚是针对 runtimeException，导致很多想回滚的都失效了，此时需要指定回滚异常类型。

```
//定异常的类型回滚。
@Transactional(rollbackFor=ArithmeticException.class)

----------
//定异常不回滚
@Transactional(noRollbackFor=ArithmeticException.class)


```

 - ###### 主动抛出运行时异常，能回滚。

    `throw new RuntimeException(e)`
     开发过程中这个使用的比较多。或者直接不加try-catch保障数据的原子性。

```
	@Transactional
	public void save(User user) {
		try {
			mapper.save(user);
			int i=1/0;
		} catch (Exception e) {
			throw new RuntimeException(e); 
		}

	}
```

 - ###### 编程式实现主动回滚

    `TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();`

```
@Transactional(propagation=Propagation.REQUIRED)
	public boolean delete(int id) {
		boolean result = mapper.delete(id);
		TransactionAspectSupport.currentTransactionStatus().setRollbackOnly(); 
		return result;
	}
```

----------
## 事务的传播级别[^qwe] ##
[^qwe]: https://www.cnblogs.com/zhangqian1031/p/6542037.html

当有特殊的需求是，往往一个服务方法里面调用了其他的服务方法，形成一个链状的调用顺序。
事务的传播级别就是针对这个问题的。
`七种传播级别`
`propagation_requierd`：如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中，这是最常见的选择。
propagation_supports：支持当前事务，如果没有当前事务，就以非事务方法执行。
propagation_mandatory：使用当前事务，如果没有当前事务，就抛出异常。
propagation_required_new：新建事务，如果当前存在事务，把当前事务挂起。
propagation_not_supported：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
propagation_never：以非事务方式执行操作，如果当前事务存在则抛出异常。
propagation_nested：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与propagation_required类似的操作

> Spring 默认的事务传播行为是 PROPAGATION_REQUIRED，它适合于绝大多数的情况。假设 ServiveX#methodX() 都工作在事务环境下（即都被 Spring 事务增强了），假设程序中存在如下的调用链：Service1#method1()->Service2#method2()->Service3#method3()，那么这 3 个服务类的 3 个方法通过 Spring 的事务传播机制都工作在同一个事务中。
