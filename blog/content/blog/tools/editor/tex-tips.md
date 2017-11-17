Title: tex tips
Date: 2017-09-21 22:30
Category: tools
Tags: 201709, tex
Author: laomie
Summary: tex技巧

texlive安装
===============
将texlive装到"/usr/local/data/tools"目录
```
# 下载安装
wget https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/texlive.iso 
sudo apt install libdigest-perl-md5-perl perl-tk
sudo mount -o loop texlive.iso /mnt
/mnt/install-tl --gui
sudo umount /mnt
 
# 设置环境变量
export TeXLiveMANPATH=/urs/local/data/tools/texlive/2017/texmf-dist/doc/man
export TeXLiveINFOPATH=/usr/local/data/tools/texlive/2017/texmf-dist/doc/info
export TeXLivePATH=/usr/local/data/tools/texlive/2017/bin/x86_64-linux
export PATH=$PATH:$TeXLivePATH

# 更新字体
sudo fc-cache -fsv
```

参考
==================
[Ubuntu 安装新版本TeXLive并更新](http://www.latexstudio.net/archives/9788)
[TeXシステムのインストール](http://user.ecc.u-tokyo.ac.jp/users/user-15826/wiki/?TeX/Install)
