Title: hadoop installation
Date: 2014-07-30 22:30
Category: database
Tags: 201407, hadoop
Author: laomie
Summary: hadoop安装

安装必要的软件
-----------------
安装jdk和openssh并将hadoop解压到相关路径，此处为"/home/hduser/tools/hadoop"

设置用户和组
----------------------
增加"hadoop"组和"hduser“用户
```bash
sudo groupadd hadoop
sudo adduser --ingroup hadoop hduser
(archlinux: sudo seradd -m -g hadoop -s /bin/bash hduser)
sudo passwd hduser
```

ssh设置
----------------------
```bash
su - hduser
ssh-keygen -t rsa -P ""
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
```

禁用ipv6
----------------------
在"/etc/sysctl.conf"加入以下内容（注：archlinux为"/etc/sysctl.d/99-sysctl.conf"）
```
#disable ipv6
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```

环境变量设置
---------------------
在"hduser“用户路径下的".bashrc”里加入以下内容
```bash
# Set Hadoop-related environment variables
export HADOOP_HOME=/home/hduser/tools/hadoop
# Native Path
export HADOOP_COMMON_LIB_NATIVE_DIR=${HADOOP_HOME}/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
export JAVA_LIBRARY_PATH=${HADOOP_HOME}/lib/native 
#Java path
export JAVA_HOME='/home/hduser/tools/jdk7'
# Add Hadoop bin/ directory to PATH
export PATH=$PATH:$HADOOP_HOME/bin:$JAVA_PATH/bin:$HADOOP_HOME/sbin
```

建hadoop的数据目录和临时目录
-------------------
```bash
source ~/.bashrc
mkdir -p $HADOOP_HOME/yarn_data/hdfs/namenode
mkdir -p $HADOOP_HOME/yarn_data/hdfs/datanode
mkdir -p $HADOOP_HOME/tmp
```

hadoop配置文件设置
--------------------
1\. 为"libexec/hadoop-config.sh"设置"JAVA_HOME"
```bash
export JAVA_HOME='/home/hduser/tools/jdk8'
```

2\. 为"etc/hadoop/hadoop-env.sh"设置"JAVA_HOME"
```bash
export JAVA_HOME='/home/hduser/tools/jdk8'
```

3\. 设置"etc/hadoop/yarn-site.xml"
```xml
<configuration>
    <property>                                                                                                          
        <name>yarn.nodemanager.aux-services</name>                                                                      
        <value>mapreduce_shuffle</value>                                                                                
    </property>                                                                                                         
    <property>                                                                                                          
        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>                                              
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>                                                          
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.class</name>
        <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler</value>
    </property>

    <!-- performance setting -->
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>512</value>
        <description>Minimum limit of memory to allocate to each container request at the Resource Manager.</description>
    </property>
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>3072</value>
        <description>Maximum limit of memory to allocate to each container request at the Resource Manager.</description>
    </property>
    <property>
        <name>yarn.scheduler.minimum-allocation-vcores</name>
        <value>1</value>
        <description>The minimum allocation for every container request at the RM, in terms of virtual CPU cores. Requests lower than this won't take effect, and the specified value will get allocated the minimum.</description>
    </property>
    <property>
        <name>yarn.scheduler.maximum-allocation-vcores</name>
        <value>4</value>
        <description>The maximum allocation for every container request at the RM, in terms of virtual CPU cores. Requests higher than this won't take effect, and will get capped to this value.</description>
    </property>
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>3072</value>
        <description>Physical memory, in MB, to be made available to running containers</description>
    </property>
    <property>
        <name>yarn.nodemanager.resource.cpu-vcores</name>
        <value>4</value>
        <description>Number of CPU cores that can be allocated for containers.</description>
    </property>
    <!-- end of performance setting -->

    <property>
        <name>yarn.resourcemanager.address</name>
        <value>localhost:8032</value>
    </property>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>localhost:8031</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>localhost:8030</value>
    </property>
</configuration>
```

