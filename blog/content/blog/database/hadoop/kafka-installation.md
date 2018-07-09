Title: kafka installation
Date: 2015-12-18 09:50
Category: database
Tags: 201512, kafka, zookeeper, hadoop 
Author: hnlaomie
Summary: kafka安装

代码编译
=================
```
gradle
gradle releaseTarGzAll
```
安装包在./core/build/distributions/

安装
=========================
[安装zookeeper](http://hnlaomie.github.io/posts/2015/07/zookeeper-installation/)，然后修改"config/server.properties"设置zookeeper
```
zookeeper.connect=localhost:2181
```

在"bin/kafka-run-class.sh"中修改日志路径（否则日志将写到安装路径的"logs"目录）
```
LOG_DIR="/data/hadoop/kafka/log4j/logs"
```

快速指南
=======================
```
# 启动
kafka-server-start.sh config/server.properties &
# 停止
kafka-server-stop.sh config/server.properties
# 新建主题
kafka-topics.sh --create --zookeeper 192.168.11.82:2181 --replication-factor 2 --partitions 18 --topic test
# 显示主题
kafka-topics.sh --list --zookeeper 192.168.11.82:2181
# 查看主题
kafka-topics.sh --describe --zookeeper 192.168.11.82:2181 --topic test 
# 修改数据保存时间
kafka-configs.sh --zookeeper 192.168.11.82:2181 --alter --entity-type topics --entity-name test --add-config retention.ms=86400000
# 发送消息
kafka-console-producer.sh --broker-list 192.168.11.82:9092 --topic test
# 接收消息
kafka-console-consumer.sh --bootstrap-server 192.168.11.81:9092,192.168.11.82:9092 --topic test --from-beginning
kafka-avro-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning
# consumer group
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group groupid --describe
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group groupid --delete
kafka-run-class.sh kafka.admin.ConsumerGroupCommand --group onlineKafka --zookeeper node3:2181 --describe
# 启动schema-registry,需要confluent
schema-registry-start ./etc/schema-registry/schema-registry.properties
# Start Connect in distributed mode. Run this command in its own terminal.
$ connect-distributed ./etc/schema-registry/connect-avro-distributed.properties
# start ksql
$ ksql-server-start ./etc/ksql/ksql-server.properties
```

kafka和spark整合
==============================
```
cd $SPARK_HOME
bin/run-example org.apache.spark.examples.streaming.KafkaWordCountProducer localhost:9092 test 3 5
bin/run-example org.apache.spark.examples.streaming.KafkaWordCount localhost:2181 test-consumer-group test 1
bin/run-example org.apache.spark.examples.streaming.DirectKafkaWordCount localhost:9092 test
```

idea开发环境设置
==============================
kafka, spark streaming相关例子，只需启动kafka，然后在运行环境里做如下设置
```
VM options: -Dspark.master=local[2]
Environment variables: MASTER=localh[2]
```
scala2.10使用jkd7，gradle需做以下设置兼容jdk7
```
sourceCompatibility = "1.7"
targetCompatibility = "1.7"
```

删除主题内容
=================================
设置server.properties
```
delete.topic.enable=true
```
用以下命令删除内容
```bash
bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic test
# 从zookeeper删除
bin/zkCli.sh # and delete the topics using 
rmr /brokers/topics/<<topic>> and rmr /admin/delete_topics/<<topic>>
```

新版本"kafka-console-consumer.sh"不能用"--bootstrap-server"
==========================================================
需要删除zookeeper上老版本的"/brokers"目录，然后让新版本重建目录

1. Delete all topics and stop kafka brokers

2. Connect to zookeeper cluster and remove "/brokers" z node

3. Restart kafka brokers

复制错误处理
======================
报"Number of alive brokers '1' does not meet the required replication factor"，需要设置"server.properties"
```
offsets.topic.replication.factor=1
```

跨集群复制
=======================
低版本集群数据复制到高版本集群，低版本应用能读写高版本集群，反之不行

消费低版本集群的配置"consumer.properties"
```
zookeeper.connect=node3:2181,node6:2181,node7:2181
group.id=onlineKafka
auto.offset.reset=smallest
partition.assignment.strategy=roundrobin
```
往高版本集群产生数据的配置"producer.properties"
```
bootstrap.servers=192.168.11.81:9092,192.168.11.82:9092,192.168.11.83:9092
```
复制主题内容的命令
```
$KAFKA_HOME/bin/kafka-mirror-maker.sh --consumer.config consumer.properties --producer.config producer.properties --num.streams=2 --whitelist "test"
```

kafka升级
========================
kafka upgrade from 1.0.0 to 1.1.0

1. add follow content to config/server.properties
```
# for upgrade
inter.broker.protocol.version=1.0
message.format.version=1.0
```

2. stop broker from 1 to n and upgrade

3. Once the entire cluster is upgraded, bump the protocol version by editing inter.broker.protocol.version and setting it to 1.1.

4. restart the brokers one by one for the new protocol version to take effect.

5. Once the entire client is upgraded, bump the protocol version by editing message.format.version and setting it to 1.1.

6. restart the brokers one by one.


参考
===============================
* <http://blog.jobbole.com/99195/>
