### SpringBean的生命周期

Spring Bean 生命周期比较复杂，可以分为创建和销毁两个过程。其中，注解`@Bean`加载的自定义 bean 也会实现BeanPostProcessor 的前后置方法。

**创建**

- 实现`InitializingBean`接口会在初始化时执行一次。
- 实现`BeanFactoryPostProcessor`接口会在初始化后执行一次。
- 实现`BeanPostProcessor`接口会在初始化每个接口时`各`执行一次对应实现的前后置方法。
- 实现`DisposableBean`接口会在容器关闭时执行一次销毁方法。

```java
@Component
public class MyBean implements InitializingBean, BeanPostProcessor, BeanFactoryPostProcessor, DisposableBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean初始化Bean时执行一次");
    }
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("bean初始化前执行");
        return null;
    }
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("bean初始化后执行");
        return null;
    }
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {
        System.out.println("bean初始化后执行一次");
    }
    @Override
    public void destroy() throws Exception {
        System.out.println("销毁");
    }
}
```

启动打印(顺序)：

> InitializingBean初始化Bean时执行一次
> bean初始化后执行一次
> bean初始化前执行
> bean初始化后执行
> bean
> bean初始化前执行
> bean初始化后执行
>
> ...
>
> 销毁

**销毁**

销毁需要在应用中需要关闭上下文才可以触发。`System.in.read()`模拟应用不退出的情况。

```java
@Test
public void test1() throws IOException {
    ConfigurableApplicationContext context = (ConfigurableApplicationContext) this.applicationContext;
    context.close();
    System.in.read();
}
```



### Spring Bean 有五个作用域

- `Singleton`，这是 Spring 的默认作用域，也就是为每个 IOC 容器创建唯一的一个 Bean 实
  例。

- `Prototype`，针对每个 getBean 请求，容器都会单独创建一个 Bean 实例。

  从 Bean 的特点来看，Prototype 适合有状态的 Bean，而 Singleton 则更适合无状态的情况。
  另外，使用 Prototype 作用域需要经过仔细思考，毕竟频繁创建和销毁 Bean 是有明显开销
  的。

  如果是 Web 容器，则支持另外三种作用域：

  - Request，为每个 HTTP 请求创建单独的 Bean 实例。
  - Session，很显然 Bean 实例的作用域是 Session 范围。
  - GlobalSession，用于 Portlet 容器，因为每个 Portlet 有单独的 Session，GlobalSession
    提供一个全局性的 HTTP Session。