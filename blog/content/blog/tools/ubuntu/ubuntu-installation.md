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
$ sudo add-apt-repository ppa:kelleyk/emacs
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
deb https://apt.dockerproject.org/repo ubuntu-xenial main
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

修改"/etc/default/docker"，设置容器的dns，
```bash
DOCKER_OPTS="--dns 208.67.222.222"
```

设置镜像和容器的存放路径
* 修改启动的配置文件"/lib/systemd/system/docker.service"
```
ExecStart=/usr/bin/docker daemon -g /usr/local/data/tools/docker -H fd://
```
重启服务并验证"Docker Root Dir"
```bash
systemctl stop docker
ps aux | grep -i docker | grep -v grep
# docker停不了可能需要运行以下命令再停
systemctl daemon-reload
# 同步docker到新的目录
rsync -aqxP /var/lib/docker/ /usr/local/data/tools/docker
systemctl start docker
# 验证路径是否已是新路径
docker info
```

ubuntu image需要安装软件列表
```
apt install vim net-tools inetutils-ping
```

相关链接
<https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04>
<https://blog.docker.com/2015/07/new-apt-and-yum-repos/>
<https://docs.docker.com/engine/installation/linux/ubuntulinux/>
<https://linuxconfig.org/how-to-move-docker-s-default-var-lib-docker-to-another-directory-on-ubuntu-debian-linux>

安装R
======================
```bash
sudo echo "deb http://cran.rstudio.com/bin/linux/ubuntu xenial/" | sudo tee -a /etc/apt/sources.list
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9
gpg -a --export E084DAB9 | sudo apt-key add -
sudo apt update
sudo apt install r-base r-base-dev
sudo apt install gdebi-core
wget https://download1.rstudio.org/rstudio-0.99.903-amd64.deb
sudo gdebi -n rstudio-0.99.903-amd64.deb
```
相关链接
<https://www.datascienceriot.com/how-to-install-r-in-linux-ubuntu-16-04-xenial-xerus/kris/>

安装mariadb
========================
```bash
sudo apt-get install software-properties-common
sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8
sudo add-apt-repository 'deb [arch=amd64,i386,ppc64el] http://ftp.yz.yamagata-u.ac.jp/pub/dbms/mariadb/repo/10.1/ubuntu xenial main'
sudo apt update
sudo apt install mariadb-server
```

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

安装texlive
======================
```bash
sudo add-apt-repository ppa:jonathonf/texlive
sudo apt update && sudo apt install texlive-full
sudo apt update && sudo apt install texworks
sudo apt install ppa-purge && sudo ppa-purge ppa:jonathonf/texlive
```
