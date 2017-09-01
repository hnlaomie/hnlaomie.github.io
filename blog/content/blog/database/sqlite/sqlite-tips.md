Title: sqlite tips
Date: 2017-06-30 14:30
Category: database 
Tags: 201706, sqlite 
Author: laomie
Summary: sqlite 常见问题的解决办法

sqlite 数据类型
==================================
```
INTEGER (INT, TINYINT, BIGINT)
TEXT (VARCHAR, TEXT, CLOB)
BLOB (BLOB)
REAL (FLOAT, DOUBLE)
NUMERIC (DECIMAL, BOOLEAN, DATE, DATETIME)
```

常用操作
===============================
```sql
-- upsert
UPDATE OR IGNORE …
INSERT OR IGNORE …

REPLACE INTO

-- rename table
ALTER TABLE foo RENAME TO bar

-- 查看表
.schema tbl1

```

数据导入导出
=======================
控制台方式
```
-- 导出csv
sqlite> .headers on
sqlite> .mode csv
sqlite> .output test.csv
sqlite> select * from tbl1;
sqlite> .output stdout

-- 导入csv
sqlite> .separator ,
sqlite> .import imei.csv tac_info

```
命令行方式
```
sqlite3 -header -csv /path/file.db "select * from tbl1;" > data.csv
sqlite3 -header -csv /path/file.db < query.sql > data.csv
```

