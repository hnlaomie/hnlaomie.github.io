Title: log4j tips
Date: 2015-05-12 19:30
Category: tools
Tags: 201505, java, log4j 
Author: laomie
Summary: log4j2使用说明

依赖包
=================
```
log4j-core
log4j-api
log4j-slf4j-imp
slf4j-api
slf4j-ext
```

log4j2.xml用例１
=====================
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration status="OFF">
    <appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </appenders>
    <loggers>
        <!--我们只让这个logger输出trace信息，其他的都是error级别-->
        <!--
        additivity开启的话，由于这个logger也是满足root的，所以会被打印两遍。
        不过root logger 的level是error，为什么Bar 里面的trace信息也被打印两遍呢
        -->
        <logger name="cn.lsw.base.log4j2.Hello" level="trace" additivity="false">
            <appender-ref ref="Console"/>
        </logger>
        <root level="error">
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```

log4j2.xml用例２
===================
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration status="error">
    <!--先定义所有的appender-->
    <appenders>
        <!--这个输出控制台的配置-->
        <Console name="Console" target="SYSTEM_OUT">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="trace" onMatch="ACCEPT" onMismatch="DENY"/>
            <!--这个都知道是输出日志的格式-->
            <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
        </Console>
        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，这个也挺有用的，适合临时测试用-->
        <File name="log" fileName="log/test.log" append="false">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
        </File>
        <!--这个会打印出所有的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFile" fileName="logs/app.log"
                     filePattern="log/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout pattern="%d{yyyy-MM-dd 'at' HH:mm:ss z} %-5level %class{36} %L %M - %msg%xEx%n"/>
            <SizeBasedTriggeringPolicy size="50MB"/>
        </RollingFile>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <loggers>
        <!--建立一个默认的root的logger-->
        <root level="trace">
            <appender-ref ref="RollingFile"/>
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```
