Title: ubuntu tips
Date: 2015-02-02 10:30
Category: tools
Tags: 201502, ubuntu
Author: laomie
Summary: ubuntu技巧

禁用笔记本触摸板
=================================
安装 gpointing-device-settings
system settings -> mouse & touchpad

启动菜单
=========================================
/usr/share/applications

取消ssh远程连接键盘失灵
=========================================
在"~/.ssh/config"中加入以下内容
```
Host *
ServerAliveInterval 180
```

远程桌面
===========================
安装teamviewer
```bash
sudo dpkg --add-architecture i386
sudo apt-get update
sudo dpkg -i teamviewer_10.0.41499_i386.deb
sudo apt-get install -f
```

teamviewer启动，停止
```bash
sudo service teamviewerd stop|start|restart
sudo teamviewer -info
sudo teamviewer --daemon stop|start|restart
sudo teamviewer --passwd password
```

ibus-googlepinyin托板单词数
===============================
修改 /usr/share/ibus-googlepinyin/engine.py
```
self.__lookup_table.set_page_size(9)
```

16.04 ssh 问题
=========================
sign_and_send_pubkey: signing failed: agent refused operation
```
sudo apt-get autoremove gnome-keyring
ssh-add -D
```

vcd dat to mpeg
=========================
```
sudo apt-get install handbrake
```

git ssl errors
=========================
```
export GIT_SSL_NO_VERIFY=1
```

thinkpad E431 安装双系统
=====================================
BIOS做以下设置
```
Security -> Secure Boot -> Disabled
Startup -> Boot -> Network Boot -> USB HDD
Startup -> Boot -> UEFI/Legacy Boot -> Both / Legacy First
```

chrome安装xx-net代理插件
============================================
1. 从＂工具->扩展程序＂打开扩展插件，将"XX-Net/SwitchyOmege/SwitchyOmega.crx"拖入

2. 点击＂添加扩展程序＂，＂跳过教程＂安装插件

3. 在插件界面的＂导入/导出 -> 从备份文件恢复＂导入"XX-Net/SwitchyOmege/OmegaOptions.bak"文件

4. 在插件界面的＂XX-Net自动切换＂点击＂立即更新情景模式＂

5. 菜单-设置-下翻点击“显示高级设置”-点击“管理证书”，＂授权中心->导入＂，选择＂XX-Net/data/gae_proxy/CA.crt＂

下载youtube
=========================
安装youtube-dl
```
pip install youtube-dl
```
下载
```
# 查看媒体输出格式
youtube-dl --proxy http://127.0.0.1:8087 --no-check-certificate -F https://www.youtube.com/watch?v=R92Gr-c0Vfo&index=2&list=PLuJmmKtsV1dNE5y1gu1xpbIl3M2b7AW4D
# 从url列表文件下载
youtube-dl -f 22 --proxy http://127.0.0.1:8087 --no-check-certificate --batch-file='pg2016.txt'
```

lubuntu的fcitx输入法黑屏
=========================
```
sudo apt install qtdeclarative5-qtquick2-plugin
```

视频转换
==================
```
HandBrakeCLI -Z "High Profile" -i a_movie.iso -o a_movie.mp4
```

查看，关闭端口
==========
```
netstat -tln | grep 7077
lsof -i :7077
kill -9 进程id
kill -9 $(netstat -tlnp|grep 7077|awk '{print $7}'|awk -F '/' '{print $1}')
```

编译emacs
=============
```
sudo apt install build-essential checkinstall
sudo apt-get install texinfo libx11-dev libxpm-dev libjpeg-dev libpng-dev libgif-dev libtiff-dev libgtk2.0-dev libncurses-dev libxpm-dev automake autoconf -y
sudo apt-get build-dep emacs24
./configure
make
sudo checkinstall
sudo dpkg -r emacs-25
```

查看磁盘uuid
===================
```
sudo blkid
# ls -al /dev/disk/by-uuid
```

增加sudo权限用户
=====================
```
adduser laomie
usermod -aG sudo laomie
# su - root
```

使用ufw
===============
```
ufw default deny 
ufw allow 22
ufw enable
```

修改locale
=========================
```
# vim /etc/default/locale
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh:en_US:en"

# vim /var/lib/locales/supported.d/zh-hans
zh_CN.UTF-8 UTF-8
zh_SG.UTF-8 UTF-8

# run command
sudo locale-gen
```
