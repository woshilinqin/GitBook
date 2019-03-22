### Collectors

###### 1.获取最小值`minBy`

```java
 Optional<Integer> min = list.stream().collect(Collectors.minBy((o1, o2) -> o1 - o2));
 if (min.isPresent()) {
      System.out.println(min.get());
  }

```

或者直接使用stream内置的`min`方法

```java
list.stream().min((o1, o2) ->{return o1 - o2;}).get()
```

###### 2.求和 `summingInt` `summingLong` `summingDouble`

```java
Integer sum = list.stream().collect(Collectors.summingInt(Integer::intValue));
```

###### 3.分组

```java
Map<String, List<Fruit>> groupMap = fruitList.stream().collect(Collectors.groupingBy(Fruit::getName));
```

```java
 Map<String, List<Integer>> groupby = list.stream().collect(Collectors.groupingBy(item -> {
            if (item >= 5) {
                return "datuwu";
            } else {
                return "xiaoxuwu";
            }
        }));

        List<Integer> datuwu = groupby.get("datuwu");
        List<Integer> xiaoxuwu = groupby.get("xiaoxuwu");

        datuwu.forEach(System.out::println);
        System.out.println();
        xiaoxuwu.forEach(System.out::println);
```

###### 4.`toMap`

初始化list对象集合

```java
   User user = new User("linqin", 12);
        User user1 = new User("linqin1", 13);
        User user2 = new User("linqin2", 14);
        User user3 = new User("linqin3", 12);

        List<User> list = new ArrayList<>();
        list.add(user);
        list.add(user1);
        list.add(user2);
        list.add(user3);
```

tomap要注意重复数据的处理。否者重复key会报错。

```java
Map<Integer, String> collect = list.stream().collect(Collectors.toMap(User::getAge, User::getName, (o1, o2) -> o2));
```

> [12=linqin3, 13=linqin1, 14=linqin2]

stream的`map`方法，区别：只能取出一个key或者value的值。

```java
List<Integer> collect1 = list.stream().map(User::getAge).collect(Collectors.toList());
collect1.forEach(System.out::println);
```

> 12
> 13
> 14
> 12

###### 5.`toList`

```java
List<String> collect = list.stream().collect(Collectors.toList());
```

###### 6.`toSet`去重

```java
Set<String> collect1 = list.stream().collect(Collectors.toSet());
```

###### 7.自定义集合collection

```java
List<String> linkedListResult = list.stream().collect(Collectors.toCollection(LinkedList::new));
```

###### 8.`joining`

```java
String join = list.stream().collect(Collectors.joining(","));
```

###### 9.`partitioningBy`分组

```java
 Map<Boolean, List<String>> twoList = list.stream().collect(Collectors.partitioningBy(item -> item.contains("++")));
List<String> containP = twoList.get(true);
List<String> noContainP = twoList.get(false);
containP.forEach(System.out::println);
System.out.println("-----------++---");
noContainP.forEach(System.out::println);
System.out.println("-------------++-");
```

###### 10.`counting`

```java
Long num = list.stream().collect(Collectors.counting());
```

和stream的`count()`一样效果

```java
list.stream().count()
```

