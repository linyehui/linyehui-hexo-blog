---
title: '升级cocos2d-js 3.1到3.6'
layout: post
tags:
    - cocos2d
    - cocos2d-js
    - ios
    - android
---

## 问题描述
cocos2d-js 3.1的热更新有点问题，我们想升级到3.2；打开官网一看，哦~ 刚刚发布了3.6版本，既然升级就直接升到最新的吧（会是个坑吧……）

### 关于cocos2d-js 3.6更新了什么
从官网上可以看到3.1到3.6还是发生了很多事情：
[COCOS2D-JS 发布说明](http://www.cocos2d-x.org/docs/manual/framework/html5/release-notes/zh)  

另外还有论坛上的帖子：[Cocos2d-JS v3.6 已发布！](http://www.cocoachina.com/bbs/read.php?tid=297633)  


## 升级前的准备

### 升级前的版本
* cocos2d-js 3.1
* cocos ide 1.1.0
* cocos studio 1.6
* NDK r9d

### 升级到cocos2d-js 3.6需要升级的部分
* cocos2d-js 3.6
* cocos ide 1.2.0
* NDK r10c （不升级无法兼容Android 5.0，其他版本的Android还可以正常使用)
* cocos studio 2.2.0 官方声称兼容老的版本，所以我们这次没有升级，因为变化太大，但实际测试兼容还是有点小问题

## 升级步骤

##### 1.本次只更新cocos2d-js和ide，cocos studio保持1.6版本不变，需要下载下面这两个文件：  

* cocos2d-js-v3.6.zip
* cocos-code-ide-win64-1.2.0.exe，对应的Mac版本：cocos-code-ide-mac64-1.2.0.dmg  

##### 2.删除或者重命名cocod2d-3.1目录（这一步其实可以不做，只是方便环境没有切换过来的时候不至于使用到老的）  


##### 3.解压cocos2d-js-v3.6.zip，解压后执行下setup.py，执行后在命令行下执行下cocos命令，检查下路径是否正确  


##### 4.安装cocos ide（覆盖安装），并设置下cocos2d-js的新目录（首选项-》cocos-》Javascript）  


##### 5.从cocos2d-js目录下重新复制这4个目录到工程目录：  

frameworks/cocos2d-html5/
frameworks/js-bindings/
tools/bindings-generator/
tools/tojs/
##### 6.使用cocos ide或者xcode编译，第一次编译需要较长的时间


## 升级过程中遇到的问题

### JSB编译错误
主要是jsb开始使用智能指针，以前的代码需要改造下

找到两个官方的帖子：
[refactor binding code use JS::CallArgs instead of c style macro #1109](https://github.com/cocos2d/cocos2d-js/issues/1109)  

[Use SpiderMonkey template for GC thing instead of raw type #1139](https://github.com/cocos2d/cocos2d-js/issues/1139)  

[JSAPI Cookbook](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey/JSAPI_Cookbook?redirectlocale=en-US&redirectslug=SpiderMonkey%2FJSAPI_Phrasebook)  



#### 解决方案
```
    JS::CallArgs args = JS::CallArgsFromVp(argc, vp);
    args.rval().setUndefined();


    XXXObject *newObject = XXXObject::GetInstance();
    JS::CallArgs args = JS::CallArgsFromVp(argc, vp);

    js_proxy_t *p;
    HASH_FIND_PTR(_native_js_global_ht, &newObject, p);
    if (!p) {
        JSObject *obj = JS_NewObject(cx, JSB_XObject_class, JS::RootedObject(cx, JSB_XObject_object), JS::NullPtr());
        p = jsb_new_proxy(newObject, obj);
        JS::AddNamedObjectRoot(cx, &p->obj, JSB_MODULE_OS_HELPER);
    }

    args.rval().set(OBJECT_TO_JSVAL(p->obj));




    // define name space
    JS::RootedObject myBinding(cx, JS_NewObject(cx, NULL, JS::NullPtr(), JS::NullPtr()));
    JS::RootedValue myBindingVal(cx);
    myBindingVal = OBJECT_TO_JSVAL(myBinding);
    JS_SetProperty(cx, obj, "XXXHandleBinding", myBindingVal);
    JsbVoiceHandleObjectCreateClass(cx, myBinding, "XXXHandleObject");



static void JsbVoiceHandleObjectCreateClass(JSContext *cx, JS::HandleObject globalObj, const char* name)

    JSContext* jc = ScriptingCore::getInstance()->getGlobalContext();
    JS::RootedValue retval(jc);

    bool ok = true;
    int32_t arg0 = 0;
    ok &= jsval_to_int32(cx, args[0], &arg0);

```

### 向导生成的文件升级了
主要是class根目录以及class/runtime目录下的文件，解决方案就是用新版本的cocos ide生成一个新的带native的工程，对比下新旧文件，覆盖过来就可以了

#### Android还需要往.mk文件中加下文件
```
../../Classes/runtime/ConnectWaitLayer.cpp \
../../Classes/runtime/ConsoleCommand.cpp \
../../Classes/runtime/FileServer.cpp \
```


### 编译通过后提示UI ExportJson文件找不到了
原因是3.3版本的时候升级了从xxxSence.json文件找ui文件的算法，以前都是用全路径，现在改成了相对于xxxSence.json的相对路径，所以文件都找不到了


#### 解决方案
我们其实没有使用xxxSence.json，可以试下全部直接使用xxxExportJson

修改前后的代码对比

>
//        var node = ccs.sceneReader.createNodeWithSceneFile(res.login_scene);  

//        var widget = node.getChildByTag(10003).getComponent("LoginComponent").getNode();  

        var widget = ccs.uiReader.widgetFromJsonFile(res.LoginUI_ExportJson);  

        this.addChild(widget);  



虽然cocos官方说支持cocos studio 1.6版本，但是还是会有些布局上的问题，需要针对3.6进行下适配。

### Cocos2D-X 3.6 android与友盟 zlib 冲突，无法编译！
[Cocos2D-X 3.6 android与友盟 zlib 冲突，无法编译！](http://bbs.umeng.com/thread-8639-1-1.html)

这个目前只能呢个等待umeng官网更新版本支持。

#### 2015-05-08更新
umeng的跟进还是挺快的，他们更新了一个兼容cocos2d-x 3.6的测试版本，可以从[这里](http://dev.umeng.com/game_analytics/game-c2d/sdk-download)下载到（测试版本，后续可能会被更新掉）：   


```
cocos2dx 游戏统计分析SDK使用 v4.1 （2015-05-07）
功能更新:
新SDK使用新协议(Thrift)
重构SDK数据采集的逻辑框架，提高SDK稳定性；
采用了全新的账号统计接口
支持cocos2dx 3.6
支持按间隔发送数据策略
```

