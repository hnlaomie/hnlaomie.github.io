Title: docker introduction
Date: 2014-11-21 14:30
Category: tools 
Tags: 201411, docker
Author: laomie
Summary: docker的使用

image
====================
镜像（image）类似虚拟机镜像，是面向Docker引擎的只读模板，包含文件系统．运行容器前需本地存在对应的镜像，
没有则从仓库下载．

```bash
# 列出所有image
$ docker images

# 库中查找相关image
$ docker search ubuntu:latest

# 从库中下载image
$ docker pull ubuntu:latest

# 上传image到库
$ docker push ubuntu:latest

# 删除image（注：需要先删除相关的container）
$ docker rmi ubuntu:latest
$ docker rmi [image id]

# 根据path下的Dockerfile创建image
$ docker build -t ubuntu-java:latest /path

# 基于已有容器创建
$ docker commit -m "Added a new file" -a "laomie" CONTAINER_ID test:latest

# 保存，载入镜像
$ docker save -o ubuntu_latest.tar ubuntu:latest
$ docker load < ubuntu_latest.tar

# 更改某个image的tag
$ docker tag ubuntu-java:latest

# 上传镜像
$ docker tag test:latest hnlaomie/test:latest
$ docker push hnlaomie/test:latest
```

container
================
Container（容器）类似一个轻量级沙箱，Docker利用容器来运行和隔离应用．
容器是从镜像创建的应用运行实例，可以将其启动，开始，停止，删除，而这些容器都相互隔离，互不可见．
镜像自身是只读的，容器从镜像启动的时候，Docker会在镜像的最上层创建一个可写层，镜像本身将保持不变．
```bash
# 创建容器（创建后为停止状态）
docker create -it ubuntu:latest
# 启动容器
docker start -ai CONTAINER_ID
# 创建并启动容器
docker run -it ubuntu:latest /bin/bash
# 停止容器
docker stop CONTAINER_ID

# 查看容器
docker ps -a

# 进入运行的容器
docker exec -ti CONTAINER_ID /bin/bash

# 删除容器
docker rm CONTAINER_ID

# 导入导出容器
docker export CONTAINER_ID > test.tar
cat test.tar | docker import - hnlaomie/ubuntu:test
```

repository
=======================
Repository（仓库）是Docker集中存放镜像文件的场所，官方公共仓库为https://hub.docker.com
```bash
# 镜像查询
docker search
# 镜像下载
docker pull ubuntu:latest
# 创建私有仓库
docker run -d -p 5000:5000 -v /data/docker/registry:/tmp/registry registry
# 上传镜像
docker tag ubuntu:latest 192.168.1.20:5000/test
docker push 192.168.1.20:5000/test
```

volume
===================
Data Volumes（数据卷）是一个可供容器使用的特殊目录，可在容器间共享和重用，对它的修改会立马生效，
对它的更新不会影响镜像，它会一直存在，直到没有容器使用，类似mount文件．
```bash
# 创建volume，默认放在主机的"docker目录/volumes"下
$ docker volume create --name vol_simple
# 查看，明细，删除分别用以下命令
$ docker volume [ls | inspect | rm]
# 容器挂载数据卷到"/data"目录
$ docker run -d -v vol_simple:/data ubuntu:latest /bin/bash
# 共享volume
$ docker run -rm -it --name vol_use --volumes-from vol_simple ubuntu:latest /bin/bash
# 容器挂载主机目录
$ docker run -v /host/dir:/container/dir:rw ubuntu:latest /bin/bash

# 备份和恢复
docker run --rm --volumes-from vol_simple -v $(pwd):/backup ubuntu:latest tar cvf /backup/data.tar /data
docker run -it --name vol_bak -v /data ubuntu:latest /bin/bash
docker run --rm --volumes-from vol_bak -v $(pwd):/backup ubuntu:latest tar xvf /backup/data.tar -C /
```

network
====================
安装工具
```
apt install net-tools
```
