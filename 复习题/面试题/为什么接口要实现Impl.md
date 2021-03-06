### 为什么接口要实现Impl

###### 1.解耦

抽象出service层也是为了更好的控制`事务`吧，将持久化和业务分开。

###### 2.面向接口编程

###### 3.代理方式

一、简单来说：

- JDK 动态代理只能对实现了接口的类生成代理，而不能针对类
- CGLIB 是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法（继承）

二、Spring在选择用JDK还是CGLiB的依据：

- Bean实现接口时，Spring就会用JDK的动态代理
- 当Bean没有实现接口时，Spring使用CGlib是实现

三、CGlib比JDK快？

- 使用CGLib实现动态代理，CGLib底层采用ASM字节码生成框架，使用字节码技术生成代理类，比使用Java反射效率要高。唯一需要注意的是，CGLib不能对声明为final的方法进行代理，因为CGLib原理是动态生成被代理类的子类。
- 在对JDK动态代理与CGlib动态代理的代码实验中看，1W次执行下，JDK7及8的动态代理性能比CGlib要好20%左右。