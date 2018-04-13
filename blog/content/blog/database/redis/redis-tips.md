Title: redis tips
Date: 2018-04-12 14:30
Category: database 
Tags: 201804, redis 
Author: laomie
Summary: redis 常见问题的解决办法

常用操作
===============================
```bash
# 执行redis命令，停止用"shutdown"
redis-cli -h [host] -p [port] [command]
# 查看key
SCAN 0 COUNT 10 MATCH * 
# 查看key总数
INFO keyspace
#redis批量导入数据 
cat 1.csv | redis-cli -h [host] -p [port] –pipe
```
