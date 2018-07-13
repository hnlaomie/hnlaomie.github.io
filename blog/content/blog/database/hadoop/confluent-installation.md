Title: confluent installation
Date: 2018-07-12 19:50
Category: database
Tags: 201807, kafka, zookeeper, confluent
Author: hnlaomie
Summary: confluent安装

环境变量,数据目录设置
====================
```
export CONFLUENT_CURRENT=/data/hadoop
export CONFLUENT_HOME=/data/apache/confluent
export PATH=$PATH:$CONFLUENT_HOME/bin
```
生效后用以下命令查看confluent数据目录,并生成kafka, zookeeper数据目录
```
confluent current
# confluent.ljzXwHuB为以上命令生成的目录
mkdir -p /data/hadoop/confluent.ljzXwHuB/zookeeper/data
mkdir -p /data/hadoop/confluent.ljzXwHuB/kafka/data
echo 1 > /data/hadoop/confluent.ljzXwHuB/zookeeper/data/myid
```

zookeeper配置
=======================
vim /data/apache/confluent/etc/kafka/zookeeper.properties
```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5

dataDir=/data/hadoop/confluent.ljzXwHuB/zookeeper/data
# the port at which the clients will connect
clientPort=2181

# the maximum number of client connections.
# increase this if you need to handle more clients
maxClientCnxns=60

server.1=192.168.11.82:2888:3888
server.2=192.168.11.81:2888:3888
server.3=192.168.11.83:2888:3888

autopurge.snapRetainCount=3
autopurge.purgeInterval=24
```

kafka配置
==============================
vim /data/apache/confluent/etc/kafka/server.properties
```
broker.id=1

listeners=PLAINTEXT://192.168.11.82:9092

log.dirs=/data/hadoop/confluent.ljzXwHuB/kafka/data

num.partitions=12

log.retention.bytes=-1
log.cleanup.policy=delete

zookeeper.connect=192.168.11.82:2181,192.168.11.81:2181,192.168.11.83:2181

# delete topics
delete.topic.enable=true
```

查看zookeeper状态
==============================
利用nc命令查看
```
# centos
yum install nmap
# debian
apt-get install netcat
# zookeeper leader or fellower
echo stat | nc 192.168.1.20 2181 | grep Mode
```

schema-registry配置
===========================
修改配置文件"schema-registry.properties",以下为常用命令
```
# List all subjects
curl -X GET http://localhost:8081/subjects

# Deletes all schema versions registered under the subject "Kafka-value"
curl -X DELETE http://localhost:8081/subjects/Kafka-value
```

connect配置
==========================
修改配置文件"connect-avro-distributed.properties",以下为常用命令
```
# start connect
confluent start connect
# list connectors
confluent list connectors
# load hdfs sink connect (place one config file in kafka-connect-hdfs)
confluent load hdfs-sink -d etc/kafka-connect-hdfs/test-hdfs.properties
# unload hdfs sink
confluent unload hdfs-sink
# connector's status
confluent status connectors
```
test-hdfs.properties配置
```
name=hdfs-sink
connector.class=io.confluent.connect.hdfs.HdfsSinkConnector
tasks.max=1
topics=test_hdfs
hdfs.url=hdfs://localhost:9000
flush.size=2
partitioner.class=io.confluent.connect.hdfs.partitioner.FieldPartitioner
partition.field.name=log_date,log_hour
#format.class=io.confluent.connect.hdfs.parquet.ParquetFormat
#hive.integration=true
#hive.metastore.uris=thrift://localhost:9083
#schema.compatibility=BACKWARD
#hive.database=test
```
hive外部表
```
-- 生成的avro文件自带schema
CREATE EXTERNAL TABLE `test_hdfs`(
  `req_id` string,
  `ip` string, 
  `create_date` int
)
PARTITIONED BY (
  `log_date` int,
  `log_hour` int
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.avro.AvroSerDe'
STORED AS AVRO
LOCATION '/topics/test_hdfs/'

-- 可采取一些两种方式添加分区,添加后才能查询
msck repair table test_hdfs;
alter table test_hdfs add partition (log_date=20180712, log_hour=1);
```

参考
===============================
* <https://docs.confluent.io/current/zookeeper/deployment.html#supported-version>
* <https://github.com/confluentinc/kafka-connect-hdfs/blob/master/docs/configuration_options.rst>
* <https://engineering.pandora.com/creating-a-data-pipeline-with-the-kafka-connect-api-from-architecture-to-operations-56715080ac55>
* <https://resources.zaloni.com/blog/partitioning-in-hive>