4\. 设置"etc/hadoop/core-site.xml"
```xml
<configuration>
    <property>                                                                                                          
        <name>fs.defaultFS</name>                                                                                    
        <value>hdfs://localhost:9000</value>                                                                            
    </property>                                                                                                         
    <property>                                                                                                          
        <name>hadoop.tmp.dir</name>                                                                                     
        <value>/home/laomie/tools/data/hdfs/tmp</value>                                                                    
    </property>                                                                                                         
    <property>
        <name>fs.trash.interval</name>
        <value>1440</value>
    </property>
    <property>
        <name>hadoop.native.lib</name>
        <value>true</value>
    </property>
    
    <property>
        <name>file.stream-buffer-size</name>
        <value>4096</value>
    </property>
    <property>
        <name>file.blocksize</name>
        <value>67108864</value>
    </property>
</configuration>
```

5\. 设置"etc/hadoop/mapred-site.xml" （注：mapred-site.xml.template生成mapred-site.xml）
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <description>Execution framework.</description>
    </property>
    <property>
        <name>mapreduce.jobtracker.address</name>
        <value>localhost:8021</value>
    </property>
    <property>
        <name>mapreduce.map.output.compress</name>
        <value>true</value>
    </property>

    <!-- performance setting -->
    <property>
        <name>mapreduce.reduce.shuffle.memory.limit.percent</name>
        <value>0.10</value>
    </property>
    <property>
        <name>yarn.app.mapreduce.am.resource.mb</name>
        <value>256</value>
    </property>
    <property>
        <name>yarn.app.mapreduce.am.command-opts</name>
        <value>-Xmx192m</value>
    </property>
    <property>
        <name>mapreduce.map.cpu.vcores</name>
        <value>1</value>
        <description>The number of virtual cores required for each map task.</description>
    </property>
    <property>
        <name>mapreduce.reduce.cpu.vcores</name>
        <value>1</value>
        <description>The number of virtual cores required for each map task.</description>
    </property>
    <property>
        <name>mapreduce.map.memory.mb</name>
        <value>256</value>
        <description>Larger resource limit for maps.</description>
    </property>
    <property>
        <name>mapreduce.map.java.opts</name>
        <value>-Xmx192m</value>
        <description>Heap-size for child jvms of maps.</description>
    </property>
    <property>
        <name>mapreduce.reduce.memory.mb</name>
        <value>512</value>
        <description>Larger resource limit for reduces.</description>
    </property>
    <property>
        <name>mapreduce.reduce.java.opts</name>
        <value>-Xmx384m</value>
        <description>Heap-size for child jvms of reduces.</description>
    </property>
    <!-- end of performance setting -->
</configuration>
```

6\. 设置"etc/hadoop/hdfs-site.xml"
```xml
<configuration>
    <property>                                                                                                              
        <name>dfs.replication</name>                                                                                        
        <value>1</value>                                                                                                    
    </property>                                                                                                             
    <property>                                                                                                              
        <name>dfs.namenode.name.dir</name>                                                                                  
        <value>file:/home/laomie/tools/data/hdfs/namenode</value>                                               
    </property>                                                                                                             
    <property>                                                                                                              
        <name>dfs.datanode.data.dir</name>                                                                                  
        <value>file:/home/laomie/tools/data/hdfs/datanode</value>                                               
    </property> 
    <property>                                                                                                              
        <name>dfs.permissions</name>                                                                                        
        <value>false</value>                                                                                                
    </property>
    <property>     
        <name>dfs.socket.timeout</name>    
        <value>180000</value>
    </property>
    <property>
        <name>dfs.namenode.handler.count</name>
        <value>10</value>
    </property>
    <property>
        <name>dfs.namenode.checkpoint.period</name>
        <value>7200</value>
    </property>
    <property> 
        <name>dfs.balance.bandwidthPerSec</name> 
        <value>20971520</value> 
    </property>
    <property>
        <name>dfs.blocksize</name>
        <value>33554432</value>
    </property>
    <property>
        <name>dfs.stream-buffer-size</name>
        <value>8192</value>
    </property>
    <!--
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
        <value>file:/usr/local/data/data/hdfs/namesecondary</value>	
    </property>
    -->
