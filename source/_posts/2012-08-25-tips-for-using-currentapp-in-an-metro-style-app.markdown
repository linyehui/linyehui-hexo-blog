---
title: 'Windows 8下如何使用Windows.ApplicationModel.Store的CurrentApp类'
layout: post
tags:
    - store
    - win8
    - windows8
    - winrt
---

# 首先你应该知道这个类大概是做什么的：  

Windows::ApplicationModel::Store::CurrentApp  

[MSDN上的说明：Windows.ApplicationModel.Store classes](http://msdn.microsoft.com/en-us/library/windows/apps/br229885.aspx)

上面的MSDN页面给我们提供的API主要是用于Metro Style  App的Store购买相关的信息和功能，其实微软给我们提供了两个类：  


```
CurrentApp class
Defines methods and properties you can use to get license and listing info about the current app and perform in-app purchases.

CurrentAppSimulator class
Defines methods and properties used to instantiate an object that you can use to get simulated license info during testing.
```

这两个类中，微软推荐我们用CurrentAppSimulator进行测试，可惜我有个坏毛病，代码一定要自己跑过一次才相信，微软所说的先用CurrentAppSimulator调试，上架的时候直接换成CurrentApp就万事大吉了，这样的“理所当然”我可不信，所以我想方设法想亲自验证下CurrentApp这个类的功能，下面是一些我自己的验证。

# 测试环境：
外网大家正在用的Windows 8 RTM版本，VS2012（这俩微软官没有正式版，所以本文的结论一段时间后可能会被微软自己推翻）

# 我遇到的问题：
微软的MSDN说要调试CurrentApp必须使用开发者帐号，且应用已经上架，那我就想我本地想调试应该怎么弄呢？我不想用CurrentAppSimulator敷衍我自己  


```
2012/9/8 补充
这里其实有猫腻，所谓的已经上架还不够，还需要你在调试的机器上购买并下载了这个应用才算数
所以调试之前一定要到应用商店的“你的应用”中检查下要调试的应用的状态
```
## 最后成功的方案和需要注意的细节：
* 1、应用已经上架，这个是必须的，如果没有的话就确实只能用CurrentAppSimulator
* 2、Package.appxmanifest里Packageing Tab页的Publisher属性必须是你已经上架了的应用的开发者帐号对应的证书，这个证书在你创建用于上传到Store的Package时会自动帮你填充（**这个证书是关键**）
* 3、只要你调试的时候符合上面两个条件，无论你是本机直接F5调试还是编译本地安装包然后测试，都能获取到CurrentApp的Store信息。你会发现异步获取到的ListingInformation就是你在Store后台配置的应用信息。(ListingInformation->Name就是Store上的应用名)

## 修改安装后的应用文件是否会影响CurrentApp->LicenseInformation的IsActive和IsTrial呢？
我的实验结果是不会，而且我发现这影响Store返回的两个属性值的关键在于我上面提到的Package.appxmanifest里Packageing Tab页的Publisher这个证书。  

也就是说你从Store安装了某个应用，然后修改这个应用安装目录下的文件，CurrentApp->LicenseInformation的IsActive和IsTrial属性并不会受到影响，其他的属性我就没有进一步确认，大家有兴趣可以自己验证。

## 2012/9/8 21:18 今晚调试后再补充点信息吧，希望对读者有用：
* 1、没有网络的时候获取License Information是会失败的
* 2、没有购买的时候获取License Information也是会失败的
* 3、License获取的线程也是有讲究的，你用线程定时器起来的异步操作中执行的话，需要先做下线程切换

所以我们需要对代码进行异常捕获，要这么写才可以： 

```cpp
 void AppEngine::InitializeLicenseCore()
 {
 	try
 	{
 		m_licenseInformation = CurrentApp::LicenseInformation;
 		task<ListingInformation^> listingTask(CurrentApp::LoadListingInformationAsync());
 		this->m_licenseInformation->LicenseChanged += ref new LicenseChangedEventHandler(this, &AppEngine::OnLicenseChanged);
 		this->OnLicenseChanged();
 
 		listingTask.then([=](ListingInformation^ listing)
 		{
 			this->m_listingInformation = listing;
 			Platform::String^ name = this->m_listingInformation->Name;
 			// ...
 		})
 		.then([=](Concurrency::task<void> t)
 		{
 			try
 			{
 				t.get();
 			}
 			catch (Platform::Exception ^ e)
 			{
 				log_error("license listing task exception");
 			}
 		});
 	}
 	catch (Platform::Exception^ e)
 	{
 		log_error("InitializeLicenseCore exception");
 	}
 }
```

Windows 8还没正式发布，我所说的都是错的 :)