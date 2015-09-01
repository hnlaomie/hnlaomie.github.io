Title: centos tips
Date: 2015-03-24 16:30
Category: tools
Tags: 201503, centos
Author: laomie
Summary: centos技巧

新增用户
===========================
```bash
groupadd dev
useradd -g dev laomie
passwd laomie
```

sudo权限设置，输入visudo，然后写入以下内容
  laomie ALL=(ALL)   ALL

安装python3
=======================
```bash
yum groupinstall "Development tools"
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
vim /etc/ld.so.conf  (add '/usr/local/lib')
./configure --prefix=/usr/local --enable-shared
make
make altinstall
ln -s /usr/local/bin/python3.4 /usr/bin/python3
vim /usr/bin/yum  (change to '#!/usr/bin/python2.6')
ldconfig

yum install lapack lapack-devel blas blas-devel
pip3.4 install numpy
pip3.4 install scipy
```

安装svn
=========================
编辑/etc/yum.repos.d/wandisco-svn.repo
```
[WandiscoSVN]
name=Wandisco SVN Repo
baseurl=http://opensource.wandisco.com/centos/6/svn-1.8/RPMS/$basearch/
enabled=1
gpgcheck=0
```
安装
```
# yum clean all
# yum install subversion
```
