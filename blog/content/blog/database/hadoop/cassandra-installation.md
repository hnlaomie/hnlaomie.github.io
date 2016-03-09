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
export PATH=$PATH:$CASSANDRA_HOME/bin
```

建目录（所有节点）
============================
```bash
$ mkdir -p /data/hadoop/cassandra/data
$ mkdir -p /data/hadoop/cassandra/commitlog
```

设置"conf/cassandra.yaml"
===============================
192.168.11.82
```bash
cluster_name: 'CassandraCluster'
- seeds: "192.168.11.82"
listen_address: 192.168.11.82
rpc_address: 192.168.11.82

commitlog_directory: /data/hadoop/cassandra/commitlog
data_file_directories: 
- /data/hadoop/cassandra/data
```
192.168.11.81
```bash
cluster_name: 'CassandraCluster'
- seeds: "192.168.11.82"
listen_address: 192.168.11.81
rpc_address: 192.168.11.81

commitlog_directory: /data/hadoop/cassandra/commitlog
data_file_directories: 
- /data/hadoop/cassandra/data
```
192.168.11.83
```bash
cluster_name: 'CassandraCluster'
- seeds: "192.168.11.82"
listen_address: 192.168.11.83
rpc_address: 192.168.11.83

commitlog_directory: /data/hadoop/cassandra/commitlog
data_file_directories: 
- /data/hadoop/cassandra/data
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
cqlsh:> CREATE KEYSPACE test WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 1 };
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

trace查询相关
=====================
1\. 用"nodetool setlogginglevel"设置日志级别，然后在"system_traces.events"和"system_traces.sessions"看日志记录

2\. 也可在cqlsh中用"tracing (on | off)"在查询中显示trace信息

references
=========================
* <https://docs.datastax.com/en/cassandra/2.0/cassandra/initialize/initializeSingleDS.html>
* <http://arturmkrtchyan.com/how-to-setup-multi-node-cassandra-2-cluster>
* <http://www.planetcassandra.org/blog/installing-the-cassandra-spark-oss-stack>

links
=========================
* <http://blog.threatstack.com/scaling-cassandra-lessons-learned>
