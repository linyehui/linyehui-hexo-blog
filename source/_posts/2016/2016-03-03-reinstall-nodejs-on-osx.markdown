---
title: 'Mac OSX下重装node.js'
layout: post
tags:
    - mac
    - nvm
    - nodejs
---

## 需求
之前没太留意node的安装版本，各种手动安装，今天突然发现node怎么又变成了0.10的版本，nvm，npm，node版本都怪怪的。

在代码洁癖的驱动下，准备清理这个恶劣的开发环境，清理后的目标是:

* 用brew来安装nvm，并管理nvm的升级
* 用nvm来安装node.js，并管理node.js的升级

## 卸载老版本的node和nvm

### 卸载node

* 如果是从brew安装的, 运行brew uninstall node
* 删除~/目录下所有node和node_modules
* 删除/usr/local/lib中的所有node和node_modules
* 删除/usr/local/lib中的所有node和node_modules的文件夹
* 在/usr/local/bin中, 删除所有node的可执行文件(node和npm)

#### 手动删除文件，整理成脚本是这个样子:
```
sudo rm -rf ~/.npm
sudo rm -rf ~/node_modules
sudo rm -rf ~/.node-gyp
sudo rm /usr/local/bin/node
sudo rm /usr/local/bin/npm
sudo rm /usr/local/lib/dtrace/node.d
```

参考文章: [如何删除node.js？](https://www.zhihu.com/question/27389115/answer/36434788)


### 卸载.nvm
我之前是手动安装的nvm，nvm的目录结构比较简单，删除这三个就可以了

```
rm -rf ~/.nvm
rm -rf ~/.npm
rm -rf ~/.bower
```

还需要删除下.bash_profile文件中的配置(用brew安装后还需要重新加上，但不太一样)

```
# vim .bash_profile
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```

参考文章: [How to uninstall nvm? #298](https://github.com/creationix/nvm/issues/298)

### 清理干净后的确认
重启终端后，挨个测试几个命令应该都是找不到，才算是正确的:


```
nvm
node
npm
```

## 重新安装
```
# 使用brew安装nvm
brew install nvm

# vim .bash_profile后增加下面这两行
export NVM_DIR="$HOME/.nvm"
source $(brew --prefix nvm)/nvm.sh

# 使用nvm安装node.js
nvm install node

# 校验
$ nvm --version
0.31.0
$ node -v
v5.7.1
$ npm -v
3.6.0
$ nvm list
->       v5.7.1
default -> node (-> v5.7.1)
node -> stable (-> v5.7.1) (default)
stable -> 5.7 (-> v5.7.1) (default)
iojs -> N/A (default)

```


好了，世界清净了 :)

## 参考文章: 
[Node.js 安裝與版本切換教學 (for MAC)](http://icarus4.logdown.com/posts/175092-nodejs-installation-guide)

[Mac上使用brew安装nvm来支持多版本的Nodejs](http://www.cnblogs.com/Don/p/4672287.html)
