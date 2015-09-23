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

hive1.1的jline问题
=======================
删除$HADOOP_HOME/share/hadoop/yarn/lib/jline-0.9.94.jar

mysql日志写入问题
=============================
ubuntu在/etc/mysql/my.cnf加入以下内容
```bash
[mysqld]                                                                                                                
# for hive                                                                                                              
binlog_format=mixed 
```

hive导出csv，默认用"\t"分隔数据
=====================
```bash
INSERT OVERWRITE LOCAL DIRECTORY '/home/laomie/init.csv' ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' select * from android_init

hive --database game -e 'select * from android_init' | sed 's/[[:space:]]\+/,/g' > /home/laomie/init.csv
```

hive导出表结构
=====================
```bash
hive --database game -e 'show create table android_init' > android_init.sql
```

hive的slf4j
====================
```
rm -fr $HADOOP_HOME/share/hadoop/common/lib/slf4j*
mv $HBASE_HOME/lib/slf4j* $HADOOP_HOME/share/hadoop/common/lib
jar -xvf $SPARK_HOME/lib/spark-assembly*.jar
# delete slf4j path and zip the directory
jar -cvf $SPARK_HOME/lib/spark-assembly-1.4.0-hadoop2.6.0.jar $SPARK_HOME/lib/spark-assembly-1.4.0-hadoop2.6.0/
```

hive的timestamp时间差
=========================
```
CREATE TABLE ts (txt string, st Timestamp, et Timestamp) 
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';

select 
  txt, 
  cast(
    round(
      cast((e-s) as double) * 1000
    ) as int
  ) latency 
from (select txt, cast(st as double) s, cast(et as double) e from ts) q;
```
