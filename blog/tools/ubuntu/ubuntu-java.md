Title: ubuntu java
Date: 2014-11-18 21:30
Category: programming
Tags: 201411, java, maven, gradle, ubuntu
Author: laomie
Summary: ubuntu下java环境


解压安装jdk8，maven3，gradle
---------------------------------
jdk-8u25-linux-x64.tar.gz
apache-maven-3.2.3-bin.tar.gz
gradle-2.2-all.zip

设置环境变量
------------------------------------
修改"~/.bashrc"
```bash
export JAVA_HOME=/usr/local/tools/jdk8
export CLASSPATH=/usr/local/tools/jdk8/lib
export MAVEN_HOME=/usr/local/tools/maven3
export GRADLE_HOME=/usr/local/tools/gradle
export PATH=$PATH:$JAVA_HOME/bin:$GRADLE_HOME/bin:$MAVEN_HOME/bin
```

系统设置jdk
--------------------------------------------
```bash
$ sudo update-alternatives --install /usr/bin/java java /usr/local/tools/jdk8/bin/java 300
$ sudo update-alternatives --install /usr/bin/javac javac /usr/local/tools/jdk8/bin/javac 300
$ sudo update-alternatives --config java
```



