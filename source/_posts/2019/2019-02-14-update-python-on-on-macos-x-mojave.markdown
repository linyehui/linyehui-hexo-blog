---
title: 'Mac OS X Mojave (10.14) 下更新python开发环境'
layout: post
tags:
    - python
    - mac
    - pyenv
---

## 需求
系统自带的python版本是2.7，之前用homebrew直接安装了一个python 3.6.5，不方便切换版本，想要清理下环境，使用pyenv来统一管理系统内的python版本

## 清理老版本
### 清理老的pip安装
之前用sudu pip安装了不少package，太挫了，先把这部分清理一下

```bash
# 确认下当前使用的文件
which pip
which python

# 删除pip
sudu pip uninstall pip

# 卸载之前brew安装的python
brew uninstall python –ignore-dependencies

brew uninstall pyenv
```

### 检查是否清理干净

下面这几个命令应该都找不到了
```
pyenv
python
pip
```

## 安装新版本
### 最先需要安装的是pyenv
```
brew install pyenv

# 打开.bash_profile
vim ~/.bash_profile

# 添加下面三行

# pyenv
export PYENV_ROOT=/usr/local/var/pyenv
eval "$(pyenv init -)"

# 重新加载下配置，查看当前的python版本，应该只有一个system版本
source ~/.bash_profile
pyenv versions
```

### 安装指定的python版本
本来应该是一条命令搞定的事情，比如:

```bash
pyenv install 3.6.8
```

结果遇到了问题：[Install fails, "zlib not available" #530](https://github.com/pyenv/pyenv/issues/530)

简单来说，我确认了以下几个操作后最终可以了，我也不太确定是那个生效了：

```bash
# 检查xcode编译工具
xcode-select --install

# 检查几个安装，其实我之前都安装过了，提示是否需要reinstall，我没有重新安装
brew install readline

# 修正zlib的inclue路径
export CFLAGS="-I$(xcrun --show-sdk-path)/usr/include"
export CPPFLAGS="-I/usr/local/opt/zlib/include"
pyenv install 3.6.8
```

安装成功后，把默认版本修改下
```
pyenv versions
pyenv global 3.6.8
```

检查下版本是否正常，正常的话就算完成了
```
python --version
pip --version
```