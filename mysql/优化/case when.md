###### case when

```sql
SELECT 
  (
    CASE
      WHEN NAME = 'zs' 
      THEN '张三' 
      WHEN NAME = 'ls' 
      THEN '李四' 
      ELSE '王五' 
    END
  ) AS NAME 
FROM
  test 
```

也可以

```sql
SELECT 
  (
    CASE
      NAME 
      WHEN 'zs' 
      THEN '张三' 
      WHEN 'ls' 
      THEN '李四' 
      ELSE '王五' 
    END
  ) AS NAME 
FROM
  test 
```

生成指定的查询结果集。