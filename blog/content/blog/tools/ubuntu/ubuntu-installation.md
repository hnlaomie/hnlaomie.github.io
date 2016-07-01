Title: utuntu installation
Date: 2014-11-23 22:30
Category: tools 
Tags: 201411, ubuntu, java
Author: laomie
Summary: ubuntu安装


usb刻录
====================================
```bash
$ sudo dd bs=4M if=/home/laomie/ubuntu-14.04.1-desktop-amd64.iso of=/dev/sdc && sync
```

开发源和安装软件
==========================
```bash
$ sudo add-apt-repository ppa:git-core/ppa
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo add-apt-repository ppa:otto-kesselgulasch/gimp
$ sudo add-apt-repository ppa:nginx/stable
$ sudo add-apt-repository ppa:libreoffice/libreoffice-5-0
$ sudo apt-get --yes install subversion pkg-config libssl-dev git ant maven cmake build-essential zlib1g-dev lib32z1-dev libsnappy-dev oracle-java8-installer
$ sudo apt-get install ibus-anthy ibus-googlepinyin ibus-sunpinyin synaptic aptitude vim-gtk filezilla openssh-server mariadb-server
$ sudo apt-get install vlc browser-plugin-vlc gnome-mplayer bleachbit ubuntu-restricted-extras gnome-tweak-tool unity-tweak-tool gimp chromium-browser
```

grub启动
========================
```bash
$ sudo mv /etc/grub.d/30_os-prober /etc/grub.d/08_os-prober
$ sudo update-grub            （注：redhat用  grub2-mkconfig -o /boot/grub2/grub.cfg)
```

设置dns
==========================
安装dnsmasq
```bash
$ sudo apt-get install dnsmasq
```
修改"/etc/dnsmasq.conf"
```bash
listen-address=127.0.0.1
resolv-file=/etc/resolv.dnsmasq.conf
```
修改"/etc/NetworkManager/dnsmasq.d/cache"
```bash
cache-size=1000
```
新增"/etc/resolv.dnsmasq.conf"
```bash
# baidu dns
180.76.76.76
# OpenDNS
208.67.222.222
208.67.220.220
# 阿里DNS
223.5.5.5
223.6.6.6
# 114DNS
114.114.114.114
114.114.115.115
# OneDNS 北方
114.215.126.16
# V2EX DNS
199.91.73.222
178.79.131.110
# Google DNS
8.8.8.8
8.8.4.4
# OpenerDNS
42.120.21.30
# OneDNS 南方
112.124.47.27
```

安装docker
=============================
编辑"/etc/apt/sources.list.d/docker.list"，增加以下内容
```bash
deb https://apt.dockerproject.org/repo ubuntu-trusty main
```
导入key和安装
```bash
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
$ sudo apt-get install docker-engine
```
修改"/etc/default/ufw"
```bash
DEFAULT_FORWARD_POLICY="ACCEPT"
```
它机访问docker容器
```bash
$ sudo ufw allow 2375/tcp
```
修改"/etc/default/docker"，设置容器的dns
```bash
DOCKER_OPTS="--dns 208.67.222.222"
```
相关链接
<https://blog.docker.com/2015/07/new-apt-and-yum-repos/>
<http://get.docker.com/ubuntu>

安装qq
==========================
```bash
sudo apt-get install libgtk2.0-0:i386
sudo apt-get install lib32ncurses5
sudo apt-get -f install
dpkg -i *.deb
```
相关链接
<http://www.ubuntukylin.com/applications/showimg.php?lang=cn&id=23>

取消游客登录
======================
Create a directory ‘lightdm.conf.d’ under ‘/etc/loghtdm’
create a file ‘51-no-guest.conf’ under  the directory ‘/etc/lightdm/lightdm.conf.d’
```
[SeatDefaults]
allow-guest=false
```
```
sudo systemctl restart lightdm.service
```
