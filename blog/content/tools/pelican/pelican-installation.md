Title: pelican installation
Date: 2014-07-11 14:30
Category: tools
Tags: 201407, pelican, python, git 
Author: laomie
Summary: pelican的安装和github发布


pelican的安装
================

依赖包
---------

feedgenerator, jinja2, pygments, docutils, pytz, blinker, unidecode, six,
python-dateutil, markupsafe, markdown

安装
----------
```bash
$ pip install -e git+https://github.com/getpelican/pelican.git#egg=pelican
```

使用
----------
```bash
$ pelican-quickstart

$ fab build
$ fab serve

$ pelican content
$ cd output
$ sudo python -m http.server 80
```

配置
----------
  pelicanconf.py中设置"THEME"，值为pelican-bootstrap3的路径

在github建个人博客
-------------------------
  以"hnlaomie.github.io"建立repository，签出，进入并建立"blog"目录和如下设置".gitignore"
```bash
blog
blog/*
```

master提交新加的"blog"目录
```bash
$ git add .
$ git commit -m "init empty blog"
```

将已有的博客复制到"blog"目录，并建立代码分支
```bash
$ cd blog
$ git checkout -b source
$ git add .
$ git commit -m "add blog"
```

将博客发布到"master"
```bash
$ pelican content
$ ghp-import -b master output
$ git checkout master
$ git push --all
```

github使用
=========================

常用命令
------------
```bash
$ git clone https://github.com/hnlaomie/backup
$ cd backup
$ git add .
$ git commit -m 'first commit'
$ git push origin master
```
参考
==================
* <http://ntanjerome.org/blog/how-to-setup-github-user-page-with-pelican/>
* <http://blogs.skicelab.com/maurizio/unicode-common-pitfalls.html>
* <http://ntanjerome.org/blog/how-to-setup-github-user-page-with-pelican/>
* <http://moparx.com/2014/04/adding-search-capabilities-within-your-pelican-powered-site-using-tipue-search/>
