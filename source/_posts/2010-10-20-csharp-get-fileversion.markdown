---
title: '.NET CF下获取文件版本号'
layout: post
tags:
---

.NET CF 3.5下可用的取文件版本号方法

```csharp
string path = @"C:\WINNT\Microsoft.NET\Framework\v1.1.4322\System.dll";  
Assembly assembly = Assembly.LoadFile(path);  
// 获取当前程序集的版本就用这个
// Assembly assembly = Assembly.GetExecutingAssembly();   

AssemblyName assemblyName = assembly.GetName();  
Version version = assemblyName.Version;
```