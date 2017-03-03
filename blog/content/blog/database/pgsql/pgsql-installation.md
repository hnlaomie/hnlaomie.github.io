Title: postgresql installation
Date: 2016-11-24 10:10
Category: database
Tags: 201611, pgsql 
Author: laomie
Summary: postgresql installation

centos 6.5上安装postgresql 9.5
====================================
/etc/yum.repos.d/CentOS-Base.repo的[base] and [updates] 部分加入以下内容
```
exclude=postgresql*
```

安装postgresql
```bash
# 下载并安装
wget https://download.postgresql.org/pub/repos/yum/9.5/redhat/rhel-6-x86_64/pgdg-centos95-9.5-3.noarch.rpm
yum install pgdg-centos95-9.5-3.noarch.rpm
yum install postgresql95-server
# yum install postgresql95-client postgresql95-devel
# 修改默认路径
mkdir -p /data/pgsql/data
chown -R postgres.postgres /data/pgsql
vim /etc/sysconfig/pgsql/postgresql-9.5
###############
PGDATA=/data/pgsql/data
PGLOG=/data/pgsql/pgstartup.log
###############
# 初始化数据库
service postgresql-9.5 initdb
# 启动
chkconfig postgresql-9.5 on
service postgresql-9.5 start
# 验证默认路径
sudo -u postgres psql
SHOW data_directory;
```

卸载老版postgresql
```
yum erase postgresql-libs*
vim /etc/profile.d/pgsql.sh
#############
export PGSQL_HOME=/usr/pgsql-9.5
export PATH=$PGSQL_HOME/bin:$PATH
#############
```

安装后切换到postgres用户就能直接使用psql，因为pg_hba.conf有以下默认配置
```
local   all              all                                     peer
```
可用以下命令设置密码
```bash
# 设置postgres的密码
sudo su - postgres
psql
\password postgres
\q
```

设置远程登录
```
vim /data/pgsql/data/postgresql.conf
#########
listen_addresses = '*'
#########
vim /data/pgsql/data/pg_hba.conf
#########
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
host    all             all             192.168.11.1/24         md5 
#########
```
参考文档：

<https://wiki.postgresql.org/wiki/YUM_Installation#Install_PostgreSQL>
<https://www.digitalocean.com/community/tutorials/how-to-move-a-postgresql-data-directory-to-a-new-location-on-ubuntu-16-04>

ubuntu 16.04 上安装postgresql 9.6
====================================
vim /etc/apt/sources.list.d/pgdg.list
```
deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main
```
安装pgsql，配置文件默认路径为"/etc/postgresql/9.6/main"
```
sudo apt install wget ca-certificates
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt update
sudo apt install postgresql-9.6
sudo service postgresql start
```

postgresql建用户建库
====================
用以下命令建库和建用户，并把库权限给用户
```
create database adwo;
\c adwo;
create role dw_yingguang login password 'bidev';
create schema dw_yingguang authorization dw_yingguang;

create schema dw_dsp;
create role dw_dsp login password 'bidev';
grant all on schema dw_dsp to dw_dsp;

-- 设置schema查询
set search_path to dw_yingguang;

-- psql登录
PGPASSWORD=bidev psql -h 192.168.11.83 -U dw_yingguang adwo
```
