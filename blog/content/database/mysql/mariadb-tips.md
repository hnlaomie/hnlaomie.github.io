Title: mariadb tips
Date: 2014-07-12 10:30
Category: database 
Tags: 201407, mysql, mariadb
Author: laomie
Summary: mariadb 常见问题的解决办法

mariadb设置root密码
==================================
```bash
mysqladmin -u root password "passwd"
```

数据库导入导出
===============================
```bash
-- 导出整个库
mysqldump -uroot -ppassword -P3306 -h127.0.0.1 --default-character-set=utf8 --result-file=test.sql test
-- 导出表
mysqldump -uroot -ppassword -P3306 -h127.0.0.1 --default-character-set=utf8 --result-file=test.sql test table1 table2
-- 导出表结构
mysqldump -d -uroot -ppassword -P3306 -h127.0.0.1 --default-character-set=utf8 --result-file=test.sql test table1
-- 导入数据
mysql -uroot -ppassword -P3306 -h127.0.0.1 test < test.sql
```

字符集设置为utf-8
=======================
/etc/my.cnf做如下修改
```
[mysqld]
skip-character-set-client-handshake=1
default-character-set=utf8
```


