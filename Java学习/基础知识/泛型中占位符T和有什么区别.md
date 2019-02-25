## 泛型

#### 1.T代表一个类型

> <T> 表示固定的一个内部类型，不用转型。

List集合内的类型只能是一种。

```java
public static <T> void show1(List<T> list){
 for (Object object : list) {
        System.out.println(object.toString());
    }
}
```



#### 2.？代表不确定类型

```java
public static void show2(List<?> list) {
    for (Object object : list) {
        System.out.println(object);
    }
}
```

List集合内的类型可以多种。



#### 3.List<？ extend Student>

这个？代表必须是Student的子类。



#### 4.List<？ super Student>

这个？代表必须是Student的父类。