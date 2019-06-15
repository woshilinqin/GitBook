##### 1.背景简介

在我们的日常开发中，经常会出现不断的增加业务需求，而且都是`同类`的东西，而策略模式就是帮助去拓展的一种形式。

比如：电脑接口，都支持USB。通过USB可以连接鼠标（mouse），键盘（keyboard），照相机（camera）等等。它还可以连接很多的外接设备。然后电脑就可以使用这些拓展出来的设备。

![Pattern of Computer](https://i.loli.net/2019/06/15/5d048f77cad7319988.jpg)





##### 2.示例代码

USB接口：

```java
public interface USB {
    String readData(String data);
}
```

实现类 *3：

```java
public class Camera implements USB {
    @Override
    public String readData(String data) {
        System.out.println("Camera");
        return data;
    }
}
```

```java
public class Keyboard implements USB {
    @Override
    public String readData(String data) {
        System.out.println("Keyboard");
        return data;
    }
}
```

```java
public class Mouse implements USB {
    @Override
    public String readData(String data) {
        System.out.println("Mouse");
        return data;
    }
}
```

具体计算机对象：

```java
public class Computer {

    private USB usb;

    public void setUsb(USB usb) {
        this.usb = usb;
    }

    public String readData(String data) {
        return usb.readData(data);
    }
}
```

测试用例：

```java
public class Test {
    public static void main(String[] args) {

        Computer computer = new Computer();
        computer.setUsb(new Keyboard());
        String data = computer.readData("input data");

        System.out.println(data);
    }
}
```



##### 3.优点

使用策略模式可以很好的对业务进行拓展，我们只需要增加一个接口实现就可以避免代码变得更加复杂。在具体使用对象里面进行注入对应的应用实例，可以灵活的变化。