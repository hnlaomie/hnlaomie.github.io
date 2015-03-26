Title: spark installation
Date: 2014-08-27 17:00
Category: database
Tags: 201408, spark 
Author: laomie
Summary: spark安装

maven编译
-----------------------------
```bash
export MAVEN_OPTS="-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"
mvn -Pyarn --Dyarn.version=2.5.2 -Phadoop-2.4 -Dhadoop.version=2.5.2 -Phive -Phive-thriftserver -Dscala-2.11 -DskipTests clean package
```

sbt编译
----------------------------
```bash
SPARK_HADOOP_VERSION=2.5.2 SPARK_YARN=true SPARK_HIVE=true sbt/sbt clean assembly
```

生成spark部署包
```bash
export MAVEN_OPTS="-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"
./make-distribution.sh --tgz --with-tachyon -Pyarn -Dyarn.version=2.5.2 -Phadoop-2.4 -Dhadoop.version=2.5.2 -Phive -Phive-thriftserver -DskipTests
```

intellij调试spark代码
-------------------------------------
安装jdk8，scala2.10，sbt0.13并在"~/.bashrc"设置相关环境变量
```bash
export SCALA_HOME=/home/laomie/tools/scala-2.10
export SBT_HOME=/home/laomie/tools/sbt
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk
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
------------------------------------------
```bash
run-example SparkPi 10
```

spark on yarn
-----------------------------------------
```bash
SPARK_JAR=./assembly/target/scala-2.10/spark-assembly-1.2.0-SNAPSHOT-hadoop2.5.0.jar HADOOP_CONF_DIR=/home/hduser/tools/hadoop2.5-single ./bin/spark-submit --master yarn --deploy-mode cluster --class org.apache.spark.examples.SparkPi --num-executors 3 --driver-memory 2g --executor-memory 1g --executor-cores 1 examples/target/scala-2.10/spark-examples-1.2.0-SNAPSHOT-hadoop2.5.0.jar

SPARK_JAR=./assembly/target/scala-2.10/spark-assembly-1.2.0-SNAPSHOT-hadoop2.5.0.jar HADOOP_CONF_DIR=/home/hduser/tools/hadoop2.5-single/etc/hadoop MASTER=yarn-client ./bin/spark-shell
```

问题一览
---------------------------------
单机运行spark-shell出现ERROR Remoting: Remoting error:，在"conf/spark-env.sh"增加以下环境变量
```bash
export SPARK_MASTER_IP=localhost
export SPARK_LOCAL_IP=localhost
```

参考
------------------------------------------
* <http://mmicky.blog.163.com/blog/static/1502901542014312101657612/>
* <http://www.cnblogs.com/hseagle/p/3732492.html>
* <http://parambirs.wordpress.com/2014/05/20/running-spark-1-0-0-snapshot-on-hadoopyarn-2-4-0/>
