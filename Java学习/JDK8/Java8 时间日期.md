### 使用 LocalDate、LocalDateTime、LocalTime

###### 获取当前时间实例 `now()`

```java
// 2019-04-19
LocalDate localDate = LocalDate.now();
//2019-04-19T11:07:40.039
LocalDateTime localDateTime = LocalDateTime.now();
//11:07:40.039
LocalTime localTime = LocalTime.now();
```

###### 自定义初始化 `of()`

```java
  // 设定时间
  LocalDateTime.of(2017, 01, 01, 00, 00, 00);
  LocalDate.of(2017, 01, 01);
  LocalTime.of(12, 12);
```

###### 加时间 `plus*`系列

```java
LocalDateTime localDateTime = LocalDateTime.now();
localDateTime.plusYears(1);
localDateTime.plusMonths(1);
localDateTime.plusDays(1);
localDateTime.plusHours(1);
localDateTime.plusMinutes(1);
localDateTime.plusSeconds(1);
localDateTime.plusNanos(1);
localDateTime.plusWeeks(1);
```

###### 减时间  `minus`系列

```java
localDateTime.minusYears(1);
localDateTime.minusMonths(1);
localDateTime.minusDays(1);
localDateTime.minusHours(1);
localDateTime.minusMinutes(1);
localDateTime.minusSeconds(1);
localDateTime.minusNanos(1);
localDateTime.minusWeeks(1);
```

###### 时间校正器 `TemporalAdjuster `

- 静态类 `TemporalAdjusters`包含很多静态的方法。

![TemporalAdjusters具体实现](https://i.loli.net/2019/06/15/5d046f44c2fef75691.jpg)

```java
   // 2019-04-21
   LocalDate localDate2 = LocalDate.now();
   // 2019-04-01
   localDate2.with(TemporalAdjusters.firstInMonth(DayOfWeek.SATURDAY));
```

- 自定义时间较正器

```java
      LocalDateTime localDateTime1 = LocalDateTime.now();
        System.out.println(localDateTime1); // 2019-04-19T16:14:20.155

        LocalDateTime ldt5 = localDateTime1.with(temporal -> {
            LocalDateTime ldt4 = (LocalDateTime) temporal;
            DayOfWeek dow = ldt4.getDayOfWeek();

            if(dow.equals(DayOfWeek.FRIDAY)){
                LocalDateTime localDateTime = ldt4.plusDays(3);
                return localDateTime;
            }else if(dow.equals(DayOfWeek.SATURDAY)){
                return ldt4.plusDays(2);
            }else{
                return ldt4.plusDays(1);
            }
        });

        System.out.println(ldt5); // 2019-04-22T16:14:20.155
```



######  Duration : 用于计算两个“时间”间隔

######  Period : 用于计算两个“日期”间隔

```java
 		Instant ins1 = Instant.now();

        System.out.println("--------------------");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
        }

        Instant ins2 = Instant.now();

        System.out.println("所耗费时间为：" + Duration.between(ins1, ins2));

        System.out.println("----------------------------------");

        LocalDate ld1 = LocalDate.now();
        LocalDate ld2 = LocalDate.of(2011, 1, 1);

        Period pe = Period.between(ld2, ld1);
        System.out.println(pe.getYears());
        System.out.println(pe.getMonths());
        System.out.println(pe.getDays());
```



###### Instant 时间戳

```java
Instant ins = Instant.now();  //默认使用 UTC 时区
System.out.println(ins);

OffsetDateTime odt = ins.atOffset(ZoneOffset.ofHours(8));
System.out.println(odt);

System.out.println(ins.getNano());

Instant ins2 = Instant.ofEpochSecond(5);
System.out.println(ins2);

```



###### DateTimeFormatter 

```java
// DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss E");
DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyyMMdd HH:mm:ss.SSS");
LocalDateTime ldt = LocalDateTime.now();
System.out.println(dtf.format(ldt)); //2019年04月20日 18:52:43 星期六

// 字符串转时间,字符串格式要和转化格式一致。
LocalDateTime parse = LocalDateTime.parse("2018-06-01 10:12:05", dtf);
System.out.println(parse);

// 和 Date 转换
Date date = new Date();
LocalDateTime localDateTime = LocalDateTime.ofInstant(date.toInstant(), ZoneId.systemDefault());
System.out.println(localDateTime);
```

PS： SimpleDateFormat  线程不安全，多线程情况下，部分线程可能获取的时间不对，或则，部分线程直接报 **java.lang.NumberFormatException:multiple points **错，线程直接挂死了。解决方案：只在需要的时候创建新实例，不用static修饰。
```java
 // 线程不安全，多线程会有问题。
 SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd HH:mm:ss.SSS");
 System.out.println(sdf.format(new Date()));
```