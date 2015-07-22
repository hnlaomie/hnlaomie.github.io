Title: postgresql tips
Date: 2014-12-22 15:10
Category: database
Tags: 201412, pgsql 
Author: laomie
Summary: postgresql tips

postgresql修改密码
---------------------------------
修改“/etc/postgresql/9.1/main/pg_hba.conf"
```bash
local   all    all   md5
```

修改密码
```bash
$ sudo service postgresql restart
$ sudo -u postgres psql
postgres=# \password postgres
postgres=# \q
```

恢复“/etc/postgresql/9.1/main/pg_hba.conf"
```bash
local   all    all   peer
```
