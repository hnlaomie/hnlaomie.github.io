Title: spark installation
Date: 2014-08-27 17:00
Category: database
Tags: 201408, spark 
Author: laomie
Summary: spark安装

maven编译
================
```bash
export MAVEN_OPTS="-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"
mvn -Pyarn -Phadoop-2.7 -Dhadoop.version=2.7.3 -Phive -Phive-thriftserver -Psparkr -DskipTests clean package
```

sbt编译
======================
```bash
SPARK_HADOOP_VERSION=2.7.3 SPARK_YARN=true SPARK_HIVE=true sbt/sbt clean assembly
```

生成spark部署包
```bash
export MAVEN_OPTS="-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"
./dev/make-distribution.sh --tgz -Pyarn -Phadoop-2.7 -Dhadoop.version=2.7.3 -Phive -Phive-thriftserver -Psparkr -DskipTests
```

intellij调试spark代码
=============================
安装jdk8，scala2.11，sbt0.13并在"~/.bashrc"设置相关环境变量
```bash
export SCALA_HOME=/home/laomie/tools/scala-2.11
export SBT_HOME=/home/laomie/tools/sbt
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export PATH=$PATH:$JAVA_HOME/bin:$SCALA_HOME/bin:$SBT_HOME/bin
```

intellij安装"scala"插件

下载spark代码
```bash
git clone https://github.com/apache/spark.git
```

在intellij导入spark代码
  选择File->Import Project, 在弹出的窗口中指定spark源码目录
  选择项目类型为sbt project，然后点击next
  在新弹出的窗口中先选中"Use auto-import",然后点击Finish

依赖项设置
  选择File->Project Structure->Modules 
  streaming-twitter, streaming-kafka, streaming-flume, streaming-mqtt, streaming-zeromq等项目的"Dependencies"
  的"Library..."加入"SBT: org.apache.spark:spark-core_2.10:1.0.0"

flume-sink代码生成
  到external/flume-sink执行以下代码
  mvn generate-sources

vm设置
  -Dspark.master=local[4]
  -Dspark.master=spark://inspiron1520:7077

环境变量
  MASTER=local[4]
  MASTER=spark://inspiron1520:7077

spark on local
====================
```bash
run-example SparkPi 10
```

spark on yarn
=======================
```bash
./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode cluster --num-executors 5 --driver-memory 2g --executor-memory 1g --executor-cores 3 --queue thequeue lib/spark-examples*.jar 20

./bin/spark-shell --master yarn-client
```

spark-env.sh设置
===================================
```
export JAVA_HOME=/usr/local/devtools/jdk
export SCALA_HOME=/usr/share/scala
export SPARK_HOME=/data/apache/spark
export SPARK_PID_DIR=/data/hadoop/spark/pids

export SPARK_LOCAL_IP=192.168.11.82
export SPARK_PUBLIC_DNS=test-master
export SPARK_CLASSPATH=$SPARK_CLASSPATH:$SPARK_HOME/lib/mysql-connector-java-5.1.35.jar
export SPARK_LOCAL_DIRS=/data/hadoop/spark/data

export HADOOP_CONF_DIR=/data/apache/hadoop/etc/hadoop

export SPARK_MASTER_IP=test-master
export SPARK_WORKER_CORES=2
export SPARK_WORKER_INSTANCES=6
export SPARK_WORKER_DIR=/data/hadoop/spark/worker
```

问题一览
===================
单机运行spark-shell出现ERROR Remoting: Remoting error:，在"conf/spark-env.sh"增加以下环境变量
```bash
export SPARK_MASTER_IP=localhost
export SPARK_LOCAL_IP=localhost
```
add jars in conf/spark-defaults.conf
```
spark.driver.extraClassPath /fullpath/firs.jar:/fullpath/second.jar
spark.executor.extraClassPath /fullpath/firs.jar:/fullpath/second.jar
```
add jars with spark-submit
```
./bin/spark-submit --class "SparkTest" --master local[*] --jars /fullpath/first.jar,/fullpath/second.jar /fullpath/your-program.jar
```

参考
===============
* <http://mmicky.blog.163.com/blog/static/1502901542014312101657612/>
* <http://www.cnblogs.com/hseagle/p/3732492.html>
* <http://parambirs.wordpress.com/2014/05/20/running-spark-1-0-0-snapshot-on-hadoopyarn-2-4-0/>
