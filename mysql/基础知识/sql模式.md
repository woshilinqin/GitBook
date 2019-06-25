## 查询mysql 相关mode

```sql
select @@global.sql_mode
```

在 mysql 版本为 5.7+ 默认的模式为

> ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

其中 ONLY_FULL_GROUP_BY 意味着使用 group by 语句 select 部分不能使用聚合函数，如 max，min 等，否则报错

```java
Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated colum
```

###### 解决方案：

修改 mysql 配置 my.conf ，增加配置

```properties
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

去掉默认的 ONLY_FULL_GROUP_BY 配置。重启服务即可！