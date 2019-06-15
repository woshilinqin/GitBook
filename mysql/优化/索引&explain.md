###### 1.设计表的时候尽量符号三大范式

- 第一范式:`不可再分`原则,例如联系方式分为电话和手机
- 第二范式: 不存在`依赖关系`,如课程表里面有课程id还有学生id应该分为两个表
- 第三范式:不存在`传递依赖`,比如每个表都有学校名或者有学校地址,应该拆分为两个表

###### 2.最好每个表有主键，设置auto_increment字段，避免有null，default默认可以为空字符串。

###### 3.text类型无法建立索引。enum类型最好使用 小整形 来记录。

###### 4.尽量避免使用 GROUP BY、DISTINCT 、OR、IN 等语句的使用，避免使用大表联合查询和子查询，因为将使执行效率大大下降

###### 5.针对索引字段使用 >, >=, =, <, <=, IF NULL和BETWEEN 将会使用索引， 如果对某个索引字段进行 LIKE 查询，使用 LIKE ‘%abc%’不能使用索引，使用 LIKE ‘abc%’ 将能够使用索引。

###### 6.查询条件左边不要用任何的函数，数学运算，尽可能用UNION ALL 取代UNION 。组合索引最左边的值要在条件中有用到才有用。
最左原则。数据更新频繁的列不宜建立索引；

查看索引：`show index from table`
删除：`ALTER table user DROP INDEX  UK_d2ia11oqhsynodbsi46m80vfc`
创建索引：`create INDEX age_password on user(age,password)` 
没有修改。

分析：
**select_type:**	 

​	`SIMPLE`:简单SELECT(不使用UNION或子查询)

​       `UNION`:UNION中的第二个或后面的SELECT语句

**table:** 对应的表

**type：**最重要的一个参数（顺序排，最优到最差）
	system、const 表仅有一行
	eq_ref 对于每个来自于前面的表的行组合,后表中读取一行，查询的比较操作通常是 =, 查询效率较高.
	ref 使用非唯一索引或者唯一索引扫描或者是使用了 最左前缀
	range 索引范围检索，这个类型通常出现在 =, <>, >, >=, <, <=, IS NULL, <=>, BETWEEN, IN() 操作中.
	index 检索索引树
	all 全表扫描

**possible_keys：**指出MySQL能使用哪个索引在该表中找到行
       key：显示MySQL实际决定使用的键(索引)。如果没有选择索引,键是NULL。
       key_len：显示MySQL决定使用的键长度。如果键是NULL,则长度为NULL
       ref：显示使用哪个列或常数与key一起从表中选择行
      rows：显示MySQL认为它执行查询时必须检查的行数。多行之间的数据相乘可以估算要处理的行数

![解析sql](https://i.loli.net/2019/06/15/5d04860b08e6764970.jpg)

 