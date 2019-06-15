# 前言

在开发过程中，经常有这样的情况。if elseif ... else。条件少还好，一旦 `else if` 过多这里的逻辑将会比较混乱，并很容易出错。但是也不要盲目使用，如果业务预测就几个分支，那么直接`if else` 最好。

当每个分支里面的操作都有一定的复杂时才有意义。不需要简单问题复杂化。

##### 1.直接使用ifelse

在上一个计算机例子中。我们在业务中的使用如下，根据对应页面的输入来选择对应的实现类。

![项目结构](https://i.loli.net/2019/06/15/5d048f77187da44172.jpg)

```java
@RestController
public class TestController {
    @Autowired
    private Computer computer;

    @Autowired
    private Mouse mouse;

    @Autowired
    private Keyboard keyboard;

    @Autowired
    private Camera camera;

    @RequestMapping("/test")
    public String test(String data) {
        if ("1".equals(data)) {
            computer.setUsb(mouse);
        } else if ("2".equals(data)) {
            computer.setUsb(keyboard);
        } else if ("3".equals(data)){
            computer.setUsb(camera);
        }
        return computer.readData(data);
    }

}
```

如果更多这会很难维护，也可以把这里封装为简单工厂。但是这些都没有以下的好。



##### 2.使用枚举来维护

新增三个类：

![新增类](https://i.loli.net/2019/06/15/5d048f775daf151184.jpg)



UsbList:枚举类

```java
public enum UsbList {
    CAMERA("1", "照相机设备", "com.service.Camera"),
    KEYBOARD("2", "键盘设备", "com.service.Keyboard"),
    MOUSE("3", "鼠标设备", "com.service.Mouse");

    /**
     * 对应关系数字
     */
    private String num;
    /**
     * 描述
     */
    private String description;
    /**
     * 实现类
     */
    private String implClass;

    UsbList(String num, String description, String implClass) {
        this.num = num;
        this.description = description;
        this.implClass = implClass;
    }

    /**
     * 获取枚举维护列表的全部结果
     *
     * @return
     */
    public static Map<String, Object> getAllClazz() {
        Map<String, Object> map = new HashMap<>();
        for (UsbList emum : UsbList.values()) {
            map.put(emum.getNum(), emum.getImplClass());
        }

        return map;
    }
    //...set get省略
}
    
```

usb选择器：`UsbSelector`

```java
@Component
public class UsbSelector {

    public USB getInstance(String data) throws ClassNotFoundException {
        // 获取枚举关系列表
        Map<String, Object> allClazz = UsbList.getAllClazz();

        String clazz = (String) allClazz.get(data);
        if (StringUtils.isEmpty(clazz)) {
            System.out.println("没有该实现类！");
        }

        //根据维护列表里面的选择对应的实现类
        USB usb = (USB) SpringBeanFactory.getBean(Class.forName(clazz));
        return usb;
    }
}
```

spring工厂类：`SpringBeanFactory` 工具类

```java
/**
 * @Description: spring工厂容器
 * @author: LinQin
 * @date: 2019/02/11
 */
@Component
public class SpringBeanFactory implements ApplicationContextAware {

    private static ApplicationContext context;

    public static <T> T getBean(Class<T> c){
        return context.getBean(c);
    }


    public static <T> T getBean(String name,Class<T> clazz){
        return context.getBean(name,clazz);
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        context = applicationContext;
    }
}
```



控制器修改：只要修改一点即可。免去更多的注入。

```java
@RestController
public class TestController {
    @Autowired
    private Computer computer;

    @Autowired
    private UsbSelector usbSelector;

    @RequestMapping("/test")
    public String test(String data) throws ClassNotFoundException {
        USB instance = usbSelector.getInstance(data);
        computer.setUsb(instance);

        return computer.readData(data);
    }

}
```