Title: wordpress tips
Date: 2017-07-12 14:30
Category: tools
Tags: 201707,wordpress 
Author: laomie
Summary: wordpress技巧

google字体加载慢
===============
查找出google字体加载代码，修改加载链接
```
grep -rnw 'wp_html' -e 'googleapis'

# 替换找到的链接
https://fonts.googleapis.com -> https://fonts.proxy.ustclug.org
```
