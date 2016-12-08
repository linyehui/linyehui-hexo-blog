---
title: 'Android拍摄视频异常处理'
layout: post
tags:
---

遇到了好多异常，逐一介绍解决方案如下。

# java.lang.RuntimeException: autoFocus failed at android.hardware.Camera.native_autoFocus

### autoFocus 时机不对导致的Crash，异常信息:


    java.lang.RuntimeException: autoFocus failed at android.hardware.Camera.native_autoFocus

### 得到的用户反馈描述：

Sony s39h 一点拍摄视频就crash，从他们上传上来的log看是autoFocus的时候crash了，找了下资料，这个比较接近：[Android Camera.autoFocus() failed](http://www.hitziger.net/blog/android-camera-autofocus-failed/)
### 参考上面的文章在我们的代码中发现了一个调用时机不对的autoFocus()，去掉，搞定。


# java.lang.RuntimeException: startPreview failed

### 老的系统版本需要调用SurfaceView的 setType，异常信息：



    06-19 21:25:28.730: E/AndroidRuntime(1082): FATAL EXCEPTION: main
        06-19 21:25:28.730: E/AndroidRuntime(1082): java.lang.RuntimeException: startPreview failed
        06-19 21:25:28.730: E/AndroidRuntime(1082): 	at android.hardware.Camera.startPreview(Native Method)
        06-19 21:25:28.730: E/AndroidRuntime(1082): 	at com.duowan.huanjuwan.app.truthordare.MediaActivity$1.surfaceChanged(MediaActivity.java:413)

## 解决方案

**只需要加上setType这个修改就可以了**，但是代码加的位置有讲究，要在getHolder().addCallback之后加才可以

    getHolder().addCallback(this);
    getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);


## 心路历程

解决问题的过程比结果更优价值 :)

### 搜索到描述最接近的一文章：

[Android Camera will not work. startPreview fails](http://stackoverflow.com/questions/7942378/android-camera-will-not-work-startpreview-fails)
试了下里面的代码，没有解决

### 其他的参考文章：

[Issue 7909:Camera preview does not work on HTC Desire](http://code.google.com/p/android/issues/detail?id=7909)
[java.lang.RuntimeException: startPreview failed](http://www.cnblogs.com/dyingbleed/archive/2012/12/04/2801621.html)

### 再试下这个：


    #34 paul.won...@gmail.com
    Hi everyone,
    

    I'm new to Android but encountered the same problem.  Could it be the case that the width and height parameters are not called properly?  I added the following two lines before parameters.setPreviewSize and it now seems to work fine.
    

    w = parameters.getPreviewSize().width;
    h = parameters.getPreviewSize().height;
            

    For the record, I am using a HTC Desire too.
    

    Cheers,
    

    Paul

还是不行


### 尝试下用Demo来调试

Demo加了两个地方，成功跑起来了，要看下能不能具体到只加一个就可以


    @SuppressWarnings("deprecation")
        	public void initCameraManager(Context paramContext) {
        		cameraManager = ((MainActivity) paramContext).getCameraManager();
        		getHolder().addCallback(this);
        		getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
        	}



    int preview_width = parameters.getPreviewSize().width;
        			int preview_height = parameters.getPreviewSize().height;
        			parameters.setPreviewSize(preview_width, preview_height);
        			Log.e("video", "Width: " + preview_width + ", height: " + preview_height);
        			//parameters.setPreviewSize(640, 480);
        			camera.setParameters(parameters);

**确认了：只需要加上setType这个修改就可以了**，比较奇怪的是我昨天在我们的产品工程加了，但还是crash了，可能是加的位置不对
> getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);

### 回我们的产品工程再验证下

果然是加的位置不对，要在getHolder().addCallback之后加才可以

    getHolder().addCallback(this);
    getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);


# java.lang.NoSuchFieldError: android.os.AsyncTask.THREAD_POOL_EXECUTOR

### THREAD_POOL_EXECUTOR在Level 11后才有，需要做系统版本判断再调用，异常信息:


    06-20 12:36:06.410: E/AndroidRuntime(1613): FATAL EXCEPTION: main
        06-20 12:36:06.410: E/AndroidRuntime(1613): java.lang.NoSuchFieldError: android.os.AsyncTask.THREAD_POOL_EXECUTOR
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at com.duowan.huanjuwan.app.camera.AutoFocusManager.onAutoFocus(AutoFocusManager.java:67)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at android.hardware.Camera$EventHandler.handleMessage(Camera.java:559)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at android.os.Handler.dispatchMessage(Handler.java:99)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at android.os.Looper.loop(Looper.java:130)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at android.app.ActivityThread.main(ActivityThread.java:3703)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at java.lang.reflect.Method.invokeNative(Native Method)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at java.lang.reflect.Method.invoke(Method.java:507)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:841)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:599)
        06-20 12:36:06.410: E/AndroidRuntime(1613): 	at dalvik.system.NativeStart.main(Native Method)


