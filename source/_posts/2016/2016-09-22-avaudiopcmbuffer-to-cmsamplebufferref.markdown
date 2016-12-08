---
title: 'AVAudioPCMBuffer to CMSampleBufferRef'
layout: post
tags:
    - audio
    - ios
    - pcm
---

## 需求
把AVAudioPCMBuffer转换层CMSampleBufferRef，同理也可以从AudioBufferList加上AudioStreamBasicDescription转换成CMSampleBufferRef

## 实现代码
实现代码如下：

```

- (CMSampleBufferRef)createAudioSampleBufferFrom:(AVAudioPCMBuffer *) pcmBuffer {
	AudioBufferList *audioBufferList = [pcmBuffer mutableAudioBufferList];
	AudioStreamBasicDescription asbd = *pcmBuffer.format.streamDescription;

	CMSampleBufferRef sampleBuffer = NULL;
	CMFormatDescriptionRef format = NULL;

	OSStatus error = CMAudioFormatDescriptionCreate(kCFAllocatorDefault, &asbd, 0, NULL, 0, NULL, NULL, &format);
	if (error != noErr) {
		return nil;
	}

	CMSampleTimingInfo timing = { CMTimeMake(1, asbd.mSampleRate), kCMTimeZero, kCMTimeInvalid };
	error = CMSampleBufferCreate(kCFAllocatorDefault,
								 NULL, false, NULL, NULL, format,
								 pcmBuffer.frameLength,
								 1, &timing, 0, NULL, &sampleBuffer);
	if (error != noErr) {
		CFRelease(format);
		NSLog(@"CMSampleBufferCreate returned error: %d", (int)error);
		return nil;
	}

	error = CMSampleBufferSetDataBufferFromAudioBufferList(sampleBuffer, kCFAllocatorDefault, kCFAllocatorDefault, 0, audioBufferList);
	if (error != noErr) {
		CFRelease(format);
		NSLog(@"CMSampleBufferSetDataBufferFromAudioBufferList returned error: %d", (int)error);
		return nil;
	}

	CFRelease(format);
	return sampleBuffer;
}

```

## 内存释放
生成的CMSampleBufferRef引用计数为1，记得释放，类似：

```

CMSampleBufferRef sampleBuffer = [self createAudioSampleBufferFrom:pcmBuffer];

// do somthing with sampleBuffer

CFRelease(sampleBuffer);

```

## AudioBufferList
关于AudioBufferList，需要说的是PCM中的双通道，对应到AudioBufferList就是mNumberBuffers

假定存在

> AudioBufferList *ioData = ...
AudioStreamBasicDescription asbd = ...

如果ioData->mNumberBuffers为2，那么有以下几个推导：

* 1、ioData->mBuffers[0]有数据外，ioData->mBuffers[1]也有数据；
* 2、跟AudioBufferList对应的asbd->mChannelsPerFrame == 2
* 3、还要补充一点的是，asbd.mChannelsPerFrame == 2的时候，asbd.mBytesPerFrame并不会包含2个channel的数据

```

asbd.mBytesPerFrame = asbd.mBitsPerChannel/8; // 正确
asbd.mBytesPerFrame = asbd.mBitsPerChannel/8 * mChannelsPerFrame; // 错误

```

## 参考文档
[CMSampleBuffer](https://developer.apple.com/reference/coremedia/1669345-cmsamplebuffer?language=objc)
[iOS 音频采集 AudioBufferList转CMSampleBufferRef](http://blog.csdn.net/wnnvv/article/details/50865201)
[CMSampleBufferSetDataBufferFromAudioBufferList returning -12731](https://forums.developer.apple.com/thread/11418)
[osstatus](https://www.osstatus.com/search/results?platform=all&framework=all&search=-12737)