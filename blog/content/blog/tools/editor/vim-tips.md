Title: vim tips
Date: 2015-02-02 10:30
Category: tools
Tags: 201502, vim
Author: laomie
Summary: vim技巧

复制粘贴
===============
```
"ay          复制到a寄存器
"ap          是从a寄存器粘贴
"+p          从粘贴板粘贴
"+y          复制到粘贴板
```

vim和终端间切换
======================
```
ctrl + z     切换到终端
fg           从终端返回vim
```

nerdtree使用
====================
```
,nn          打开nerdtree
shift + i    显示隐藏目录
ctrl + ww    nerdtree和vim间切换
```

格式化代码
=====================
```
gg=G
```

vim替换
=====================
```
# 替换行尾的^M
%s/\r/
# 替换制表符为","
%s/\t/,/g
```

参考
==================
[vim quick reference card]({filename}/docs/tools/editor/vimqrc.pdf)
