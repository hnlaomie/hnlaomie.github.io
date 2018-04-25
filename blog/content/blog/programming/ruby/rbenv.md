Title: rbenv installation
Date: 2018-04-12 21:30
Category: programming
Tags: 201804, ruby 
Author: laomie
Summary: rbenv的安装

rbenv的安装
================

下载rbenv
```bash
git clone https://github.com/rbenv/rbenv.git ~/tools/rbenv
git clone https://github.com/rbenv/ruby-build.git ~/tools/rbenv/plugins/ruby-build
```

在".bashrc"或".bash_profile"设置环境变量
```bash
apt-get install build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev
export RBENV_ROOT="$HOME/tools/rbenv"
export PATH="$RBENV_ROOT/bin:$RBENV_ROOT/plugins/ruby-build/bin:$PATH"
eval "$(rbenv init -)"
```

重启shell,让环境变量生效
```bash
exec $SHELL
```

rbenv命令
```bash
# 查看可安装版本
rbenv install -l
# 安装ruby
rbenv install 2.5.1
# 卸载ruby
rbenv uninstall 2.5.1

# ruby可用版本
rbenv versions

# ruby指定版本
rbenv local 2.5.1 system

# 安装ruby gems
gem install bundler
```

在"~/tools/rbenv/version"设置ruby版本
```bash
2.5.1
system
```

安装常用包
```bash
# redis
gem install redis
```

ruby本地镜像安装
==========================
下载"ruby-2.5.1.tar.bz2"到本地目录，到目录下如下操作
```
# 启动http服务
python3 -m http.server
# 设置本地镜像
export RUBY_BUILD_MIRROR_URL="http://127.0.0.1:8000/"
# rbenv安装python时找不到本地文件，需要将"ruby-2.5.1.tar.bz2"重命名为"http.server"输出的请求64位字符
rbenv install 2.5.1
```

参考
==================
