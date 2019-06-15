### Java 8 数据流

- 衔接操作：filter、map、sorted
- 终止操作：anyMatch、forEach、collect、Count、Reduce

`ps：任何的流操作都不会影响原来的数据结构。`

#### 1.获取数据流

集合.`stream()` 或者 `Stream.of()`

```java
List<String> stringCollection = new ArrayList<>();
stringCollection.add("ddd2");
stringCollection.add("aaa2");
stringCollection.add("bbb1");
stringCollection.add("aaa1");
stringCollection.add("bbb3");
stringCollection.add("ccc");
stringCollection.add("bbb2");
stringCollection.add("ddd1");

stringCollection.stream().forEach(System.out::println);
Stream.of(stringCollection).forEach(System.out::println);
```

#### 2.处理顺序

衔接操作的一个重要特性就是延迟性。这是因为衔接操作只在终止操作调用时被执行。

```java
   //无结果返回
    Stream.of("d2", "a2", "b1", "b3", "c")
            .filter(s -> {
                System.out.println("filter: " + s);
                return true;
            });
```


```java
//间隔输出filter、foreach
Stream.of("d2", "a2", "b1", "b3", "c")
        .filter(s -> {
            System.out.println("filter: " + s);
            return true;
        }).forEach(s -> System.out.println("forEach:" + s));
}
```


所以：有终止操作可以减少衔接的操作，比如anyMatch，不会每个元素都映射一次。

```java
Stream.of("d2", "a2", "b1", "b3", "c")
        .map(s -> {
            System.out.println("map: " + s);
            return s.toUpperCase();
        })
        .anyMatch(s -> {
            System.out.println("anyMatch: " + s);
            return s.startsWith("A");
        });
```

执行就是流水线式执行，如果衔接操作过程过滤了，那么终止操作也没法执行了。多个衔接操作就要考虑他们的顺序，可以直接影响执行的次数了。

#### 3.复用数据流

按照我们正常的想法，可以复用变量。

```java
List<String> stringCollection = new ArrayList<>();
stringCollection.add("ddd2");
stringCollection.add("aaa2");
stringCollection.add("bbb1");
stringCollection.add("aaa1");
stringCollection.add("bbb3");
stringCollection.add("ccc");
stringCollection.add("bbb2");
stringCollection.add("ddd1");

Stream<String> stream = stringCollection.stream();
stream.forEach(s -> System.err.println("forEach" + s));
stream.forEach(s -> System.out.println("forEach" + s));
```

报错

```shell
Exception in thread "main" java.lang.IllegalStateException: stream has already been operated upon or closed
	at java.util.stream.AbstractPipeline.sourceStageSpliterator(AbstractPipeline.java:279)
	at java.util.stream.ReferencePipeline$Head.forEach(ReferencePipeline.java:580)
	at stream.复用.main(复用.java:26)
```

解决方案：创建一个数据流供应器，来构建新的数据流，并且设置好所有衔接操作：

```java
stringCollection.stream().forEach(s -> System.out.println("forEach" + s));
Supplier<Stream<String>> streamSupplier = new Supplier() {
    @Override
    public Object get() {
        return stringCollection.stream();
    }
};
streamSupplier.get().forEach(System.out::println);
```



### 常用的方法

- 1.filter 过滤

```java
stringCollection.stream().filter((s) -> s.startsWith("a")).forEach(System.out::println);
```

其中filter 需要一个Predicate 对象

```java
Predicate predicate = new Predicate() {
    @Override
    public boolean test(Object o) {
        return false;
    }
};
```

- 2.sorted 排序

  ```java
  stringCollection.stream().sorted((a,b) -> b.compareTo(a)).forEach(System.out::println);
  ```

- 3.Map转换

  这个 map 转换是`不会去重`的，和 hashmap 不一样。

  ```java
  stringCollection.stream().map(String::toUpperCase).sorted((a,b) -> b.compareTo(a)).forEach(System.out::println);
  ```

- 4.Match 

  ```java
  boolean anyMatch = stringCollection.stream().anyMatch(s -> s.startsWith("s"));
  System.out.println(anyMatch);
  ```

- 5.Count

  ```java
  long count = stringCollection.stream().filter(s -> s.startsWith("a")).count();
  System.out.println(count);
  ```

- 6.Reduce 归约操作将所有流中的元素组合为单一结果。
  ifPresent 有值返回true，没值false

  ```java
  stringCollection.stream().sorted().reduce((s1, s2) -> s1 + "#" + s2).ifPresent(System.out::println);
  ```

- 7.collect

  ```java
  Set<String> collect = stringCollection.stream().collect(Collectors.toSet());
  collect.forEach(System.out::println);
  ```

- 8.flatMap 拆开

  ```java
   // flatMap 就是拆开
          Stream<List<Integer>> inputStream =
                  Stream.of(Arrays.asList(1), Arrays.asList(2, 3), Arrays.asList(4, 5, 6));
          inputStream.flatMap(childList -> childList.stream()).collect(Collectors.toList());
  ```

  图解：

![1558192452641](https://i.loli.net/2019/06/15/5d046f45d36b519497.jpg)