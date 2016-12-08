---
title: 'How To Port Code To WinRT'
layout: post
tags:
    - appcert
    - certification
    - win8
    - winrt
---

**首先要明确下迁移成功的标准：通过Windows App Certification Kit的Validate Metro style App**

# 问题一：App安装后，无法在认证列表中找到
这个问题很诡异，我换了一台机器就好了，完全不知道什么原因，可以参照下下面这个帖子，看看过段时间微软的人会不会答复这个问题。  

[Prerequisites for exposing metro applications to the Windows Logo Certification GUI application? ](http://social.msdn.microsoft.com/Forums/en-US/toolsforwinapps/thread/32ea893d-2e34-44f9-973f-5c3819141a7a)

# 问题二：References没有Debug和Release之分，打包和认证时要特别留意
如果用到了动态链接库，Deploy和Package VS都会把References那里指定的那个DLL打包过去！  

注意：Debug和Release只能选择一个！这个问题不留意的话会遇到非常诡异的问题，编译没问题，调用时抛出莫名其妙的Exception

## 在解决认证的这个问题时我发现了这么一个诡异的问题：
> 认证的结果显示用到了不被支持的API，解决问题的方法比较直接，无非就是去掉对这些函数的使用，但是我发现我明明已经不用这个函数了，但是检测还是不通过
  Use of Supported Platform APIs 
  FAILED Supported APIs
  Error: This application failed the supported API check.
### 最后发现原因是这样：
1. 我拿来认证的是Release版本，但是References里面绑定的文件没有Debug和Release之分，当时指定的是Debug的
2. Package的时候会把Reference的那个DLL打包进去，我改的是Release，实际打包的时Debug的那个Dll
3. 明白了问题的原因，改起来就很明显了，坑爹！改了下References中的引用，改了就通过了

## Unsupported API的替换方案
```
CreateEvent->CreateEventEx
CreateFileW->CreateFile2
CreateMutexW->CreateMutexEx
WaitForSingleObject->WaitForSingleObjectEx
GetTickCount->(DWORD)GetTickCount64
DeleteFileA->DeleteFileW
FormatMessageA->FormatMessageW
GetDiskFreeSpaceA->GetDiskFreeSpaceW
GetFileAttributes->GetFileAttributesEx
GetFileSize->GetFileInformationByHandleEx
GetFullPathName-> :(
GetTempPathA-> :(
GetVersionExW-> :(
InitializeCriticalSection->InitializeCriticalSectionEx
LoadLibrary->don't use this at best,alothough ther is LoadPackagedLibrary can use
LockFile->LockFileEx
UnLockFile->UnLockFileEx
OutputDebugStringA-> don't use
SetFilePointer->SetFilePointerEx
SetThreadPriority-> :(
```

## 特别说明下GetFileSize的替代方案的实现：

```cpp
    DWORD WinRTGetFileSize(_In_ HANDLE hFile, _Out_opt_ LPDWORD lpFileSizeHigh)
    {
    	FILE_STANDARD_INFO stInfo = {0};
    	if (GetFileInformationByHandleEx(hFile, FileStandardInfo, &stInfo, sizeof(stInfo )))
    	{
    		*lpFileSizeHigh = stInfo.EndOfFile.HighPart;
    		return stInfo.EndOfFile.LowPart;
    	}
    

    	return INVALID_FILE_SIZE;
    }
```


## WinRT下支持的Win32API列表可以从这里查到
(find Win32 API which is supported by WinRT here):  

[Win32 and COM for Metro style apps](http://msdn.microsoft.com/en-us/library/windows/apps/br205757.aspx)  
