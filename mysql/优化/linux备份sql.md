mysqldump -uroot -p123456 -h 127.0.0.1 --single-transaction --default-character-set=utf8 
ecard_test trans -w "orderno in ('4137161113049663','4113161113756578')" > /var/trans.sql


mysqldump -uroot -p$(sudo cat /root/save/.root) -h 127.0.0.1 --single-transaction --default-character-set=utf8 ecard trans -w "orderno in ('4128170912395813','4151708221007297')" >/usr/BAK-sql/ecard.trans-bak1925_chg.sql