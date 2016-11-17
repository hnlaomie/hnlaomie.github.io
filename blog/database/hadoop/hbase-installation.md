Title: hbase installation
Date: 2014-11-30 22:50
Category: database
Tags: 201411, hbase
Author: laomie
Summary: hbase安装

建目录
============================
```bash
$ hdfs dfs -mkdir -p /hbase
$ mkdir -p $HBASE_HOME/zookeeper
```

设置".bashrc"
===========================
```bash
export HBASE_HOME='/usr/local/tools/hbase'
export PATH=$PATH:$HBASE_HOME/bin
```

设置"conf/hbase-env.sh"
===============================
```bash
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export HBASE_MANAGES_ZK=true
```

设置"conf/hbase-site.xml" (for pseudo-distributed)
=========================================
```xml
<property>
    <name>hbase.rootdir</name>
    <value>hdfs://master:9000/hbase</value>
</property>
<property>
    <name>hbase.tmp.dir</name>
    <value>/usr/local/data/tools/hbase/tmp</value>
</property>
<property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
</property>
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>master</value>  
</property>
<property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/usr/local/data/tools/hbase/zookeeper</value>
</property>
<property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
</property>
```

复制hadoop的jar
============================
（注：自编译的hbase不需要）
```bash
$ cd $HBASE_HOME/lib
$ rm -fr slf4j*.jar
$ rm -fr hadoop*.jar
$ cp $HADOOP_HOME/share/hadoop/common/hadoop*.jar .
$ cp $HADOOP_HOME/share/hadoop/hdfs/hadoop*.jar .
$ cp $HADOOP_HOME/share/hadoop/mapreduce/hadoop*.jar .
$ cp $HADOOP_HOME/share/hadoop/tools/lib/hadoop*.jar .
$ cp $HADOOP_HOME/share/hadoop/yarn/hadoop*.jar .
```

修改"/etc/hosts"
=====================================
将"127.0.1.1"改为"127.0.0.1"或删除，"master"指向具体的ip

启动，停止hbase
==========================
```bash
$ start-hbase.sh
$ stop-hbase.sh
```

hbase简单操作
============================
```bash
$ hbase shell

# create a table
hbase> create 'test', 'cf'

# list information about your table
hbase> list 'test'

# put data into your table
hbase> put 'test', 'row1', 'cf:a', 'value1'
hbase> put 'test', 'row2', 'cf:b', 'value2'
hbase> put 'test', 'row3', 'cf:c', 'value3'

# scan the table for all data at once
hbase> scan 'test'

# get a single row of data
hbase> get 'test', 'row1'

# disable, enable table
hbase> disable 'test'
hbase> enable 'test'

# drop the table
hbase> drop 'test'

# exit the hbase shell
hbase> quit
```

编译源码
======================
```
mvn clean install assembly:single -DskipTests -Dmaven.javadoc.skip=true -Dhadoop-two.version=2.5.2
```

安装phoenix
============================
编译
```
mvn package -DskipTests -Dmaven.javadoc.skip=true -Dhadoop-two.version=2.5.2
```
解压安装 phoenix-assembly/target/phoenix-4.3.1.tar.gz，并将phoenix-4.3.1-server.jar复制到hbase的lib目录

references
=========================
* <https://archanaschangale.wordpress.com/2013/08/31/installing-pseudo-distributed-hbase-on-ubuntu/>
* <http://hbase.apache.org/book/quickstart.html>
* <http://www.cnblogs.com/junrong624/p/3564699.html>

