Title: redis installation
Date: 2018-04-13 14:30
Category: database 
Tags: 201804, redis 
Author: laomie
Summary: redis 集群安装

安装redis
==================
下载并编译redis，编译完成后会在src目录下生成Redis服务端程序redis-server和客户端程序redis-cli
```
wget http://download.redis.io/releases/redis-4.0.9.tar.gz
tar zxvf redis-4.0.9.tar.gz
cd redis-4.0.9
make
```

安装ruby
=================
通过"rbenv"安装ruby和相关依赖包
```
gem install redis
```

设置redis和ruby
======================
将ruby和redis加入系统路径
```
# rbenv
export RBENV_ROOT="/data/apache/tools/rbenv"
export PATH="$RBENV_ROOT/bin:$RBENV_ROOT/plugins/ruby-build/bin:$PATH"
eval "$(rbenv init -)"

# redis
export REDIS_HOME="/data/apache/tools/redis-4.0.9/src"
export PATH="$REDIS_HOME:$PATH"
```

集群说明
====================
在"192.168.11.61"和"192.168.11.62"两台机的三个端口"6386,6387,6388"启动redis

redis配置
====================
在集群机器建不同端口的redis配置文件
```
mkdir -p /data/hadoop/redis/config/6386
mkdir -p /data/hadoop/redis/config/6387
mkdir -p /data/hadoop/redis/config/6388
```

配置文件"redis-6386.conf"内容如下
```
# redis后台运行
daemonize yes

port 6386
pidfile  /var/run/redis_6386.pid

# 开启集群
cluster-enabled yes
# 集群的配置文件首次启动自动生成
cluster-config-file nodes-6386.conf
# 请求超时，默认15秒
cluster-node-timeout 15000

# aof日志开启  有需要就开启，它会每次写操作都记录一条日志
appendonly yes
# 可以指定日志的目录
logfile "/data/hadoop/redis/data/6386/redis-6386.log"

# 关闭保护模式
protected-mode no
```

redis启动
================
到每个配置文件目录下启动redis
```
cd /data/hadoop/redis/config/6386
redis-server redis-6386.conf
```

redis集群启动，利用redis-trib创建cluster的操作，只需要一次即可，假设系统关机，把所有6个节点全关闭后，下次重启后，即自动进入cluster模式，不用再次redis-trib.rb create。
=======================
```
redis-trib.rb create --replicas 1 192.168.11.61:6386 192.168.11.61:6387 192.168.11.61:6388 192.168.11.62:6386 192.168.11.62:6387 192.168.11.62:6388
```

redis验证
===================
查看进程，端口，集群
```
ps aux | grep redis
ps -ef | grep 6386
redis-trib.rb check 192.168.11.61:6386
```

集群扩容，删除
=========================
```
# 增加主节点
redis-trib.rb add-node [host]:[port]
# 为新增主节点分配slots
redis-trib.rb reshard [host]:[port]
# 增加从节点
redis-trib.rb add-node --slave --master-id $[nodeid] 192.168.210.128:7007 192.168.210.128:7000
# 删除节点
redis-trib.rb reshard 192.168.210.128:7002
edis-trib del-node 192.168.210.128:7002  ${node-id}
```

参考
============
http://www.cnblogs.com/wlandwl/p/rediscluster.html
http://blog.csdn.net/huwh_/article/details/79242625
https://redis.io/topics/cluster-tutorial
