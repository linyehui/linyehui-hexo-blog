---
title: 'MediaRecorder.start failed: -19'
layout: post
tags:
---

## 问题描述

Android下，使用MediaRecorder进行视频录制，同一段代码在小米2S下代码运行正常，但是在Nexux S下会直接异常退出，log如下：
> 05-15 16:22:21.631: E/MediaRecorder(9265): start failed: -19
> 05-15 16:22:21.635: W/System.err(9265): java.lang.RuntimeException: start failed.
> 05-15 16:22:21.639: W/System.err(9265): 	at android.media.MediaRecorder.start(Native Method)

## 问题解决

### 去掉对VideoFrameRate的设置：

> mediaRecorder.setVideoFrameRate(24);
或者
> profile.videoFrameRate = Math.min(24);

**参考这个**：[Mediarecorder start failed -19](http://stackoverflow.com/questions/11249642/mediarecorder-start-failed-19)

### 不过我这还遇到了比其他网友更奇怪的问题：

直接使用MediaRecorer的set方法会异常退出，但是get CamcorderProfile后进行相应的修改就能正常运行

下面这两份代码的视频和音频格式参数都是一样的，但是上面这种写法会导致异常退出：
> 05-15 16:22:21.631: E/MediaRecorder(9265): start failed: -19
> 05-15 16:22:21.635: W/System.err(9265): java.lang.RuntimeException: start failed.
> 05-15 16:22:21.639: W/System.err(9265): 	at android.media.MediaRecorder.start(Native Method)

#### 失败


    // 失败 -19 异常退出
    CamcorderProfile profile = CamcorderProfile.get(CamcorderProfile.QUALITY_HIGH);
    mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
    mediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.H264);
    mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AAC);
    //mediaRecorder.setVideoFrameRate(24);
    mediaRecorder.setVideoEncodingBitRate(1000 * 1024);
    mediaRecorder.setAudioEncodingBitRate(42 * 1024);
    mediaRecorder.setAudioSamplingRate(44100);
    mediaRecorder.setAudioChannels(2);
    mediaRecorder.setVideoSize(640, 480);


#### 成功


    // 成功
    CamcorderProfile profile = CamcorderProfile.get(CamcorderProfile.QUALITY_HIGH);    		
    profile.fileFormat = MediaRecorder.OutputFormat.MPEG_4;
    profile.videoCodec = MediaRecorder.VideoEncoder.H264;
    profile.audioCodec = MediaRecorder.AudioEncoder.AAC;
            	
    // 设置videoFrameRate会导致Nexus S下异常退出-19 @linyehui
    //profile.videoFrameRate = 24;
    profile.videoBitRate = 1000 * 1024;
    profile.audioBitRate = 42 * 1024;
    profile.audioSampleRate = 44100;
    profile.audioChannels = 2;        

    mediaRecorder.setProfile(profile);
    mediaRecorder.setVideoSize(640, 480);


## 总结

会导致MediaRecorder start时-19异常退出，主要有两个原因：
1. mediaRecorder.setVideoSize设置的大小硬件不支持
**解决方案**：
先用getSupportedPreviewSizes获取硬件支持的分辨率再使用

2. mediaRecorder.setVideoFrameRate设置的参数硬件不支持
**解决方案**：
暂时没找到好的办法，我的方法是先不用
（应该有方法可以获取硬件支持的这个信息，然后根据硬件支持的参数来设置就可以了）

## 参考

[Error when Using Camera on Android 4/Nexus s](http://stackoverflow.com/questions/9271349/error-when-using-camera-on-android-4-nexus-s)  

[Android MediaRecorder - “start failed: -19”](http://stackoverflow.com/questions/10496969/android-mediarecorder-start-failed-19)  

[Mediarecorder start failed -19](http://stackoverflow.com/questions/11249642/mediarecorder-start-failed-19)  

[Error处理： android.media.MediaRecorder.start(Native Method) 报错：start failed: -19](http://blog.csdn.net/netwalk/article/details/17686993)  


-- End --