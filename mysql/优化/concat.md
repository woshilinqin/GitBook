###### SELECT CONCAT('1',NULL,'33')

按照指定的顺序结合起来，如有空则为空。oracle null不会空，并且只能连接两个



###### SELECT CONCAT_WS(';','qq',null,'ee','sd')

以第一个字符串为标准，把后面的字段按照它间隔连接起来，`跳过NULL`

> qq;ee;sd

