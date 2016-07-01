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
bin/zookeeper-server-start.sh config/zookeeper.properties &
# 停止
bin/zookeeper-server-stop.sh config/zookeeper.properties
# 新建主题
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
# 显示主题
bin/kafka-topics.sh --list --zookeeper localhost:2181
# 发送消息
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
# 接收消息
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
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
```

参考
===============================
* <http://blog.jobbole.com/99195/>
