---
title: 'Windows下使用nvm管理node版本'
layout: post
tags:
    - nvm
    - windows
    - node
    - npm
---

## 需求
在Windows下像Mac和Linux那样使用NVM来管理node版本，使用npm

* 原来的解决方案用的挺好的：
https://github.com/coreybutler/nvm-windows

* 今天更新node版本遇到了问题：
之前安装的8.x的版本能正常使用，nvm install 10.10.0后发现node有但是npm文件没有下载下来

nvm root看了下安装路径是在AppData下，虚拟链接是在%Program Files%目录下，nvm use的时候会弹出UAC提示，照理应该也不会出错，不过nvm的对应版本目录下并没有下载到npm文件。

## 解决方案
github上看了下，最近（2018-08-02）更新了一个版本1.1.7：
https://github.com/coreybutler/nvm-windows/releases

我把老的版本卸载干净，然后重新安装了下新版本，然后重新安装，重新安装的时候我把安装目录和软连接目录都放到的我的非系统盘D盘的根目录下：
```
D:\nodejs\
D:\nvm\
```
重新使用nvm安装下最新版本的node：
```
nvm list available
nvm install 10.10.0
nvm use 10.10.0

node --version
npm --version
```
验证了下版本，没问题：
```
D:\>npm --version
6.4.1

D:\>node --version
v10.10.0

```