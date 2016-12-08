---
title: 'WebBrowser内嵌页面的跨域调用问题'
layout: post
tags:
---

很早之前我写过一篇Blog：[网页通过External接口与WebBrowser交互](http://blog.csdn.net/debehe/article/details/5053759)，文中的交互其实只介绍了JS调用C++的部分，而C++调用JS由于微软自己的例子太多，那篇文章就没介绍，不过我最近遇到了一个新问题，和C++调用JS有关，所以重新梳理了下这块的逻辑，把之前的代码完善了下。

## 我遇到的问题：

内嵌IE浏览器控件WebBrowser的内嵌页host.html中使用iframe又嵌套了一个页面iframe.html，iframe.html上有个JS方法，我用C++调用不到，而host.html上的JS方法可以正常调用到。

## 问题分析：

从JS来说，这是个跨域问题，host.html和iframe.html不在一个域内；
JS解决跨域问题的方案其实也是有的，但这个不是我们本文的重点，本文的重点是怎么通过WebBrowser控件直接来解决这个“跨域调用”的问题。

### 问题的根本原因在于：

调用网页的JS需要拿到IHTMLDocument2接口，而每个iframe都有自己对应的IHTMLDocument2，所以我们只要能拿到iframe对应的IHTMLDocument2就能解决问题了。

## 解决方案：直接上代码吧



    /* -------------------------------------------------------------------------
    //	FileName	:	calljs_helper.h
    //	Creator		:	linyehui
    //	Date		:	2013/11/16 01:18:09
    //	Brief		:	调用WebBrowser控件内嵌页上的JS函数，iframe中的也能调到
    //
    //	$Id: $
    // -----------------------------------------------------------------------*/
    #ifndef __CALLJS_HELPER_H__
    #define __CALLJS_HELPER_H__
    
    // -------------------------------------------------------------------------
    namespace calljs_helper
    {
    	bool CallFunction(
    		CComPtr<IWebBrowser2> spIWebBrowser, 
    		LPCTSTR lpFuncName,
    		const vector<wstring>& paramArray, 
    		CComVariant * pVarResult = NULL,
    		bool bEnumFrame = true);
    
    } // namespace
    
    // -------------------------------------------------------------------------
    // $Log: $
    
    #endif /* __CALLJS_HELPER_H__ */



    /* -------------------------------------------------------------------------
    //	FileName	:	calljs_helper.cpp
    //	Creator		:	linyehui
    //	Date		:	2013/11/16 01:18:14
    //	Brief		:	调用WebBrowser控件内嵌页上的JS函数，iframe中的也能调到
    //
    //	$Id: $
    // -----------------------------------------------------------------------*/
    
    #include "stdafx.h"
    #include "calljs_helper.h"
    
    // -------------------------------------------------------------------------
    
    CComPtr<IWebBrowser2> HtmlWindowToHtmlWebBrowser(CComPtr<IHTMLWindow2> spWindow)
    {
    	ATLASSERT(spWindow != NULL);
    	CComQIPtr<IServiceProvider>  spServiceProvider = spWindow;
    	if (spServiceProvider == NULL)
    	{
    		return CComPtr<IWebBrowser2>();
    	}
    
    	CComPtr<IWebBrowser2> spWebBrws;
    	HRESULT hRes = spServiceProvider->QueryService(IID_IWebBrowserApp, IID_IWebBrowser2, (void**)&spWebBrws);
    	if (hRes != S_OK)
    	{
    		return CComPtr<IWebBrowser2>();
    	}
    
    	return spWebBrws;
    }
    
    // Converts a IHTMLWindow2 object to a IHTMLDocument2. Returns NULL in case of failure.
    // It takes into account accessing the DOM across frames loaded from different domains.
    CComPtr<IHTMLDocument2> HtmlWindowToHtmlDocument(CComPtr<IHTMLWindow2> spWindow)
    {
    	ATLASSERT(spWindow != NULL);
    	CComPtr<IHTMLDocument2> spDocument;
    	HRESULT hRes = spWindow->get_document(&spDocument);
    	if ((S_OK == hRes) && (spDocument != NULL))
    	{
    		// The html document was properly retrieved.
    		return spDocument;
    	}
    
    	// hRes could be E_ACCESSDENIED that means a security restriction that
    	// prevents scripting across frames that loads documents from different internet domains.
    	CComPtr<IWebBrowser2> spBrws = HtmlWindowToHtmlWebBrowser(spWindow);
    	if (spBrws == NULL)
    	{
    		return CComPtr<IHTMLDocument2>();
    	}
    
    	// Get the document object from the IWebBrowser2 object.
    	CComPtr<IDispatch> spDisp;
    	hRes = spBrws->get_Document(&spDisp);
    	spDocument = spDisp;
    	return spDocument;
    }
    
    bool CallFunctionInDocument(
    							CComPtr<IHTMLDocument2> spDocument2, 
    							LPCTSTR lpFuncName,
    							const vector<wstring>& paramArray, 
    							CComVariant * pVarResult)
    {
    	if (!spDocument2)
    		return false;
    
    	CComPtr<IDispatch> spScript; 
    	if (FAILED(spDocument2->get_Script(&spScript))) { return false; }
    
    	CComBSTR bstrMember(lpFuncName); 
    	DISPID dispid = NULL; 
    	HRESULT hr = spScript->GetIDsOfNames(IID_NULL, &bstrMember, 1, LOCALE_SYSTEM_DEFAULT, &dispid); 
    	if (FAILED(hr)) { return false; }
    
    	const int arraySize = paramArray.size(); 
    
    	DISPPARAMS dispparams; 
    	memset(&dispparams, 0, sizeof dispparams); 
    	dispparams.cArgs = arraySize; 
    	dispparams.rgvarg = new VARIANT[dispparams.cArgs]; 
    
    	for (int i = 0; i < arraySize; i++)
    	{
    		CComBSTR bstr = paramArray[arraySize - 1 - i].c_str(); // back reading
    		bstr.CopyTo(&dispparams.rgvarg[i].bstrVal); 
    		dispparams.rgvarg[i].vt = VT_BSTR; 
    	}
    	dispparams.cNamedArgs = 0; 
    
    	EXCEPINFO excepInfo; 
    	memset(&excepInfo, 0, sizeof excepInfo); 
    	CComVariant vaResult; 
    	UINT nArgErr = (UINT)-1;  // initialize to invalid arg
    
    	hr = spScript->Invoke(dispid, IID_NULL, 0, DISPATCH_METHOD, &dispparams, &vaResult, &excepInfo, &nArgErr); 
    
    	delete [] dispparams.rgvarg; 
    	if (FAILED(hr)) { return false; }
    
    	if (pVarResult) { *pVarResult = vaResult; }
    
    	return true;
    }
    
    void EnumFrame(
    							  CComPtr<IHTMLDocument2> spIHTMLDocument2, 
    							  LPCTSTR lpFuncName,const vector<wstring>& paramArray, 
    							  CComVariant* pVarResult)
    {
    	if ( !spIHTMLDocument2 )
    		return;
    
    	CComPtr< IHTMLFramesCollection2 > spFramesCollection2;
    	spIHTMLDocument2->get_frames( &spFramesCollection2 );
    
    	long nFrameCount=0;
    	HRESULT hr = spFramesCollection2->get_length( &nFrameCount );
    	if ( FAILED ( hr ) || 0 == nFrameCount )
    		return;
    
    	for(long i = 0; i < nFrameCount; i++)
    	{
    		CComVariant vDispWin2;
    		hr = spFramesCollection2->item( &CComVariant(i), &vDispWin2 );
    		if ( FAILED ( hr ) ) 
    			continue;
    
    		CComQIPtr< IHTMLWindow2 > spWin2 = vDispWin2.pdispVal;
    		if( !spWin2 )
    			continue;
    
    		CComPtr < IHTMLDocument2 > spDoc2;
    		spDoc2 = HtmlWindowToHtmlDocument(spWin2);
    		if (!spDoc2)
    			continue;
    
    		CallFunctionInDocument(spDoc2, lpFuncName, paramArray, pVarResult);
    	}
    }
    
    bool calljs_helper::CallFunction(
    				  CComPtr<IWebBrowser2> spIWebBrowser, 
    				  LPCTSTR lpFuncName,
    				  const vector<wstring>& paramArray, 
    				  CComVariant * pVarResult,
    				  bool bEnumFrame)
    {
    	if (!spIWebBrowser) 
    		return false;
    
    	CComPtr<IDispatch> spDispDoc; 
    	HRESULT hr = spIWebBrowser->get_Document(&spDispDoc); 
    	if (FAILED(hr))
    		return false;
    
    	CComQIPtr<IHTMLDocument2> spDocument2 = spDispDoc; 
    	if (!spDocument2)
    		return false;
    
    	CallFunctionInDocument(spDocument2, lpFuncName, paramArray, pVarResult);
    	
    	if (bEnumFrame)
    	{
    		EnumFrame(spDocument2, lpFuncName, paramArray, pVarResult);
    	}
    	
    	return true; 
    }
    
    // -------------------------------------------------------------------------
    // $Log: $


完整的例子代码在附件中。

### 另外再附上帮助我解决问题的参考资料:

[IHTMLWindow2的get_document方法有时候会返回E_ACCESSDENIED](http://blog.csdn.net/skyremember/article/details/3422841)  

[VC++实现浏览器自动填表](http://blog.csdn.net/nvidiacuda/article/details/9300869)  

[webbrowser控件的IHTMLDocument3 or2接口的iframe问题（非安全设置）](http://www.itdelphi.com/delphibbs/doc/2007/3845499.htm)  

[window.name实现的跨域数据传输](http://www.cnblogs.com/rainman/archive/2011/02/21/1960044.html)  

[JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)  


The End.