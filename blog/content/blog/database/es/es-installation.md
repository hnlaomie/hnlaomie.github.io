Title: ElasticSearch installation
Date: 2018-09-06 08:30
Category: database 
Tags: 201809, es
Author: laomie
Summary: ElasticSearch 集群安装

系统设置
==================
"max file descriptors"修改, 在"/etc/security/limits.conf"增加以下两行
```
*            hard   nofile          65536
*            soft   nofile          65536
```
debian系统还需要修改"/etc/systemd/system.conf"和"/etc/systemd/user.conf"里的一行
```
DefaultLimitNOFILE=65535
```

"max virtual memory areas"修改, 在"/etc/sysctl.conf"增加以下一行
```
vm.max_map_count=262144
```

单机安装ElasticSearch, Kibana
===================================
1. 下载并解压elasticsearch,然后修改"config/elasticsearch.yml"
```
path.data: /home/laomie/tools/data/es/data
path.logs: /home/laomie/tools/data/es/logs

#allow origin for elasticsearch head
http.cors.enabled: true                                                                                                 
http.cors.allow-origin: "*"
```

2. 下载并解压kibana,然后修改"config/kibana.yml"
```
kibana.index: "/home/laomie/tools/data/es/kibana"
```

3. 安装 elasticsearch head (需要先用nvm装node)
```
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
# open http://localhost:9100/
```

集群安装ElasticSearch, Kibana
===================================

参考
============
https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#limits.conf
https://unix.stackexchange.com/questions/385222/debian-9-1-system-is-ignoring-ulimit-for-maximum-open-files-for-rtorrent
