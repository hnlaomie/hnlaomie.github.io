Title: hive installation
Date: 2014-08-04 21:00
Category: database
Tags: 201408, hadoop, hive
Author: laomie
Summary: hive安装

编译hive
=========================
```
mvn clean install -Phadoop-2,dist -DskipTests
```

环境变量设置
=========================
在"hduser“用户路径下的".bashrc”里加入以下内容
```bash
export HIVE_HOME='/home/hduser/tools/hive0.13'
export PATH=$PATH:$HIVE_HOME/bin
```

设置mysql，用于保存hive的元数据
===================================
```
-- 匹配所有用"%"，例如"192.168.1.%"
create user 'hadoop'@'localhost' identified by 'hadoop';
-- 修改用户密码
-- set password for 'hadoop'@'localhost' = password('hadoop');
grant all privileges on *.* to 'hadoop'@'localhost' with grant option;
-- 删除用户权限
revoke all privileges on *.* from 'hadoop'@'localhost';
drop user 'hadoop'@'localhost';
flush privileges;
create database hadoop2 default character set utf8 default collate utf8_general_ci;
alter database hadoop2 character set latin1;
```

复制mysql驱动，并建相关目录
================================
```bash
mkdir -p $HIVE_HOME/warehouse
mkdir -p $HIVE_HOME/auxlib
cp mysql-connector-java-5.1.31.jar $HIVE_HOME/auxlib
```

新增"conf/hive-site.xml"文件
================================
```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
    <name>hive.metastore.schema.verification</name>
    <value>false</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost:3306/hadoop2?createDatabaseIfNotExist=true</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hadoop</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hadoop</value>
</property>
<property>
    <name>hive.metastore.warehouse.dir</name>
    <value>file:/home/hduser1/tools/hive0.13/warehouse</value>
</property>
<property>
    <name>hive.server2.thrift.port</name>
    <value>10000</value>
</property>
<property>
    <name>hive.server2.thrift.min.worker.threads</name>
    <value>5</value>
</property>
<property>
    <name>hive.server2.thrift.max.worker.threads</name>
    <value>500</value>
</property>
<property>
    <name>hive.aux.jars.path</name>
    <value>file:/home/hduser/tools/hive0.13/auxlib</value>
</property>

<!--
<property>
    <name>hive.support.concurrency</name>
    <value>true</value>
</property>
-->

</configuration>

```
mysql表数据导出csv
============================
```sql
select *
from employs
into outfile '/home/hduser/tools/data/employs.csv'
fields terminated by '\t'
lines terminated by '\n'
```

hive建表导入数据
=======================
```
create table employs(empid int, name string, surname string)
row format delimited
fields terminated by '\t';
load data local inpath '/home/hduser/tools/data/employs.csv' overwrite into table employs;
```

hive启动
=====================
```bash
$ hive --service hiveserver2
$ netstat -anp | grep 10000
```
jdbc连接时，用户用hadoop相关用户，否则有安全方面的异常

hivemall安装
==========================
1. 编译代码
```bash
mvn clean install -DskipTests=true
```
2. 到"https://github.com/myui/hivemall/releases"下载以下软件
hivemall-core-0.4.2-rc.2-with-dependencies.jar
define-all.hive

3. 将软件放在"/data/apache/hivemall/"目录，并用以下命令将jar上传hdfs
hdfs dfs -put /data/apache/hivemall/hivemall-core-0.4.2-rc.2-with-dependencies.jar /hive/hivemall/
(注：hdfs没有hivemall目录则先建目录）

4. 建hivemall库
进入hive后，执行以下hive命令
CREATE DATABASE IF NOT EXISTS hivemall;

5. hivemall的配置
在 "~/.hiverc" 增加以下内容
use hivemall;
set hivevar:hivemall_jar=hdfs:///hive/hivemall/hivemall-core-0.4.2-rc.2-with-dependencies.jar;
source /data/apache/hivemall/define-all.hive;


