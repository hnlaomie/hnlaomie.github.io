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
pyenv install 3.5.0

# 卸载python
pyenv uninstall 3.5.0

# 重安装python
pyenv rebash

# python可用版本
pyenv versions

# python指定版本
pyenv local 3.5.0 system
```

在"~/tools/pyenv/version"设置python版本
```bash
3.5.0
system
2.7.10
```

参考
==================
* <https://github.com/yyuu/pyenv>
* <https://github.com/yyuu/pyenv/blob/master/COMMANDS.md>
