Title: hbase tips
Date: 2015-07-14 10:50
Category: database
Tags: 201507, hbase
Author: laomie
Summary: hbase使用指南

hbase优化
========================
术语
```
Memory type	                               Abbr
Physical memory available for HBase RS	   Total
Memory size for memstore	               MSize
Memory for block cache (L1) cache	       L1Size
Memory for JVM related components	       JHSize
```

hbase_env.sh HBASE_REGIONSERVER_OPTS
```
-Xmx                        Xmx = MSize + L1Size + JHSize
-XX:MaxDirectMemorySize     DMen = Total - Xmx
```

hbase_site.xml
```
hbase.regionserver.global.memstore.size              ULimit = MSize / Xmx
hfile.block.cache.size                               blksz = 0.8 - ULimit
hbase.bucketcache.size                               bucsz = DMen + (blksz * Xmx)
hbase.bucketcache.percentage.in.combinedcache        ccsz = 1 - ((blksz * Xmx) / bucsz)
hbase.bucketcache.ioengine                           ioeng = offheap or file:/localfile
```

用例
```
Total      20480
MSize      1024 
L1Size     1024
JHSize     512
Xmx        2560       
DMen       17920
ULimit     0.4
blksz      0.4
bucsz      18944
ccsz       0.946
ioeng      offheap
```

* <http://www.slideshare.net/bijugs/h-base-performance>
* <http://blog.asquareb.com/blog/2014/11/24/how-to-leverage-large-physical-memory-to-improve-hbase-read-performance/>
* <http://hadoop-hbase.blogspot.com/2013/01/hbase-region-server-memory-sizing.html/>

phoenix 启动异常"org.apache.phoenix.exception.PhoenixIOException: SYSTEM.CATALOG"
===================================
重装hbase后，需要清空zookeeper内的结点，否则导致上述错误。
