---
title: 'MediaRecorder视频录制旋转90度的问题'
layout: post
tags:
    - android
    - mediarecorder
    - video
---


## 需求

在Android下录制视频，录制的视频在手机（iOS和Android）上预览、播放正常;
在手机浏览器中播放正常;
在PC上的浏览器和播放器播放正常。

-----

## 遇到的问题

### 预览的时候视频需要旋转90°

这个可以解决，设置下摄像头显示的方向就可以了：

    // private Camera camera = null;
    // 修改摄像头参数就能解决预览视频的旋转90°问题
    // 具体是90还是270我这里就不具体说了，网上很多代码
    camera.setDisplayOrientation(90);

参考文章：[android中Camera setDisplayOrientation使用](http://blog.csdn.net/fengye810130/article/details/11614427)

### 视频文件保存的时候需要旋转90°

导致这个问题的原因是使用MediaRecorder录视频保存的文件都是“横版”的，也就是说：如果你是竖着录制视频，录制保存下来的视频本来就是“旋转了90度的”，这个问题怎么解决呢？

#### 一种有缺陷的解决方案

Android 2.3(API Level 9)之后提供了setOrientationHint方法，但这并不是完美的方案，不是所有的播放起都支持这个设置，看下官方文档:[setOrientationHint](http://developer.android.com/reference/android/media/MediaRecorder.html#setOrientationHint)

> Sets the orientation hint for output video playback. This method should be called before prepare(). This method will not trigger the source video frame to rotate during video recording, but to add a composition matrix containing the rotation angle in the output video if the output format is OutputFormat.THREE_GPP or OutputFormat.MPEG_4 so that a video player can choose the proper orientation for playback. Note that some video players may choose to ignore the compostion matrix in a video during playback.

关键是这句：
> Note that some video players may choose to ignore the compostion matrix in a video during playback.

这就是我为什么说这是一种有缺陷的解决方案，但这个方案足够简单，只用Android SDK就够了，一句代码搞定：


    // private MediaRecorder mediaRecorder = null;
    // 用VideoView,Safari,QuickTime Player播放视频，视频角度是正确的
    // 用Chrome,QQPlayer,MPlayerX播放视频，视频需要旋转90°才正确
    mediaRecorder.setOrientationHint(90);

**补充说明：**
1. 使用这个方案并不能完美解决问题，在Chrome,QQPlayer,MPlayerX等播放器中，视频都无法按照“预设的角度”被播放，因为这些播放器并不支持setOrientationHint的这种格式设置；
2. 如果非要使用这个方案，可以在HTML5中使用CSS对video标签进行旋转，但这样会把整个标签，包括控制条都旋转掉，有个猥琐的解决方案是不显示自带的控制条，而是自己实现简单的控制（播放，暂停）

参考文章：[Chrome HTML5 Video Flipping Portrait Sideways](http://stackoverflow.com/questions/12486870/chrome-html5-video-flipping-portrait-sideways)

#### 可能的完美方案(未验证)

使用FFMpeg或者OpenCV对录制的视频进行旋转
1. 对录制后的文件进行直接的FFMpeg旋转
2. 对录制过程中的每一帧进行旋转，最后生成的文件自然也是正确旋转了的

有个有点沾边的参考文章：[https://groups.google.com/forum/#!msg/javacv/J0O6jV8kK9k/cu0iY6fyNWAJ Can I set the orientation of a video when recording with FfmpegFrameRecorder?]


未完待续……