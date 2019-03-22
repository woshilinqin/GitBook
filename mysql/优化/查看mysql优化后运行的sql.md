命令行窗口

> EXPLAIN EXTENDED sql；

命令行上再 `show warning；`可以在优化一些子查询的时候使用。



连接查询可以先过滤条件在连接，减少遍历的数据量。如

```sql
SELECT
    s.*
FROM
    (
        SELECT
            *
        FROM
            SC sc
        WHERE
            sc.c_id = 0
        AND sc.score = 100
    ) t
INNER JOIN Student s ON t.s_id = s.s_id
```