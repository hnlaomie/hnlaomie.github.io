Title: r installation
Date: 2016-07-14 7:30
Category: programming
Tags: 201607, r 
Author: laomie
Summary: r的安装

r的安装
================
```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9

# 在/etc/apt/sources.list.d/cran.list文件增加以下内容
deb https://mirrors.tuna.tsinghua.edu.cn/CRAN/bin/linux/ubuntu xenial/

sudo apt update 
sudo apt install r-base r-base-dev
```

参考
==================
* <https://cran.r-project.org/bin/linux/ubuntu/>
* <https://cran.r-project.org/mirrors.html>
* <http://sites.psu.edu/theubunturblog/installing-r-in-ubuntu/>
