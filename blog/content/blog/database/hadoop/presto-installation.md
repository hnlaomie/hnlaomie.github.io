Title: presto installation
Date: 2016-09-28 17:50
Category: database
Tags: 201609, presto, hadoop 
Author: hnlaomie
Summary: presto安装

安装
=========================
下载(https://repo1.maven.org/maven2/com/facebook/presto/presto-server/0.152.3/presto-server-0.152.3.tar.gz "presto-server-0.152.3.tar.gz")并解压安装，设置环境变量"PRESTO_HOME"

下载(https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.152.3/presto-cli-0.152.3-executable.jar "presto-cli-0.152.3-executable.jar")，并将它重命名为"presto"，设置为可执行，放在"$PRESTO_HOME/bin"下，并把"$PRESTO_HOME/bin"加到环境变量"PATH"

配置
=========================
etc/node.properties
```
node.environment=production
node.id=laomie-pc
node.data-dir=/home/laomie/tools/data/presto
```
etc/jvm.config
```
-server
-Xmx2G
-XX:+UseG1GC
-XX:G1HeapRegionSize=32M
-XX:+UseGCOverheadLimit
-XX:+ExplicitGCInvokesConcurrent
-XX:+HeapDumpOnOutOfMemoryError
-XX:OnOutOfMemoryError=kill -9 %p
```
etc/config.properties
```
coordinator=true
node-scheduler.include-coordinator=true
http-server.http.port=8090
query.max-memory=2GB
query.max-memory-per-node=1GB
discovery-server.enabled=true
discovery.uri=http://localhost:8090
```
etc/log.properties
```
com.facebook.presto=INFO
```
etc/catalog/jmx.properties
```
connector.name=jmx
```
etc/catalog/cassandra.properties
```
connector.name=cassandra
cassandra.contact-points=laomie-pc
cassandra.native-protocol-port=9042
```

快速指南
=======================
```
# 启动
$PRESTO_HOME/bin/launcher run
# 后台进程启动
$PRESTO_HOME/bin/launcher start
# 停止
$PRESTO_HOME/bin/launcher stop
# 访问cassandra数据库
$PRESTO_HOME/bin/presto --server localhost:8090 --catalog cassandra --schema dmp
# 输出csv
$PRESTO_HOME/bin/presto --server localhost:8090 --catalog cassandra --schema dmp --execute "select * from user_tags limit 20" --output-format CSV > user_tags.csv
```

无法通过rpc访问cassandra
=================================
需要在cassandra.yaml中打开相关设置
```
start_rpc: true
```

连接远程hive
===========================
远程hive(192.168.11.61)启动metastore服务
```
hive --service metastore
```
在presto的catalog目录放连接文件hive_online.properties
```
connector.name=hive-hadoop2
hive.metastore.uri=thrift://192.168.11.61:9083
```

参考
===============================
* <https://prestodb.io/docs/current/>
