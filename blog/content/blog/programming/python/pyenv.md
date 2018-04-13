Title: pyenv installation
Date: 2015-10-22 17:30
Category: programming
Tags: 201510, python 
Author: laomie
Summary: pyenv的安装


pyenv的安装
================

下载pyenv
```bash
git clone https://github.com/yyuu/pyenv.git ~/tools/pyenv
```

在".bashrc"或".bash_profile"设置环境变量
```bash
apt-get install build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev
export PYENV_ROOT="$HOME/tools/pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

重启shell,让环境变量生效
```bash
exec $SHELL
```

pyenv命令
```bash
# 安装python
export PYTHON_CONFIGURE_OPTS="--enable-shared"
pyenv install 3.5.3

# 卸载python
pyenv uninstall 3.5.3

# 重安装python
pyenv rebash

# python可用版本
pyenv versions

# python指定版本
pyenv local 3.5.3 system
```

在"~/tools/pyenv/version"设置python版本
```bash
3.5.3
system
2.7.12
```

安装常用包
```bash
# pelican
pip install pelican
pip install ghp-import
pip install Markdown

# docker
pip install docker-compose

# neovim
pip install neovim
pip install jedi

# data science
pip install numpy
pip install scipy
pip install pandas
pip install matplotlib
pip install scikit-learn
pip install ipython
pip install jupyter

pip install beautifulsoup4

```

使用国内mirrors
=====================
在"~/.pip/pip.conf"增加mirror
```
[global]
index-url = https://pypi.doubanio.com/simple
```
常见国内镜像源 
* <http://pypi.douban.com/simple/> 豆瓣 
* <http://mirrors.aliyun.com/pypi/simple/> 阿里 
* <http://pypi.hustunique.com/simple/> 华中理工大学 
* <http://pypi.sdutlinux.org/simple/> 山东理工大学 
* <http://pypi.mirrors.ustc.edu.cn/simple/> 中国科学技术大学 

python本地镜像安装
==========================
下载"Python-3.6.5.tgz"到本地目录，到目录下如下操作
<code>
# 启动http服务
python3 -m http.server
# 设置本地镜像
export PYTHON_BUILD_MIRROR_URL="http://127.0.0.1:8000/"
# pyenv安装python时找不到本地文件，需要将"Python-3.6.5.tgz"重命名为"http.server"输出的请求64位字符
pyenv install 3.6.5
</code>

参考
==================
* <https://github.com/yyuu/pyenv>
* <https://github.com/yyuu/pyenv/blob/master/COMMANDS.md>
