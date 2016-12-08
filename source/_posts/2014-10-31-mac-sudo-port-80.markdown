---
title: 'Mac OS X下的80端口问题'
layout: post
tags:
    - Mac
    - web.py
    - port
---

# 需求
Mac OS X下调试iOS模拟器中的服务端API请求，想通过修改host来使用本地web.py替换远程服务器，为了让手机端的代码不需要修改，需要让web.py跑在80端口.

# 遇到的问题
> python code.py 80

提示错误如下，但是如果把80改成别的端口就是好的  


```
http://0.0.0.0:80/
Traceback (most recent call last):
  File "hello.py", line 15, in <module>
    app.run()
  File "/Library/Python/2.7/site-packages/web.py-0.37-py2.7.egg/web/application.py", line 313, in run
    return wsgi.runwsgi(self.wsgifunc(*middleware))
  File "/Library/Python/2.7/site-packages/web.py-0.37-py2.7.egg/web/wsgi.py", line 54, in runwsgi
    return httpserver.runsimple(func, validip(listget(sys.argv, 1, '')))
  File "/Library/Python/2.7/site-packages/web.py-0.37-py2.7.egg/web/httpserver.py", line 157, in runsimple
    server.start()
  File "/Library/Python/2.7/site-packages/web.py-0.37-py2.7.egg/web/wsgiserver/__init__.py", line 1753, in start
    raise socket.error(msg)
socket.error: No socket could be created

```

# 问题分析

## 是不是端口占用的问题？
用下面的命令查看了下，并没有进程占用80端口啊！  


```
MAC下查看端口是否被占用的方法：

方法一：
//查看80口是否被占用
sudo lsof -i :80

方法二：
netstat -anp tcp | grep 80

如下命令可以直接结束占用端口的所有进程：
lsof -P | grep ':80' | awk '{print $2}' | xargs kill -9
```

#### 参考:
[MAC下查看端口是否被占用](http://www.qinbin.me/mac%E4%B8%8B%E6%9F%A5%E7%9C%8B%E7%AB%AF%E5%8F%A3%E6%98%AF%E5%90%A6%E8%A2%AB%E5%8D%A0%E7%94%A8/)  

[关闭Web共享](http://www.teanie.com/how-to-solve-mac-os-port-80-is-already-in-use-problem.html)  


## 权限问题！
找到一篇文章后才知道，原来是类Linux系统下的安全策略设计就是这样的，自己对Linux的使用还是太少了！

> Normally only root processes can bind to port 80 (or to any port below 1024).

#### 参考:
[How can I open port 80 so a non-root process can bind to it?](http://superuser.com/questions/304206/how-do-i-start-nginx-on-port-80-at-os-x-login)  


# 解决方案
试了下sudo，成功了 囧rz

> sudo python code.py 80

我只是为了本地调试，用sudu是最方便的方法，如果是服务器上的话可以尝试[上面那个问答](http://superuser.com/questions/304206/how-do-i-start-nginx-on-port-80-at-os-x-login)中的方案。