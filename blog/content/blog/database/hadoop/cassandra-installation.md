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

references
=========================
* <https://docs.datastax.com/en/cassandra/2.0/cassandra/initialize/initializeSingleDS.html>
* <http://arturmkrtchyan.com/how-to-setup-multi-node-cassandra-2-cluster>
* <http://www.planetcassandra.org/blog/installing-the-cassandra-spark-oss-stack>
