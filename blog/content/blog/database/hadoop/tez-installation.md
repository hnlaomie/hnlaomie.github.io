Title: tez installation
Date: 2016-12-01 09:50
Category: database
Tags: 201612, tez, hadoop, hive 
Author: hnlaomie
Summary: tez安装

代码编译
=================
修改代码里最顶层的"pom.xml"，将hadoop版本改为和运行的一致，注掉node.js相关代码
```
<hadoop.version>2.7.3</hadoop.version>

<!--
    <module>tez-ui</module>
    <module>tez-ui2</module>
-->
```
编译代码（需要预先编译Protocol Buffers 2.5.0）
```
mvn clean package -DskipTests=true -Dmaven.javadoc.skip=true
```

安装
=========================
在hdfs建目录，并将tez复制到新建的目录
```
hdfs dfs -mkdir /apps/tez
hdfs dfs -copyFromLocal tez-dist/target/tez-0.8.4.tar.gz /apps/tez/
```
所有结点本地安装tez
```
mkdir -p /data/apache/tez
tar -xvzf tez-dist/target/tez-0.8.4-minimal.tar.gz -C /data/apache/tez
```

环境变量设置（所有结点）
================
~/.bashrc设置"TEZ_HOME"和"TEZ_CONF_DIR"
```
export TEZ_HOME=/data/apache/tez
export TEZ_CONF_DIR=$TEZ_HOME/conf
```
$HADOOP_HOME/etc/hadoop/hadoop-env.sh设置"HADOOP_CLASSPATH"
```
export HADOOP_CLASSPATH=${TEZ_CONF_DIR}:${TEZ_HOME}/*:${TEZ_HOME}/lib/*
```

hadoop, tez, hive配置
=====================
$HADOOP_HOME/etc/hadoop/mapred-site.xml设置"mapreduce.framework.name"
```xml
<property>
    <name>mapreduce.framework.name</name>
    <!--<value>yarn</value>-->
    <value>yarn-tez</value>
</property>
```
$HIVE_HOME/conf/hive-site.xml增加以下设置
```xml
<property>
   <name>hive.execution.engine</name>
   <value>tez</value>
</property>
<property>
   <name>hive.prewarm.enabled</name>
   <value>true</value>
</property>
<property>
    <name>hadoop.embedded.local.mode</name>
    <value>false</value>
</property>
```
$TEZ_HOME/conf/tez-site.xml设置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>tez.version</name>
        <value>tez-0.8.4</value>
    </property>
    <property>
        <name>tez.use.cluster.hadoop-libs</name>
        <value>true</value>
    </property>
    <property>
        <name>tez.lib.uris</name>
        <value>${fs.defaultFS}/apps/tez/${tez.version}.tar.gz</value>
    </property>
</configuration>
```
将$TEZ_HOME/conf/tez-site.xml复制到所有结点的"$HADOOP_HOME/etc/hadoop"目录

tez测试
==============
运行测试例子
```
hadoop jar $TEZ_HOME/tez-examples.jar orderedwordcount <input> <output>
hadoop jar $TEZ_HOME/tez-tests.jar testorderedwordcount <input1> <output1> <input2> <output2> ...
```

问题一览
===========
1. spark用yarn跑时，找不着tez，可将$SPARK_HOME/conf/hive-site.xml的tez改为mr方式
```xml
<property>
   <name>hive.execution.engine</name>
   <value>mr</value>
</property>
```
脚本中用以下方式运行spark-submit
```bash
files="/data/apache/spark/conf/hive-site.xml"
spark-submit --files ${files}
```

参考
===============================
* <http://tez.apache.org/install.html>
