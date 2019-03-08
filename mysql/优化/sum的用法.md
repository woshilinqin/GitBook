###### 1.使用sum在数据库计算往往很方便，但是它有精度问题

```sql
SELECT 
  SUM(CAST(amount AS DECIMAL (12, 2))) AS sumAmount 
FROM
  orders 
WHERE order_cnl = #{oldOrderNo} and sign='4'"}) 
```

数据库记录类型为varchar。先把amount的值转化（varchar变为decimal） 精度12，保留两位小数。再sum就不会精度问题。
`cast(amount as DECIMAL(12,2))`

###### 2.sql sum null换0

`COALESCE(sum(amount),0)`

