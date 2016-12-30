Title: postgresql tips
Date: 2014-12-22 15:10
Category: database
Tags: 201412, pgsql 
Author: laomie
Summary: postgresql tips

postgresql修改密码
=======================================
修改“/etc/postgresql/9.6/main/pg_hba.conf"
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

恢复“/etc/postgresql/9.6/main/pg_hba.conf"
```bash
local   all    all   peer
```

psql常用命令
===========================
```
-- load data from csv
\copy {table_name} (column1, column2, ... ) from '/path/data.csv' with delimiter ','

-- data to csv
\copy ${table_name} to '${temp_path}/${table_name}.csv' delimiter ','

-- list schema
\dn

-- list table
\dt

-- description table
\d table_name

```
