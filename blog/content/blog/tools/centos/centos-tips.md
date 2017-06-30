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

运行yum报"/usr/lib64/python2.6/site-packages/pycurl.so"错误
```
export LD_LIBRARY_PATH=/usr/lib64:$LD_LIBRARY_PATH
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

安装mysql5.7
=========================
```bash
wget http://dev.mysql.com/get/mysql57-community-release-el6-7.noarch.rpm
yum localinstall mysql57-community-release-el6-7.noarch.rpm
yum repolist enabled | grep "mysql.*-community.*"
yum install mysql-community-server
mkdir -p /data/mysql/data
mkdir -p /data/mysql/share
mysql_install_db --datadir=/data/mysql/data --lc-messages-dir=/data/mysql/share
```

/etc/my.cnf做如下修改
```
[mysqld]
#datadir=/var/lib/mysql
datadir=/data/mysql/data
```

设置密码
```
chown -R mysql.mysql /data/mysql
service mysqld start
cat /root/.mysql_secret
# 输入cat显示的密码
mysqladmin -u root -h localhost password '123456' -p
```

升级curl
================
vim /etc/yum.repos.d/city-fan.repo
```
[CityFan]
name=City Fan Repo
baseurl=http://www.city-fan.org/ftp/contrib/yum-repo/rhel$releasever/$basearch/
enabled=1
gpgcheck=0
```
执行以下命令升级curl
```
yum install epel-release -y
yum update curl.x86_64
```
