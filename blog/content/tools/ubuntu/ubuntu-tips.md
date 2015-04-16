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

删除旧内核
=================================
```bash
sudo aptitude purge ~ilinux-image-.*\(\!\(`uname -r`\|generic-.*\)\)
```

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
