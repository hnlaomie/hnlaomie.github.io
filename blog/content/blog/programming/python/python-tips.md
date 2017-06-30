Title: python tips 
Date: 2016-11-09 10:30
Category: programming
Tags: 201611, python 
Author: laomie
Summary: python tips 

pyspider的安装
================
```bash
sudo apt install libcurl4-gnutls-dev libghc-gnutls-dev
pip install pyspider
```

jupyter启动
=================
```
# 生成配置文件
jupyter notebook --generate-config

# 在~/.jupyter/jupyter_notebook_config.py追加
c.NotebookApp.ip = '192.168.1.20'

# 启动
jupyter notebook --no-browser
```
