---
title: 'Build SRS on Mac'
layout: post
tags:
    - srs
    - mac
    - rtmp
---

## 需求
1、在Mac下编译SRS的RTMP服务器

2、用OBS往我们自己搭建的服务器上推流

3、用RTMP播放器（比如VLC），观看服务器上RTMP流

## 取代码
官方git仓库已经不维护了：https://github.com/ossrs/srs

我使用的是官方推荐的第一个fork：https://github.com/smartdu/srs

由于代码比较大，挂个代理git clone成功率会高很多，这里顺便推荐下神器：proxychains4

## 编译参考文档
https://github.com/ossrs/srs/wiki/v1_CN_SampleRTMP

##### PS.
如果之前用不同的配置编译过，那么可以这样子清理下：

```
cd trunk
make clean
rm -f Makefile
```

## 编译

#### 用configure脚本来生成Makefile
```bash
cd ./srs/trunk

# 看下配置的选项

./configure -h

# 指定Mac编译，并关闭其他option
./configure --osx --without-ssl --without-hls --without-hds --without-dvr --without-nginx --without-http-callback --without-http-server --without-stream-caster --without-http-api --without-ffmpeg --without-transcode --without-ingest --without-stat --without-librtmp

make
```

一切顺利的话，编译通过，会得到了./objs/srs这个可执行文件

## 运行SRS服务器

#### srs.conf
由于Mac系统的限制，运行SRS的话需要把./conf/srs.conf中的并发数修改下，我修改成100:

```bash
#srs.conf中的这一行需要修改下：
max_connections     100;
```

#### rtmp服务器跑起来
```bash
./objs/srs -c ./conf/srs.conf

#跑起来后要留意下log，配置存在错误的话，srs进程会关闭掉
tail -f ./objs/srs.log

#检查下srs进程是否正常运行
ps aux | grep srs
```

#### 推流
直接使用OBS或者其他RTMP推流工具，填写下：

```
URL：rtmp://10.0.0.54/live
流密钥：livestream
```

#### 看RTMP流视频
直接用VLC打开这个地址就可以了:

```
rtmp://10.0.0.54/live/livestream
```

### 小结
到此Mac上最精简的SRS算是编译通过，且能正常跑起来了~