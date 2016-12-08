---
title: '使用ffmpeg对H.264/AAC视频进行处理'
layout: post
tags:
    - ffmpeg
    - h264
    - x264
    - rotate
---

# 1. 需求描述
Android手机拍摄的视频在PC上查看，视觉效果是被旋转了90度的；  

于是我们想要使用ffmpeg对视频进行90度旋转，并要求保持视频的编码格式：AVC/AAC；  

解决了这个问题后，还想用ffmpeg来进行更多的视频处理。

## 1.1 细化下来的需求点
* 视频旋转后大小不变
* 视频的编码格式和音频的编码格式不变
* 视频旋转后还可以继续在Chrome等HTML5浏览器中直接播放

## 1.2 视频的编码格式
```
视频流信息
+编码格式: AVC
+视频码率: 1065 kbps
+视频帧率：24 fps
+分 辨 率: 640 x 480
+显示比率: 1.333

音频流信息
+编码格式: A_AAC/MPEG4/LC
+音频码率: 57 kbps
+声 道 数: 2 channels
+采 样 数: 44100 Hz
+音频位数: 0 bits

```
这里要提下为什么使用这个格式，因为[HTML 5支持的视频格式就是这几个](http://www.w3schools.com/html/html5_video.asp)，而其中最通用的就是上面这个：H.264/AAC

# 2. 最后有用的命令行是这个：
```bash
# 视频转码成H.264，并旋转90度，音频格式保存不变
ffmpeg -i test.mp4 -vcodec h264 -c:a copy -vf "transpose=1" -strict -2 -y out.mp4

# 去掉rotate属性
ffmpeg -i test.mp4 -metadata:s:v rotate="0" -strict -2 -y r.mp4

# 裁剪视频

# 视频裁剪的宽高分别是(480,480)，裁剪视频的起始点是(0,0)
ffmpeg -i test.mp4 -vcodec h264 -c:a copy -filter:v "crop=480:480:0:0" -strict -2 -y ret.mp4

```

# 3. GPL的担忧和解决方案
使用命令行已经解决了我们的问题了，但是如果我们先使用ffmpeg作为库整合到我们的产品中会不会有开源协议上的风险呢？  


我们使用的视频编码格式-vcodec 是h264，突然想到一个念头，如果我们的产品中使用了ffmpeg进行视频旋转，那么会不会导致我们的产品需要遵循GPL全部开源呢？因为好像H.264的开源协议是GPL的！  


其实上面这句话是有问题的，因为没有区分清楚H.264和x264的关系：  

> H.264是一种编码规范，而x264是H.264的一种实现，x264使用GPL进行开源。
  

  

## 3.1 真正的担忧是：  

> 如果我们使用了ffmpeg来处理我们的视频，我们是否会因为使用了x264而导致产品必须遵循GPL进行开源
  


## 3.2 ffmpeg如果不使用x264能支持H.264编解码吗？不行

#### ffmpeg的解码是LGPL的，编码是GPL的(x264)
> The ffmpeg H.264 decoder is LGPL. Only the encoders are GPL, and x264 does not provide a decoder.
> It also has the advantage over Framewave and IPP of actually being usable.
  

  

#### 这一部分参考了下面的文章：

[H264 Encoders other than ffmpeg x264](http://stackoverflow.com/questions/1968790/h264-encoders-other-than-ffmpeg-x264)  

[Does using H.264 with ffmpeg make my software GPL and if so are there workarounds?](http://ffmpeg-users.933282.n4.nabble.com/Does-using-H-264-with-ffmpeg-make-my-software-GPL-and-if-so-are-there-workarounds-td934364.html)  

[Libav, licensing, h.264](http://comments.gmane.org/gmane.comp.video.ffmpeg.libav.user/10274)  

[Free/Open h.264 video decoding libraries? (Non-GPL)](http://stackoverflow.com/questions/668494/free-open-h-264-video-decoding-libraries-non-gpl)  



## 3.3 如果视频格式使用H.264，又想对视频进行处理，应该怎么办？

### 3.3.1 服务端使用
服务端使用ffmpeg进行视频处理，由于没有分发，不受GPL的限制，可以自由使用。  


### 3.3.2 终端使用
要对视频进行处理，就必然需要编码解码，而解码的话ffmpeg只能使用x264；  

使用了x264的ffmpeg必须遵循GPL进行软件分发，这对商业软件来说是个很大的门槛；  


### 3.3.3 综合下来的解决方案

#### 方案一
客户端直接录制H.264视频，但不进行视频处理，视频处理交给服务端来做。

#### 方案二
客户端入职视频采用非H.264格式，录制后本地就可以使用ffmpeg进行些简单的处理，上传到服务器后再进行H.264的转码，转码后再提供给HTML5播放。

#### 方案三
客户端录制H.264视频，本地使用x264的商业授权进行分发。

#### 方案四
客户端录制H.264视频，本地使用x264以外的其他视频库来编码视频。