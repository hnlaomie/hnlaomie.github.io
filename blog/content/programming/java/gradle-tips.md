Title: gradle tips
Date: 2014-12-18 15:10
Category: programming
Tags: 201412, gradle
Author: laomie
Summary: gradle tips

gradle对junit的支持
============================
在build.gradle增加以下内容
```bash
test {
    testLogging {
        events 'started', 'passed'
    }
}
```

运行单个测试
===========================
```bash
gradle -Dtest.single=SampleTest :project1:test
```

jvm options
===============================
gradle.properties内的org.gradle.jvmargs加入以下内容
```
-DconfigPath=/home/laomie/projects/working/biconfig
```
build.gradle内加入以下内容
```
test {
    systemProperties = System.properties
}
```
