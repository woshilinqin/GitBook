使用情景：

那些没有事先考虑，而已经出现了的，数据查询慢的情况。这个时候如果要把已有的大数据量表分开。sql语句不需要重写。逻辑分表。

[微信文章](https://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247484590&idx=1&sn=bd2318df0dfe8c5e00ffc8c81f45a97c&chksm=ebd63b82dca1b294e1eccd3b2c9c3b3cdf8ff5f1a6b16c98282c99d8e69dd0d30212cdd37fad&mpshare=1&scene=1&srcid=0410FldVqd0e5wBInQYLDs2O#rd)

mysql>show engines;

的时候你会发现mrg_myisam其实就是merge



```
mysql> CREATE TABLE IF NOT EXISTS `user1` (  
->   `id` int(11) NOT NULL AUTO_INCREMENT,  
->   `name` varchar(50) DEFAULT NULL,  
->   `sex` int(1) NOT NULL DEFAULT '0',  
->   PRIMARY KEY (`id`)  
-> ) ENGINE=MyISAM  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;  
Query OK, 0 rows affected (0.05 sec)  
 
mysql> CREATE TABLE IF NOT EXISTS `user2` (  
->   `id` int(11) NOT NULL AUTO_INCREMENT,  
->   `name` varchar(50) DEFAULT NULL,  
->   `sex` int(1) NOT NULL DEFAULT '0',  
->   PRIMARY KEY (`id`)  
-> ) ENGINE=MyISAM  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;  
Query OK, 0 rows affected (0.01 sec)  
 
mysql> INSERT INTO `user1` (`name`, `sex`) VALUES('张映', 0);  
Query OK, 1 row affected (0.00 sec)  
 
mysql> INSERT INTO `user2` (`name`, `sex`) VALUES('tank', 1);  
Query OK, 1 row affected (0.00 sec)  
 
mysql> CREATE TABLE IF NOT EXISTS `alluser` (  
->   `id` int(11) NOT NULL AUTO_INCREMENT,  
->   `name` varchar(50) DEFAULT NULL,  
->   `sex` int(1) NOT NULL DEFAULT '0',  
->   INDEX(id)  
-> ) TYPE=MERGE UNION=(user1,user2) INSERT_METHOD=LAST AUTO_INCREMENT=1 ;  
Query OK, 0 rows affected, 1 warning (0.00 sec)  
 
mysql> select id,name,sex from alluser;  
+----+--------+-----+  
| id | name   | sex |  
+----+--------+-----+  
|  1 | 张映 |   0 |  
|  1 | tank   |   1 |  
+----+--------+-----+  
2 rows in set (0.00 sec)  
 
mysql> INSERT INTO `alluser` (`name`, `sex`) VALUES('tank2', 0);  
Query OK, 1 row affected (0.00 sec)  
 
mysql> select id,name,sex from user2  
-> ;  
+----+-------+-----+  
| id | name  | sex |  
+----+-------+-----+  
|  1 | tank  |   1 |  
|  2 | tank2 |   0 |  
+----+-------+-----+  
2 rows in set (0.00 sec)
```

