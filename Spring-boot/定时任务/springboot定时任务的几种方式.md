## SpringBoot下使用定时任务的几种方式

> 本文简单的对springboot下面几种定时任务的实现方式

#### 1.定时器Scheduled

- springboot web模块依赖整合好了定时器的依赖

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

新建`Task`类，加上注解Component，主函数加上`@EnableScheduling`

```java
@Component
public class Task {
    // @Scheduled(cron = "*/5 * * ? * *")
    /**
     * fixedRate上一个调用开始后再次调用的延时（不用等待上一次调用完成）
     */
    // @Scheduled(fixedRate = 1 * 1000)

    /**
     * 等到方法执行完成后延迟配置的时间再次执行该方法
     */
    @Scheduled(fixedDelay = 1 * 1000)
    public void Task() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(System.currentTimeMillis());
    }
}

```

这是一种非常简单的实现方式。但是有个缺点。如果多个定时任务一起执行，实际上是一个个任务同一个线程搞定的，如果其中一个出了异常，串行就走不下去了。

![网络来源图片](https://i.loli.net/2019/06/15/5d0488d11bd3543327.jpg)



#### 2.开启异步

新增异步配置类`AsyncConfig`

```java
public class AsyncConfig {
     /*
   	 此处成员变量应该使用@Value从配置中读取
     */
    private int corePoolSize = 10;
    private int maxPoolSize = 200;
    private int queueCapacity = 10;
    
    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(corePoolSize);
        executor.setMaxPoolSize(maxPoolSize);
        executor.setQueueCapacity(queueCapacity);
        executor.initialize();
        return executor;
    }

}
```

主函数增加`@EnableAsync`，表示开启异步支持。

任务方法里面添加`@Async`，表示使用异步线程执行定时任务

以上都是定时任务的基本操作，不支持动态修改cron表达式，需要修改并重启应用。



#### 3.SchedulingConfigurer

实现该接口可以实现定时任务动态修改。可以把cron表达式保存到数据库中。动态修改可以热加载定时器任务。

```java
@Configuration
@EnableScheduling
public class CompleteScheduleConfig implements SchedulingConfigurer {
    @Autowired
    private CronMapper cronMapper;

    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        taskRegistrar.addTriggerTask(
                //1.添加任务内容(Runnable)
                () -> System.out.println("执行定时任务2: " + LocalDateTime.now().toLocalTime()),
                //2.设置执行周期(Trigger)
                triggerContext -> {
                    //2.1 从数据库获取执行周期。修改数据库cron表达式即可。多个定时任务可以添加id字段区分重复一下代码即可。
                    String cron = cronMapper.getCron();

                    //2.2 合法性校验.
                    if (StringUtils.isEmpty(cron)) {
                        // Omitted Code ..
                    }
                    //2.3 返回执行周期(Date)
                    return new CronTrigger(cron).nextExecutionTime(triggerContext);
                }
        );


    }
}
```

数据库就一个单独的`cron`字段。

测试动态修改cron，执行定时任务的触发时间也相应改变。