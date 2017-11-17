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

jupyter安装
=================
安装python时，需要先安装sqlite相关包
```
apt install libsqlite3-dev
```

jupyter相关配置
```
# 生成配置文件
jupyter notebook --generate-config

# 在~/.jupyter/jupyter_notebook_config.py追加
c.NotebookApp.ip = '*'
c.NotebookApp.notebook_dir = '/data/jupyter/notebook'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 10002

# 启动
jupyter notebook
```

python调用父目录模块
========================
```
import sys
from os.path import dirname, abspath 
sys.path.append(dirname(dirname(abspath(__file__))))
```
或者将项目路径加到"$PYTHONPATH"
