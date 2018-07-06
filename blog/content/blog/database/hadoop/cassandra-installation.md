Title: cassandra installation
Date: 2016-01-11 10:50
Category: database
Tags: 201601, cassandra
Author: laomie
Summary: cassandra安装

集群说明
=================
node0 192.168.11.82 (seed1)

node1 192.168.11.81

node2 192.168.11.83

设置".bashrc"（所有节点）
===========================
```bash
export CASSANDRA_HOME='/data/apache/cassandra'
# 系统日志路径
export CASSANDRA_LOG_DIR='/data/hadoop/cassandra/logs'
export PATH=$PATH:$CASSANDRA_HOME/bin
```

建目录（所有节点）
============================
```bash
$ mkdir -p /data/hadoop/cassandra/data
$ mkdir -p /data/hadoop/cassandra/commitlog
$ mkdir -p /data/hadoop/cassandra/logs
$ mkdir -p /data/hadoop/cassandra/saved_caches
```

设置"conf/cassandra.yaml"
===============================
```bash
cluster_name: 'CassandraCluster'

- seeds: "192.168.11.82"

# 各节点的ip
listen_address: 192.168.11.82

# 各节点的ip
rpc_address: 192.168.11.82

commitlog_directory: /data/hadoop/cassandra/commitlog

data_file_directories: 
- /data/hadoop/cassandra/data

saved_caches_directory: /data/hadoop/cassandra/saved_caches

# 多数据中心则使用
# endpoint_snitch: GossipingPropertyFileSnitch
```

启动cassandra（所有节点）
====================
```
$ cassandra -f
```

cassandra的命令和sql语句
===========================
```
$ cqlsh 192.168.11.82 9042
$ nodetool status
cqlsh:> CREATE KEYSPACE test1 WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 1 };
cqlsh:> CREATE KEYSPACE test2 WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'dc1' : 3, 'dc2' : 3};
cqlsh:> use test;
cqlsh:> CREATE TABLE users (user_id int PRIMARY KEY, fname text, lname text);
cqlsh:> INSERT INTO users (user_id, fname, lname) VALUES (1, 'john', 'smith') using TTL 60;
cqlsh:> CREATE INDEX ON users (lname);
```

cql超时问题
===============================
默认查询超10秒就超时，可将超时设置大些，在"~/.cassandra/cqlshrc"加以下内容
```
[connection]
request_timeout = 180
# request_timeout = None
```

批处理出错问题
=====================
报"Failed managing commit log segments. Commit disk failure policy is stop"，可修改以下参数
```
batch_size_warn_threshold_in_kb: 5
batch_size_fail_threshold_in_kb: 50
commit_failure_policy: ignore
```

ThreadPriorityPolicy出错问题
==============================
报"Improperly specified VM option 'ThreadPriorityPolicy=42'", 修改"conf/jvm.options"
```
# see http://tech.stolsvik.com/2010/01/linux-java-thread-priorities-workar                                                          
#-XX:ThreadPriorityPolicy=42
```

trace查询相关
=====================
1\. 用"nodetool setlogginglevel"设置日志级别，然后在"system_traces.events"和"system_traces.sessions"看日志记录

2\. 也可在cqlsh中用"tracing (on | off)"在查询中显示trace信息

数据库升级
=====================
1\. 下载新版应用，差分更新以下配置文件

* cassandra-env.sh
* cassandra-rackdc.properties
* cassandra-topology.properties
* cassandra.yaml （主要更新ip和文件目录）
* logback.xml

2\. 每个结点停止写操作
```
nodetool drain
```

3\. 每个结点停止老版的服务，启动新版的服务

4\. 如果是大版本升级，则每个结点用以下命令升级sstable
```
nohup nodetool upgradesstables &
```

参考

* <https://docs.datastax.com/en/upgrade/doc/upgrade/cassandra/upgrdCassandraDetails.html>

references
=========================
* <https://docs.datastax.com/en/cassandra/2.0/cassandra/initialize/initializeSingleDS.html>
* <http://arturmkrtchyan.com/how-to-setup-multi-node-cassandra-2-cluster>
* <https://netangels.net/knowledge-base/cassandra-multi-datacenter-setup/>
* <http://www.planetcassandra.org/blog/installing-the-cassandra-spark-oss-stack>

links
=========================
* <http://blog.threatstack.com/scaling-cassandra-lessons-learned>
