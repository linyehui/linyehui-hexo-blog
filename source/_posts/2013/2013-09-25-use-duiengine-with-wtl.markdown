---
title: '在WTL向导工程中使用DuiEngine'
layout: post
tags:
    - duiengine
    - ui
    - wtl
---

# 前言


假设你清楚的理解什么是[WTL](http://wtl.sf.net/)，什么是[DuiEngine](http://duiengine.googlecode.com/)，那么请继续往下看 :)

# 在WTL向导生成的工程中使用DuiEngine


#### 1. 用WTL向导生成一个Module Dialog的工程：WTLDemo

#### 2. 用DuiEngine向导生成一个最简单的工程：DuiDemo

#### 3. 从DuiDemo目录下复制这些目录和文件到WTLDemo目录下

```
duires目录
skin目录
MainDlg.h和MainDlg.cpp
UIHandler.h和UIHandler.cpp
```

#### 4. 修改.rc文件，最方便的使用BeyondCompare等工具直接比较合并，其实需要合并过来的就是这三段


```cpp
    3 TEXTINCLUDE 
    BEGIN
        "#if !defined(AFX_RESOURCE_DLL) || defined(AFX_TARG_CHS)\r\n"
        "LANGUAGE 4, 2\r\n"
        "#pragma code_page(936)\r\n"
        "#include ""duires\\winres.rc2"" //DUI×ÊÔ´\r\n"
        "#endi\0"
    END



    /////////////////////////////////////////////////////////////////////////////
    //
    // XML2
    //
    
    IDR_NAME2ID             XML2                    "duires\\name2id.xml"
    	
    /////////////////////////////////////////////////////////////////////////////
    //
    // Icon
    //
    
    // Icon with lowest ID value placed first to ensure application icon
    // remains consistent on all systems.
    IDI_MAINWND             ICON                    "duires\\WTLDemo.ico"



    #ifndef APSTUDIO_INVOKED
    /////////////////////////////////////////////////////////////////////////////
    //
    // Generated from the TEXTINCLUDE 3 resource.
    //
    #if !defined(AFX_RESOURCE_DLL) || defined(AFX_TARG_CHS)
    LANGUAGE 4, 2
    #pragma code_page(936)
    #include "duires\winres.rc2" //DUI×ÊÔ´
    #endif
    /////////////////////////////////////////////////////////////////////////////
    #endif    // not APSTUDIO_INVOKED
```

** resource.h中新增两行 **

```
#define IDR_NAME2ID                     112
#define IDI_MAINWND                     113
```

** stdafx.h中加入duiengine的头文件和lib **

```cpp
    // dui include
    #include <duistd.h>
    #include <duihostwnd.h>
    #include <duictrls.h>
    #include <duiwndnotify.h>
    #include <DuiCSS.h>
    
    #include "resource.h"
    #include "duires/res.h"
    
    using namespace DuiEngine;
    
    #ifdef DLL_DUI
    	# pragma comment(lib, "duiengine.lib")
    #else
    	# pragma comment(lib, "duiengine_static.lib")
    #endif
```


* 5.WTLDemo.cpp中新增DuiEngine的初始化和销毁，以及调用逻辑，直接贴下代码


```cpp
    #include "stdafx.h"
    
    #include "resource.h"
    
    #include "MainDlg.h"
    #include "DuiSystem.h" 
    #include "DuiDefaultLogger.h"
    
    CAppModule _Module;
    
    //////////////////////////////////////////////////////////////////////////
    
    int RunUI()
    {
    	int nRet = 0;
    	CMainDlg dlgMain;
    	nRet = dlgMain.DoModal();
    
    	return nRet;
    }
    
    int RunDuiEngine(HRESULT hRes, HINSTANCE hInstance)
    {
    	DUIASSERT(SUCCEEDED(hRes));
    
    	TCHAR szCurrentDir[MAX_PATH + 1] = {0};
    	memset( szCurrentDir, 0, sizeof(szCurrentDir) );
    	GetModuleFileName( NULL, szCurrentDir, MAX_PATH );
    	LPTSTR lpInsertPos = _tcsrchr( szCurrentDir, _T('\\') );
    	*lpInsertPos = _T('\0');   
    
    	DuiSystem duiSystem(hInstance);
    
    	DefaultLogger loger;
    	loger.setLogFilename(CDuiStringT(szCurrentDir) + _T("\\duiengine.log"));
    	duiSystem.SetLogger(&loger);
    	duiSystem.logEvent(_T("demo started"));
    
    	// 加载ID名称对照表,该资源属于APP级别，所有皮肤应该共享该名字表，名字表总是从程序资源加载
    	duiSystem.InitName2ID(IDR_NAME2ID,_T("XML2"));
    
    #ifdef __DUIFILE_RC 
    	//从文件夹加载皮肤,指定皮肤位置
    	_tcscat( szCurrentDir, _T("\\..\\..\\..\\..\\coding\\demo\\WTLDemo\\skin") );
    	DuiResProviderFiles *pResFiles=new DuiResProviderFiles;
    	if(!pResFiles->Init(szCurrentDir))
    	{
    		DUIASSERT(0);
    		return 1;
    	}
    	duiSystem.SetResProvider(pResFiles);
    #else 
    	//从资源加载皮肤
    	duiSystem.SetResProvider(new DuiResProviderPE(hInstance));
    #endif 
    	
    	//初始化DUI系统,原来的系统初始化方式依然可以使用。
    	BOOL result = duiSystem.Init(IDR_DUI_INIT);
    	duiSystem.SetMsgBoxTemplate(IDR_DUI_MSGBOX);
    	
    	int uiResult = 0;
    	if (result)
    	{
    		uiResult = RunUI();
    	}
    
    	duiSystem.logEvent(_T("demo end"));
    	delete duiSystem.GetResProvider();
    
    	return uiResult;
    }
    
    
    int WINAPI _tWinMain(HINSTANCE hInstance, HINSTANCE /*hPrevInstance*/, LPTSTR /*lpstrCmdLine*/, int /*nCmdShow*/)
    {
    	HRESULT hRes = ::CoInitialize(NULL);
    // If you are running on NT 4.0 or higher you can use the following call instead to 
    // make the EXE free threaded. This means that calls come in on a random RPC thread.
    //	HRESULT hRes = ::CoInitializeEx(NULL, COINIT_MULTITHREADED);
    	ATLASSERT(SUCCEEDED(hRes));
    
    	// this resolves ATL window thunking problem when Microsoft Layer for Unicode (MSLU) is used
    	::DefWindowProc(NULL, 0, 0, 0L);
    
    	AtlInitCommonControls(ICC_BAR_CLASSES);	// add flags to support other controls
    
    	hRes = _Module.Init(NULL, hInstance);
    	ATLASSERT(SUCCEEDED(hRes));
    
    	AtlAxWinInit();
    
    	int nRet = RunDuiEngine(hRes, hInstance);
    
    	_Module.Term();
    	::CoUninitialize();
    
    	return nRet;
    }
```

* 6.配置Pre Build Event，直接从DuiDemo复制过来就可以了，就是调用下residbuilder