</configuration>
```

hadoop名称节点格式化
---------------------
```bash
hdfs namenode -format
```

hadoop的启动，停止
--------------------
```bash
# for master
start-all.sh
start-dfs.sh
start-yarn.sh
stop-all.sh
stop-yarn.sh
stop-dfs.sh

# for master and slaves
hadoop-daemon.sh start namenode|secondarynamenode|datanode
hadoop-daemon.sh stop namenode|secondarynamenode|datanode
```

hadoop服务
-------------------
* namenode (50070)

* cluster and applications  (8088)

* secondary namenode  (50090)

* datanode (50075)

多节点hadoop设置
----------------------
在"/etc/hosts"设置主从节点机
```
192.168.1.12  laomie-pc
192.168.1.11  slave01
```

主节点机"laomie-pc"ssh登录从节点机"slave01"的设置（注：所有从节点机都需设置）
```bash
ssh-copy-id -i /home/hduser/.ssh/id_rsa.pub hduser@slave01
```

在主节点机的hadoop配置"$HADOOP_CONF_DIR/slaves"中添加从节点机
```
slave01
```

hadoop的代码编译 (archlinux)
--------------------
安装下列依赖软件
```bash
sudo pacman -S cmake apache-ant base-devel protobuf maven
ln -s /etc/profile.d/jre.sh /etc/profile.d/jdk.sh
sudo yaourt -S findbugs
```

修改~/.bashrc
```bash
export FINDBUGS_HOME=/opt/findbugs
export PATH=$PATH:$FINDBUGS_HOME/bin
```

编译hadoop
```bash
mvn package -Pdist,native,docs,src -DskipTests -Dtar
```

hadoop的代码编译 (ubuntu)
--------------------
安装下列依赖软件
```bash
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get --yes install openjdk-7-jdk subversion pkg-config libssl-dev git ant maven cmake build-essential zlib1g-dev lib32z1-dev libsnappy-dev oracle-java8-installer
```

安装protobuf2.5.0
```bash
$ ./configure
$ make
$ make check
$ sudo make install
$ sudo ldconfig
```

安装findbugs3.0.0
* 解压并如上设置环境变量
 
jdk8编译hadoop （注：不能编译doc）
```bash
mvn package -Pdist,native -DskipTests -Dtar -Dmaven.javadoc.skip=true 
```

hadoop的升级
----------------------------
```bash
# 备份namenode信息，即etc/hadoop/hdfs-site.xml下的dfs.namenode.name.dir所指向的文件夹
# 其它备份, 检查
hadoop dfsadmin -safemode enter
hadoop fsck / -files -blocks -locations | grep -v -E '^.' > /data/backup/hadoop.bak
hadoop dfsadmin -safemode leave
mr-jobhistory-daemon.sh stop historyserver
cp -r /data/hadoop/hdfs/namenode/ /data/backup

# 停机并复制配置文件到新版hadoop
stop-all.sh
cd $HADOOP_HOME/etc/hadoop
cp hdfs-site.xml mapred-site.xml yarn-site.xml core-site.xml hadoop-env.sh slaves $NEW_HADOOP_HOME/etc/hadoop/
cd ~
mv $HADOOP_HOME $OLD_HADOOP_HOME
mv $NEW_HADOOP_HOME $HADOOP_HOME
scp -r $HADOOP_HOME hadoop@node1:/data/apache/

# 升级
start-dfs.sh -upgrade
start-yarn.sh
mr-jobhistory-daemon.sh start historyserver

# 验证hadoop
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.3.jar pi 4 100

# 确认没问题后，最终完成升级
hdfs dfsadmin -finalizeUpgrade
```

references
-----------------------------------------------
* <http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html>
* <http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html>
* <http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsRollingUpgrade.html>
* <http://www.cnblogs.com/lanxuezaipiao/p/3525554.html>
* <http://solaimurugan.blogspot.com/2013/11/setup-multi-node-hadoop-20-cluster.html>
* <https://www.zybuluo.com/layor/note/161370>
* <https://www.zybuluo.com/layor/note/162019>
* <http://www.cnblogs.com/JavaSmart/p/4567173.html>



