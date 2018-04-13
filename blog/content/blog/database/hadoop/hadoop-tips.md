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

# 查看hadoop磁盘使用
hdfs dfsadmin -report

# 查看yarn的资源使用
yarn node -list

# 查看mr进程
mapred job -list
```

hadoop(spark)无法停止
=========================
当临时目录设置到"/tmp"目录下时，因为linux定期清理临时目录，导致信息不完整，hadoop无法停止

在"hadoop-env.sh"做如下设置
```
export HADOOP_PID_DIR=/data/hadoop/hdfs/pids
```

在"yarn-env.sh"做如下设置
```
export YARN_PID_DIR=/data/hadoop/hdfs/pids
```

spark类似，需要在"spark-env.sh"做如下设置
```
export SPARK_PID_DIR=/data/hadoop/spark/pids
```

hadoop的reduce任务不能启动
=============================
当磁盘剩余空间小于10%时，reduce任务无法启动

hadoop升级
=========================
备份
```
# 进入安全模式
hdfs dfsadmin -safemode enter
# fsck检查
hdfs fsck / -files -blocks -locations |grep -v -E '^.' > /home/hadoop/dfs-v-old-fs
# 离开安全模式
hdfs dfsadmin -safemode leave
# 关闭history-server
mr-jobhistory-daemon.sh stop historyserver
# 关闭集群
$HADOOP_HOME/sbin/stop-all.sh
# 备份namenode信息（fs.default.name）
cp -r /home/hadoop/hdfs/namenode/ /home/hadoop/backup
```
namenode升级
```
start-dfs.sh –upgrade
# hadoop-daemon.sh start namenode -upgrade
```
* <http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-hdfs/HdfsRollingUpgrade.html>
* <https://www.zybuluo.com/layor/note/162019>
* <http://wiki.apache.org/hadoop/Hadoop_Upgrade>
* <http://www.cnblogs.com/JavaSmart/p/4567173.html>
* <http://www.michael-noll.com/blog/2011/08/23/performing-an-hdfs-upgrade-of-an-hadoop-cluster/>

升级后的hadoop删文件不减空间，这是因为升完级，稳定运行后，需要运行以下完成升级的命令并重启namenode
```
hdfs dfsadmin -finalizeUpgrade
```
* <http://stackoverflow.com/questions/23058229/deleting-files-from-hdfs-does-not-free-up-disk-space>

查看逻辑cpu个数
====================
```
grep "^processor" /proc/cpuinfo | wc -l
```

datanode增加磁盘
======================
修改"hdfs-site.xml"
```
<property>
    <name>dfs.datanode.data.dir</name>
    <value>/data/hadoop/dfs/data,/data1/hadoop/dfs/data</value>
</property>
```
用以下命令重启datanode
```bash
yarn-daemon.sh stop nodemanager
hadoop-daemon.sh stop datanode
hadoop-daemon.sh start datanode
yarn-daemon.sh start nodemanager
```
