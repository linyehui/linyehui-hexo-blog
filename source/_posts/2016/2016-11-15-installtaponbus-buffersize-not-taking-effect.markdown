---
title: '使用AudioEngine时installTapOnBus的bufferSize设置无效'
layout: post
tags:
    - audioengine
    - installtaponbus
    - ios
---

## 需求
1、使用AudioEngine进行音频采集，并加入部分EQ等效果器

2、将混音后的音频输出为PCM的Buffer

3、将PCM Buffer编码为AAC

## 遇到的坑

AudioEngine的这个方法有Bug: [installTapOnBus](https://developer.apple.com/reference/avfoundation/avaudionode/1387122-installtaponbus)

```
- (void)installTapOnBus:(AVAudioNodeBus)bus bufferSize:(AVAudioFrameCount)bufferSize format:(AVAudioFormat * __nullable)format block:(AVAudioNodeTapBlock)tapBlock;
```

iOS 10一下的版本，installTapOnBus的第二个参数bufferSize设置是无效的，
比如我设置bufferSize为1024，如果我的format中的bytePerFrame是4，那么最后出来的bufferSize应该是1024*4=4096才对，但是在iOS 10一下的版本，出来的bufferSize每次都是66148

如果我们要进行AAC编码，那么AAC编码的bufferSize必须是能够被1024整除的，而66148是无法被1024整除

如果AAC编码的时候没有对齐bufferSize和1024，就会出现音频数据丢失的情况

### 解决这个问题的两个角度
1、让installTapOnBus出来的数据能够被1024整除

2、自己用做对齐的事情，那么部分音频数据的采集时间戳就需要自己构造

我个人比较偏向第一种，这样就不存在构造时间戳的问题。

## 解决方案

### 第一步：installTapOnBus的Block中设置frameLength

```
const uint32_t frameLength = 1024;
[_inputNode installTapOnBus:0 bufferSize:frameLength format:[_inputNode outputFormatForBus:0] block:
	^(AVAudioPCMBuffer * _Nonnull buffer, AVAudioTime * _Nonnull when) {
		// iOS 10以下需要这么设置才可以缩短Block回调的Buffer大小和同时也能缩短Block回调的时间间隔
		// linyehui 2016-11-11
		buffer.frameLength = frameLength;
}];
```


#### 参考：[I want to call 20 times per second the installTapOnBus:bufferSize:format:block:](http://stackoverflow.com/questions/26115626/i-want-to-call-20-times-per-second-the-installtaponbusbuffersizeformatblock)


### 第二步：不要使用AVAudioPCMBuffer的mutableAudioBufferList

如果你使用了步骤一的代码，那么installTapOnBus回调出来的AVAudioPCMBuffer，不要使用mutableAudioBufferList，必须使用audioBufferList。

因为mutableAudioBufferList的mDataByteSize是frameCapacity，而audioBufferList的mDataByteSize是真实的frameLength

