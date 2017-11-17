Title: linux tools
Date: 2014-07-30 22:10
Category: tools
Tags: 201407, linux
Author: laomie
Summary: linux工具


linux下音乐播放器比较
=========================
[Choosing a Linux Music Player](http://thesimplecomputer.info/choosing-a-linux-music-player)

pdf拆分合并
======================
```
# pdf拆分
pdftk ORIG_FILE.pdf cat 1 5 7 10-12 output NEW_FILE.pdf
# pdf合并
convert -density 300x300 -quality 100 mine1.pdf mine2.pdf merged.pdf
gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=merged.pdf mine1.pdf mine2.pdf
# low resolution
gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress -sOutputFile=merged.pdf mine1.pdf mine2.pdf
```
