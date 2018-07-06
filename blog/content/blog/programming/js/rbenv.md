Title: nodejs installation
Date: 2018-05-04 12:30
Category: programming
Tags: 201805, js 
Author: laomie
Summary: nodejs的安装

nodejs的安装
================

下载并安装nvm
```bash
export NVM_DIR="$HOME/tools/nvm"
git clone https://github.com/creationix/nvm.git "$NVM_DIR"
cd "$NVM_DIR"
git checkout `git describe --abbrev=0 --tags --match "v[0-9]*" $(git rev-list --tags --max-count=1)`
chmod 755 $NVM_DIR/nvm.sh
$NVM_DIR/nvm.sh
```

nvm的更新
```bash
cd "$NVM_DIR"
git fetch --tags origin
git checkout `git describe --abbrev=0 --tags --match "v[0-9]*" $(git rev-list --tags --max-count=1)`
$NVM_DIR/nvm.sh
```

在".bashrc"或".bash_profile"设置环境变量
```bash
apt-get install build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev
export NVM_DIR="$HOME/tools/nvm"                                                                                                    
# This loads nvm                                                                                                                    
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

重启shell,让环境变量生效
```bash
exec $SHELL
```

nvm命令
```bash
# 查看所有长期支持版
nvm ls-remote --lts
# 查看最新长期支持版
nvm version-remote --lts
# 安装最新版或指定版本（可设置代理）
export NVM_NODEJS_ORG_MIRROR=https://nodejs.org/dist
nvm install --lts
nvm install --lts=carbon
# 卸载最新版或指定版本
nvm uninstall --lts
nvm uninstall --lts=carbon
# 安装并迁移npm包
nvm install node --reinstall-packages-from=node
nvm install 6 --reinstall-packages-from=5
# 使用相关版本
nvm use --lts
nvm use system
# 查看当前使用版本
nvm ls
```

设置使用版本
```bash
echo "8.1" > .nvmrc
# to default to the latest LTS version
echo "lts/*" > .nvmrc
# to default to the latest version 
echo "node" > .nvmrc 
```

npm换源，编辑"~/.npmrc
```
registry =https://registry.npm.taobao.org
```

参考
==================
