### 多表join优化

###### 例子：

A表70w数据，B表5w，C表100条。

如果直接`INNER JOIN`肯定可以得到结果。但是结果集是很大的性能不高。

```sql
SELECT 
  c.id,
  c.name,
  c.position,
  c.sex,
  c.phone,
  c.office_phone,
  c.feature_info,
  c.birthday,
  c.creator_id,
  c.is_keyperson,
  c.giveup_reason,
  c.status,
  c.data_source,
  FROM_UNIXTIME(c.created_time) AS created_time,
  FROM_UNIXTIME(c.last_modified) AS last_modified,
  c.last_modified_user_id 
FROM
  contact c 
  INNER JOIN contact_branch cb 
    ON c.id = cb.contact_id 
  INNER JOIN branch_user bu 
    ON cb.branch_id = bu.branch_id 
    AND bu.status IN (1, 2) 
  INNER JOIN org_emp_info oei 
    ON oei.data_id = bu.user_id 
    AND oei.node_left >= 2875 
    AND oei.node_right <= 10802 
    AND oei.org_category = - 1 
ORDER BY c.created_time DESC 
LIMIT 0, 10 ;
优化后
SELECT 
  c.id,
  c.name,
  c.position,
  c.sex,
  c.phone,
  c.office_phone,
  c.feature_info,
  c.birthday,
  c.creator_id,
  c.is_keyperson,
  c.giveup_reason,
  c.status,
  c.data_source,
  FROM_UNIXTIME(c.created_time) AS created_time,
  FROM_UNIXTIME(c.last_modified) AS last_modified,
  c.last_modified_user_id 
FROM
  contact c 
WHERE EXISTS 
  (SELECT 
    1 
  FROM
    contact_branch cb 
    INNER JOIN branch_user bu 
      ON cb.branch_id = bu.branch_id 
      AND bu.status IN (1, 2) 
    INNER JOIN org_emp_info oei 
      ON oei.data_id = bu.user_id 
      AND oei.node_left >= 2875 
      AND oei.node_right <= 10802 
      AND oei.org_category = - 1 
  WHERE c.id = cb.contact_id) 
ORDER BY c.created_time DESC 
LIMIT 0, 10 ;
```

原来的效果：本来`10000*500*200` 取符合条件200条。总操作数巨大
优化后变成: `500*200` 取符合条件200条 关联10000的表也是200条。总操作数少了。

###### 优化思路：

1. 确定要查出来的数据量，处理报表之外应该都不大。
2. 多表连接查询先想办法缩小范围，避免直接的多表连接，数据量会直接相乘。直接的多表连接：连接过程中产生了70w条数据，但是实际上只有那么几百条。解决可以把后面这些区分度明显的作为一个子查询，过滤后在和大的链接。
3. 先排序再join和`先join再排序`理论上开销是一样的，为何提升这么多是因为有一个limit！大致执行过程是：mysql先按索引排序得到前10条记录，然后再去join过滤，当发现不够10条的时候，再次去10条，再次join，这显然在内层join过滤的数据非常多的时候，将是灾难的，极端情况，内层一条数据都找不到，mysql还傻乎乎的每次取10条，几乎遍历了这个数据表！先排序再join在有limit的情况下不可行。



