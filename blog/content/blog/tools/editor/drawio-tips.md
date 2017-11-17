Title: draw.io tips
Date: 2017-07-26 15:30
Category: tools
Tags: 201707,draw.io 
Author: laomie
Summary: draw.io技巧

下载相关代码
===============
从"https://github.com/fjudith/docker-draw.io"下载"Dockerfile"，并生成以下"docker-compose.yml"文件
```yml
version: '3'

services:

  host:
    image: hnlaomie/draw.io
    hostname: draw_host
    networks:
      dev-net:
        ipv4_address: 172.19.0.10
    ports:
      - 8002:8080

networks:
  dev-net:
    external: true
```

安装，启动
===================
```
docker build -t hnlaomie/draw.io:latest ./
docker-compose up
```
通过链接"http://localhost:8002/draw/?offline=1&https=0"打开"draw.io"
