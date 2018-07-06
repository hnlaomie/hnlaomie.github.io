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
create user 'hive'@'localhost' identified by 'hive';
-- 修改用户密码
-- set password for 'hive'@'localhost' = password('hive');
grant all privileges on hive2.* to 'hive'@'localhost' with grant option;
-- 删除用户权限
revoke all privileges on *.* from 'hive'@'localhost';
drop user 'hive'@'localhost';
flush privileges;
create database hive2 default character set utf8 default collate utf8_general_ci;
alter database hive2 character set latin1;
```

复制mysql驱动，并建相关目录，初始化metadata
================================
```bash
cp mysql-connector-java-5.1.38.jar $HIVE_HOME/lib
hdfs dfs -mkdir /hive
hdfs dfs -mkdir /hive/warehouse
hdfs dfs -mkdir /hive/scratchdir
hdfs dfs -chmod 777 /hive/scratchdir
/hive/scratchdir
```

初始化元数据
```
schematool -dbType mysql -initSchema
```

新增"conf/hive-site.xml"文件
================================
```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>  
  <property>  
    <name>javax.jdo.option.ConnectionURL</name>  
    <value>jdbc:mysql://localhost/hive2?createDatabaseIfNotExist=true&amp;autoReconnect=true&amp;useSSL=false</value>  
  </property>    
<property>  
  <name>javax.jdo.option.ConnectionDriverName</name>  
  <value>com.mysql.jdbc.Driver</value>  
</property>  
<property>
  <name>hive.metastore.warehouse.dir</name>
  <value>hdfs://localhost:9000/hive/warehouse</value>
</property>
<property>
  <name>hive.exec.scratchdir</name>
  <value>hdfs://localhost:9000/hive/scratchdir</value>
</property>
<property>
  <name>hive.querylog.location</name>
  <value>/home/laomie/hive/logs</value>
</property>  
<property>  
  <name>javax.jdo.option.ConnectionDriverName</name>  
  <value>com.mysql.jdbc.Driver</value>  
</property>  
<property>  
  <name>javax.jdo.option.ConnectionUserName</name>  
  <value>hive</value>  
</property>  
<property>  
  <name>javax.jdo.option.ConnectionPassword</name>  
  <value>hive</value>  
</property> 
<property>
    <name>hive.exec.parallel</name>
    <value>true</value>
</property>
   <property>
   <name>hive.start.cleanup.scratchdir</name>
   <value>true</value>
    </property>  
 <property>
   <name>hive.server2.thrift.port</name>
   <value>10000</value>
 </property>
<property>
  <name>hive.hwi.war.file</name>
  <value>lib/hive-hwi-2.1.0.jar</value>
  <description>This sets the path to the HWI war file, relative to ${HIVE_HOME}. </description>
</property>
<property>
  <name>hive.fetch.task.conversion</name>
  <value>more</value>
</property>
<property>
  <name>hive.optimize.index.groupby</name>
  <value>true</value>
</property>
<property>
  <name>hive.join.cache.size</name>
  <value>250000</value>
</property>
<property>
  <name>hive.mapjoin.bucket.cache.size</name>
  <value>10000</value>
</property>
<property>
 <name>hive.stats.autogather</name>
 <value>false</value>
</property>
<property>
 <name>hive.mapred.reduce.tasks.speculative.execution</name>
 <value>false</value>
</property>
<property>
 <name>hive.exec.parallel.thread.number</name>
 <value>50</value>
</property>
<property>
 <name>hive.optimize.index.groupby</name>
 <value>true</value>
</property>

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

hive升级（1.2.1 to 2.1.1）
=================
升级hive代码
```bash
old_ver=1.2.1
new_ver=2.1.1
tar -zxvf apache-hive-${new_ver}-bin.tgz
mv hive hive-${old_ver}
mv apache-hive-${new_ver}-bin hive
cp hive-${old_ver}/conf/hive-env.sh hive/conf/
cp hive-${old_ver}/conf/hive-site.xml hive/conf/
cp hive-${old_ver}/conf/hive-log4j.properties hive/conf/
cp hive-${old_ver}/lib/mysql-connector-java-5.1.39.jar spark/jars/
```
备份原先版本的hive库
```bash
mysqldump -uhive -phive --default-character-set=utf8 --result-file=hive2.sql hive2
```
升级hive库
```bash
cd ${HIVE_HOME}/scripts/metastore/upgrade/mysql
mysql -uhive -phive hive2 < hive-schema-2.0.0.mysql.sql
mysql -uhive -phive hive2 < upgrade-1.2.0-to-2.0.0.mysql.sql
mysql -uhive -phive hive2 < hive-schema-2.1.0.mysql.sql
mysql -uhive -phive hive2 < upgrade-2.0.0-to-2.1.0.mysql.sql

# 用schematool升级
schematool -dbType mysql -info
schematool -dbType derby -upgradeSchemaFrom 1.2.0
```

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
```
hdfs dfs -put /data/apache/hivemall/hivemall-core-0.4.2-rc.2-with-dependencies.jar /hive/hivemall/
(注：hdfs没有hivemall目录则先建目录）
```

4. 建hivemall库
进入hive后，执行以下hive命令
```
CREATE DATABASE IF NOT EXISTS hivemall;
```

5. hivemall的配置
在 "~/.hiverc" 增加以下内容
```bash
use hivemall;
set hivevar:hivemall_jar=hdfs:///hive/hivemall/hivemall-core-0.4.2-rc.2-with-dependencies.jar;
source /data/apache/hivemall/define-all.hive;
```

问题一览
================
hive2.1.0，在运行spark，导致修改hive的metastore的版本为1.2.0，再次运行hive，报以下错误
```
MetaException(message:Hive Schema version 2.1.0 does not match metastore's schema version 1.2.0 Metastore is not upgraded or corrupt)
```
解决办法，可将元数据表"VERSION"，里的"1.2.0"改回"2.1.0"．或"hive-site.xml"做如下设置
```xml
<property>
    <name>hive.metastore.schema.verification</name>
    <value>false</value>
</property>
```
