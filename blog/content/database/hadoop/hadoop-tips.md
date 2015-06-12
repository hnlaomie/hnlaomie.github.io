Title: hadoop tips
Date: 2014-11-08 13:30
Category: database
Tags: 201411, hadoop
Author: laomie
Summary: hadoop使用指南

hdfs dfs 常用命令
========================
```bash
# 生成目录
hdfs dfs -mkdir -p /user/laomie/temp

# 上传文件
hdfs dfs -copyFromLocal [-f] <localsrc> URI
hdfs dfs -put <localsrc> <dst>

# 下载文件
hdfs dfs -copyToLocal [-ignorecrc] [-crc] URI <localdst>
hdfs dfs -get [-ignorecrc] [-crc] <src> <localdst>

# 删除目录
hdfs dfs -rmr [-skipTrash] URI [URI ...]
```

hadoop无法停止
=========================
当临时目录设置到"/tmp"目录下时，因为linux定期清理临时目录，导致信息不完整，hadoop无法停止
