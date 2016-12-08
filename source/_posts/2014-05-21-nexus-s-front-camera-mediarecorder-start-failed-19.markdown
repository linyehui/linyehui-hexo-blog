---
title: 'Nexus S 前置摄像头MediaRecorder start failed -19'
layout: post
tags:
    - android
    - mediarecorder
    - nexus
---

# 问题描述

前几天解决了Android下的这个问题：[MediaRecorder.start failed: -19](http://www.linyehui.com/mediarecorder-start-failed-19)，后面发现没有彻底解决：
**在Nexus S这部“神机”下，如果使用前置摄像头进行录像，还是会出现start failed -19**

# 解决方案

判断是Nexus S，而且使用的是前置摄像头，调用setVideoFrameRate设置为15，解决。
参考文章是这篇：[Android cant record video with Front Facing Camera, MediaRecorder start failed: -19](http://stackoverflow.com/questions/14681703/android-cant-record-video-with-front-facing-camera-mediarecorder-start-failed)

**原文是这样的：**
> So, if I use the QUALITY_LOW profile AND set the frame-rate to 15 or lower, it magically works.
> 
> mediaRecorder.setProfile(CamcorderProfile.get(CamcorderProfile.QUALITY_LOW));
> mediaRecorder.setVideoFrameRate(15);
> Infact any value, 1 - 15 works. Which seems weird.

**不过我用CamcorderProfile.QUALITY_HIGH也是没问题的，关键是mediaRecorder.setVideoFrameRate(15);**

## 贴下我的代码：


    // ... 此处省略代码若干
    
                CamcorderProfile profile = CamcorderProfile.get(CamcorderProfile.QUALITY_HIGH);
        		
            	profile.fileFormat = MediaRecorder.OutputFormat.MPEG_4;
                profile.videoCodec = MediaRecorder.VideoEncoder.H264;
                profile.audioCodec = MediaRecorder.AudioEncoder.AAC;
    
                // 设置videoFrameRate会导致Nexus S后置摄像头异常退出-19
                // 但不设置，或者设置的不是15以下的值，Nexus S前置摄像头会异常退出-19
                // @linyehui
                if (isModelNexusS())
                {
                	if (!cameraManager.isUseBackCamera())
                	{
                        profile.videoFrameRate = 15;
                	}
                }
                else
                {
                	profile.videoFrameRate = Math.min(HJW_VIDEO_FRAME_RATE, profile.videoFrameRate);
                }
    
                // ... 此处省略代码若干
    
    	private boolean isModelNexusS() {
    		 final String MODLE_NEXUS_S = "Nexus S";
    		 Log.i(LOG_TAG, "MODEL: " + android.os.Build.MODEL);
    		 
             if (android.os.Build.MODEL.equalsIgnoreCase(MODLE_NEXUS_S))
            	 return true;
             else
            	 return false;
    	}


# 小结

我目前的做法就是对Nexus S这种神机进行适配：
* 如果是Nexus S，前置摄像头，设置videoFrameRate 为 15
* 如果是Nexus S，后置摄像头，就不要设置videoFrameRate 
* 如果不是Nexus S，正常设置videoFrameRate 即可

-- End --