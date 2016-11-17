Title: zookeeper installation
Date: 2015-07-13 13:50
Category: database
Tags: 201507, zookeeper, hbase, hadoop 
Author: laomie
Summary: zookeeper安装

单结点(standalone mode)安装
=========================
设置环境变量
```
export ZK_HOME=/data/apache/zookeeper
export $PATH:$ZK_HOME/bin
```

设置配置文件（用默认的修改）
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data/hadoop/zookeeper
clientPort=2181
```

多结点(multinode cluster)安装
=========================
设置环境变量
```
export ZK_HOME=/data/apache/zookeeper
export $PATH:$ZK_HOME/bin
```

设置配置文件zoo.cfg（用默认的修改）
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data/hadoop/zookeeper
clientPort=2181
server.1=master:2888:3888
server.2=node1:2888:3888
server.3=noee2:2888:3888
```
注：分别各结点上安装

设置日志路径zookeeper-env.sh
```
export ZOO_LOG_DIR=$ZK_HOME/logs
```

设置myid
```
echo 1 > /data/hadoop/zookeeper/myid
```
注：分别在各结点上安装，node1为２，node2为３