### 参考这个文章解决了：[Android 2.3.3 AsyncTask call throws NoSuchFieldError](http://stackoverflow.com/questions/12227682/android-2-3-3-asynctask-call-throws-nosuchfielderror)


    @TargetApi(11)
      static public <T> void executeAsyncTask(AsyncTask<T, ?, ?> task,
                                              T... params) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
          task.executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR, params);
        }
        else {
          task.execute(params);
        }
      }


# 可以启动并预览，但是一开始录制视频，画面就卡住，没Crash

### 异常信息：


    06-20 12:53:07.766: E/AndroidRuntime(1682): FATAL EXCEPTION: main
        06-20 12:53:07.766: E/AndroidRuntime(1682): java.lang.OutOfMemoryError: bitmap size exceeds VM budget
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.graphics.BitmapFactory.nativeDecodeByteArray(Native Method)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.graphics.BitmapFactory.decodeByteArray(BitmapFactory.java:419)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.graphics.BitmapFactory.decodeByteArray(BitmapFactory.java:432)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at com.duowan.huanjuwan.app.truthordare.MediaActivity$9.onPictureTaken(MediaActivity.java:446)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.hardware.Camera$EventHandler.handleMessage(Camera.java:529)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.os.Handler.dispatchMessage(Handler.java:99)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.os.Looper.loop(Looper.java:130)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at android.app.ActivityThread.main(ActivityThread.java:3703)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at java.lang.reflect.Method.invokeNative(Native Method)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at java.lang.reflect.Method.invoke(Method.java:507)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:841)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:599)
        06-20 12:53:07.766: E/AndroidRuntime(1682): 	at dalvik.system.NativeStart.main(Native Method)
        06-20 12:53:08.207: W/AutoFocusManager(1682): Unexpected exception while focusing
        06-20 12:53:08.207: W/AutoFocusManager(1682): java.lang.RuntimeException: autoFocus failed
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at android.hardware.Camera.native_autoFocus(Native Method)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at android.hardware.Camera.autoFocus(Camera.java:650)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at com.duowan.huanjuwan.app.camera.AutoFocusManager.start(AutoFocusManager.java:82)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at com.duowan.huanjuwan.app.camera.AutoFocusManager$AutoFocusTask.doInBackground(AutoFocusManager.java:118)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at android.os.AsyncTask$2.call(AsyncTask.java:185)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:306)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at java.util.concurrent.FutureTask.run(FutureTask.java:138)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1088)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:581)
        06-20 12:53:08.207: W/AutoFocusManager(1682): 	at java.lang.Thread.run(Thread.java:1019)
        06-20 12:53:10.198: I/Process(1682): Sending signal. PID: 1682 SIG: 9
        06-20 12:53:10.539: I/dalvikvm(1884): Could not find method android.view.Display.getSize, referenced from method com.duowan.huanjuwan.app.common.Utils.getDisplaySize
        06-20 12:53:10.549: W/dalvikvm(1884): VFY: unable to resolve virtual method 7806: Landroid/view/Display;.getSize (Landroid/graphics/Point;)V
        06-20 12:53:10.549: D/dalvikvm(1884): VFY: replacing opcode 0x6e at 0x0017


### 结论是老的机器内存不足，使用之前清理下内存就好了，这个就不是单纯一两行代码的问题，整个应用的内存使用都要Review下。



----------The End-----------