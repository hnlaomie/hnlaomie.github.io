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
