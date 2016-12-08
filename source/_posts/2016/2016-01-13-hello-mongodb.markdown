---
title: 'Hello MongoDB'
layout: post
tags:
    - mongodb
    - python
    - mac
---

## 需求
最近在看Tornado的[教程](http://demo.pythoner.com/itt2zh/ch4.html)，其中使用了MongoDB，之前没用过，所以需要配置下相关的环境，并在Python中使用

## 安装MongoDB

### 完整的命令行

```bash
# 安装
brew update
brew install mongodb

# 为数据库创建默认的文件目录，并设置权限
sudo mkdir -p /data/db
sudo chown -R linyehui /data

# 启动服务器
mongod

# 另外启动一个终端，连接服务器
mongo

```

### 过程分解
我的系统是Mac OS X 10.11.2，根据[MongoDB官网的指引](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/)，我直接用HomeBrew安装

```bash
brew update
brew install mongodb
```

安装成功后，尝试启动数据库服务：

```bash
mongod

# 出现错误，提示
2016-01-13T15:19:24.484+0800 I STORAGE  [initandlisten] exception in initAndListen: 29 Data directory /data/db not found., terminating
```

原来是默认的数据库目录不存在，但是于是创建下:

```bash
sudo mkdir -p /data/db

# 记得要设置下目录权限，把owner设置成当前用户
sudo chown -R linyehui /data
```

如果目前权限没有设置正常，会出现权限问题：

```bash
2016-01-13T15:22:47.055+0800 I STORAGE  [initandlisten] exception in initAndListen: 98 Unable to create/open lock file: /data/db/mongod.lock errno:13 Permission denied Is a mongod instance already running?, terminating
```

好了，可以起启动了

```bash
mongod

# 终端显示
2016-01-13T19:58:30.287+0800 I CONTROL  [initandlisten] MongoDB starting : pid=16845 port=27017 dbpath=/data/db 64-bit host=linyehui-mbp.local
2016-01-13T19:58:30.288+0800 I CONTROL  [initandlisten] db version v3.2.0
...
```

服务器启动好了，就可以新建一个终端，来连接数据库测试了

```bash
mongo

# 终端显示
MongoDB shell version: 3.2.0
connecting to: test
```

## Python中使用MongoDB
我们这里使用的Python库是PyMongo，我直接用pip安装:

```bash
sudo pip install pymongo
pip show pymongo

# 终端显示
Name: pymongo
Version: 3.2
```
安装成功后，就可以在Python下，import pymongo了

参考[PyMongo的官方文档](http://api.mongodb.org/python/current/api/pymongo/mongo_client.html#pymongo.mongo_client.MongoClient)，pymongo的连接测试代码如下：

```python
>>> from pymongo import MongoClient
>>> c = MongoClient()
>>> c.test_database
Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), u'test_database')
>>> c['test-database']
Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), u'test-database')
>>> 
```

好了，可以用了 ：）