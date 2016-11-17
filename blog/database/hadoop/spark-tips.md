Title: spark tips
Date: 2016-01-09 16:30
Category: database
Tags: 201601, spark
Author: laomie
Summary: spark使用指南

understanding closures
========================
Don't do this
```scala
// this runs in the driver
val foo = new SomeExpensiveNotSerializableThing
someRdd.map { x =>
    // this runs in the executor, so...
    // the attempt to close over foo will throw NotSerializableException
    foo.mangle(x)
}
```
Don't do this either
```scala
someRdd.map { x =>
    // this runs in the executor, ok...
    // but gets constructed for every element in the RDD, so will be slow
    val foo = new SomeExpensiveNotSerializableThing
    foo.mangle(x)
}
```
Do this instead
```scala
someRdd.mapPartitions { part =>
    // this runs in the executor, constructed only once per group of elements
    val foo = new SomeExpensiveNotSerializableThing
    part.map { x =>
        // then used for each element
        foo.mangle(x)
    }
}
```
If you encounter other closure-related issues, make sure that
```
all captured references are serializable
all captured references are immutable
all captured references are either local vals, or in a static object
```
As a specific example, don't extend App:
```
object MyApp extends App {
  // start running
```
Use an explicit main method instead
```
object MyApp {
  def main(args: Array[String]) {
    // start running
```

understanding partitions
===========================
* If data is partitioned into fewer tasks than there are executor cores, some cores may be idle
* If there are many more tasks than cores, scheduler delay may be larger than work (delay is visible in the per-stage details link)
* General rule of thumb, at least as many tasks as 2 * total number of executor cores
* Set spark.default.parallelism, pass # of partitions to methods that accept it
* Increase # partitions with rdd.repartition (which triggers a shuffle, so is costly), decrease with rdd.coalesce
* If partitions are uneven in size, one executor may get overloaded
* Common cause of unevenness is doing a grouping operation on an RDD where many keys are None; in this case, filter them out first
* As always, MEASURE. There may be good reasons for a small or large # of partitions.

understanding shuffle
=============================
* Whenever data needs to be redistributed among executors, a shuffle occurs
* This typically occurs for by-key operations on PairRDD: groupBy, reduceByKey, etc.
* Shuffle involves writing data to disk, then reading off of other executors' disks over the network.
* Memory hierarchy 101: disk and network are slower than cache and ram, so shuffle is slow
* Speeding things up requires either writing less data, or triggering fewer shuffles

write less data
====================
* set spark.serializer = org.apache.spark.serializer.KryoSerializer
* If you're using groupBy in order to do an aggregation you're probably doing it wrong.
* reduceByKey, combineByKey, aggregateByKey do work before the shuffle, so they potentially write less data
* Similar to combiners in hadoop

trigger fewer shuffles
===========================
If an rdd does not have a partitioner, and you do a transformation that requires it to be partitioned a certain way, it will trigger a shuffle. Cassandra RDDs do not have partitioners, and grouping operations on them (even when grouping on the rowkey) trigger a shuffle:
```
scala> val visits = sc.cassandraTable[(String, Long)]("test", "user_visits").
select("user", "utc_millis")
scala> visits.partitioner
res0: Option[org.apache.spark.Partitioner] = None
scala> visits.groupByKey.partitioner
res3: Option[org.apache.spark.Partitioner] = Some(org.apache.spark.HashPartitioner@1)
```
Some transformations lose the partitioner, so avoid them after a shuffle:
```
scala> visits.groupByKey.map(x => x).partitioner
res4: Option[org.apache.spark.Partitioner] = None
scala> visits.groupByKey.mapValues(x => x).partitioner
res5: Option[org.apache.spark.Partitioner] = Some(org.apache.spark.HashPartitioner@1)
```
Even though spark has no way of knowing it (yet), Cassandra RDDs are partitioned such that a given row of values for a rowkey will all be in the same partition, in order. 
```
// So instead of doing
visits.reduceByKey { value =>
    SomePerItemAggregationFunction(value)
}
// You can do
visits.mapPartitions { lotsOfValues =>
    SomePerPartitionAggregationFunction(lotsOfValues)
}
```
which will result in a map-only stage, instead of a shuffle. See GroupingExample.scala vs PartitionGroupingExample.scala for details

Joins require a shuffle, so are not particularly efficient. This is true even if both RDDs are from cassandra tables with the same rowkey, and the join is on the rowkey.

If one of the RDDs is significantly smaller than the other, you may be better off collecting it to the driver, converting it to a hashmap, then re-broadcasting it to every executor. Then the join can be performed as a map-only stage against the larger RDD.

See JoinExample.scala vs BroadcastJoinExample.scala for details

references
=======================
<http://koeninger.github.io/spark-cassandra-example>
