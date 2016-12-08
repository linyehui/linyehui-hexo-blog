---
title: 'cocos2d下语音聊天和游戏音效冲突，导致playEffect没声音'
layout: post
tags:
    - cocos2d
    - ios
    - playEffect
---

## 问题描述
iOS下，游戏使用cocos2d-js 3.1和语音聊天，单独只有语音聊天或者只有游戏音效时，两者都正常，但是语音和音效同时使用时，就会有一方无法正常播放或者两者都无法正常播放。

## 问题排查
各种尝试后，发现了这篇文章：[调用录音后，游戏音效播放不出来的问题](http://www.imgeek.org/forum.php?mod=viewthread&tid=651)，这篇给了我们一个新的解决思路：  


会不会是iOS下AVAudioSession的category导致的问题？

在语音使用麦克风录音的前后，已经播放音效的前后，都将AVAudioSession的category属性Log打印出来，发现一旦我们使用了cocos2d-js提供的cc.audioEngine.playEffect方法，category就会变成kAudioSessionCategory_SoloAmbientSound，而像我们这种既有语音又有音效的情况，我们需要的其实是：AVAudioSessionCategoryPlayAndRecord。  


好了，找到直接的原因了，但是怎么改呢？

## 阅读代码，寻找解决方案
带着上面的问题，看下cocos2d-x的代码，看看能不能找到解决方案。

* 如果没有设置或CDaudioManager的configured，会使用默认的mode：kAMM_FxPlusMusicIfNoOtherAudio

```
# ./frameworks/js-bindings/cocos2d-x/cocos/audio/ios/CDAudioManager.m

// Init
+ (CDAudioManager *) sharedManager
{
    @synchronized(self)     {
        if (!sharedManager) {
            if (!configured) {
                //Set defaults here
                configuredMode = kAMM_FxPlusMusicIfNoOtherAudio;
            }
            sharedManager = [[CDAudioManager alloc] init:configuredMode];
            _sharedManagerState = kAMStateInitialised;//This is only really relevant when using asynchronous initialisation
            [[NSNotificationCenter defaultCenter] postNotificationName:kCDN_AudioManagerInitialised object:nil];
        }    

    }
    return sharedManager;
}
```

* 而默认的这个mode对应的正是AVAudioSessionCategorySoloAmbient  


```
-(void) setMode:(tAudioManagerMode) mode {
```

* 解决方案出来了  


```
# 调用下CDAudioManager的这个方法，初始化下我们的mode
+ (void) configure: (tAudioManagerMode) mode;
```

## 最终的解决方案

1. 确认工程配置的Header目录包含CDAudioManager.h所在的目录  

2. 在播放音效之前初始下CDAudioManager的mode为kAMM_PlayAndRecord  

> [CDAudioManager configure:kAMM_PlayAndRecord];

