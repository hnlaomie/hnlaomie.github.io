Title: hive tips 
Date: 2016-07-01 13:00
Category: database
Tags: 201607, hadoop, hive
Author: laomie
Summary: hive使用指南

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

hive删除表
===========================
```
truncate table table_name;
truncate table table_name partition (dt = '2015-12-02');
alter table table_name drop if exists partition (dt = '2015-12-02');
```

变更机器名
======================
因为hive的metadata中包括机器名，当机器名变更后，需要改metadata里的机器名。可用以下命令查找并修改机器名
```
# 查找表的hdfs路径（包括机器名）
hive> describe formatted [tablename];
# 修改表的hdfs路径
hive> alter table [tablename] set location "hdfs://[newhostname]:8020/user/hive/warehouse/[tablename]"; 
```

建外部表
=====================
将spark的dataframe保存orc文件
```
df.write.mode(SaveMode.Overwrite).format("orc").save("/user/laomie/country")
```
hive用外部表关联orc文件
```
CREATE EXTERNAL TABLE country_temp (country_id int, country string, last_update timestamp)
STORED AS ORC LOCATION "/user/laomie/country";
```

移动表
==============
```
alter table table_name rename to new_database.table_name
```

变更列
==================
```
ALTER TABLE table_name CHANGE old_col_name new_col_name new_data_type
-- 变更分区列
ALTER TABLE {table_name} PARTITION COLUMN ({column_name} {column_type});
MSCK REPAIR TABLE <tablename>;
```

Hive在spark2.1启动时无法访问../lib/spark-assembly-*.jar
=================
编辑$HIVE_HOME/bin/hive
```
sparkAssemblyPath=`ls ${SPARK_HOME}/jars/spark-*.jar`
```

Hive连接远程metastore
=================
node2结点编辑$HIVE_HOME/conf/hive-site.xml
```
    <property>
       <name>hive.metastore.uris</name>
       <value>thrift://node1:9083</value>
    </property>
```
node1结点启动服务
```
hive --service metastore
```

hive删除空分区
===================
需要将分区列转为字符型才能删除
```
ALTER TABLE ssp_log_data PARTITION COLUMN (log_hour STRING);
ALTER TABLE ssp_log_data DROP PARTITION (log_hour='__HIVE_DEFAULT_PARTITION__');
ALTER TABLE ssp_log_data PARTITION COLUMN (log_hour INT);
```
