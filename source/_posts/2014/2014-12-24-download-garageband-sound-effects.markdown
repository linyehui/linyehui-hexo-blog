---
title: 'GarageBand 音效库下载'
layout: post
tags:
    - garageband
    - mac
---

# 遇到的问题
下载GarageBand内容，下载GarageBand音效库，很慢！很慢！很慢！  

我这提示需要125个小时！  

而且还总是下载失败！  


![Alt text](/media/files/2014/12/24/garageband_125_hours.png)

# 解决方案

#### 1.打开GarageBand开始下载

#### 2.使用lsof命令查看GarageBand把文件下载到那里去了
* 内容太多，所以先用ps命令找到GB的进程ID，我这里是291  

* 然后再使用lsof，并把输出重定向到log.txt中去  

```
ps aux | grep GarageBand  

lsof -g 291 > ~/log.txt  

```

* 打开log.txt，搜索.pkg，你会找到类似下面的路径，对了，这个就是下载的临时文件
```
/private/var/folders/37/1bzl8_j50wxcxgmj0dv805lm0000gn/C/com.apple.garageband10/com.apple.MusicApps/audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GarageBandPremiumContent.pkg
```

#### 3.退出GarageBand，得到下载地址，使用第三方工具下载好pkg文件
* 退出GarageBand，取消下载，GarageBand会在下载的临时文件旁边生成这么一个文件，文件中有实际的下载地址  


** MAContent10_GarageBandCoreContent_v3.pkg.resumeData **

```
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GarageBandPremiumContent.pkg
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GarageBandCoreContent_v3.pkg
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GB_StereoDrumKitsSongWriter.pkg

```
* 使用第三方下载工具，下载这个ULR对应的文件  


#### 4.下载后了之后，有两种方式进行安装

##### 4.1 方案一：替换文件
把下载到的文件替换到第2步得到的路径上，修改.resumeData中的文件大小，然后重启GarageBand。  


##### 4.2 方案二：删除临时文件，双击pkg安装
删除步骤2得到的目录下的文件，然后双击下载好的pkg，安装后，打开GarageBand。  


#### 5.遗留问题
安装后还是会提示需要下载，而且看临时文件还是在下载MAContent10_GarageBandPremiumContent.pkg，太奇怪了

# 参考
[2013新版GarageBand下载基本音乐、声效库缓慢的解决办法](http://bbs.feng.com/read-htm-tid-7164986.html)  

[天朝重装GarageBand下载几十个小时基础包的盆友们进来看](http://bbs.feng.com/read-htm-tid-8038836.html)  

[[原创]Logic Pro X全部扩展包下载地址 34.3GB](http://www.audiobar.net/viewthread.php?tid=416782)  

[logic-x-mainstage-3-content](https://s3.amazonaws.com/bwhli001/files/logic-x-mainstage-3-content.txt)  

[GarageBand全部素材包下载地址](http://blog.sina.com.cn/s/blog_8c6f1bae0102vbce.html)  

[GarageBand及随附增值包快速安装方法](http://www.worldu.me/iLife/138.html)  
