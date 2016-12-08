---
title: '版本号比较'
layout: post
tags:
    - c++
    - util
---

## 需求
比较类似1.0.0的两个字符串

## 实现方案
印象中这个代码自己写过好多次，不过这次写的是最快的，5分钟之内测试通过

```cpp
// ConsoleApplication2.cpp : Defines the entry point for the console application.
//

#include "stdafx.h"

#include <stdlib.h>
#include <stdio.h>

#include <string>

struct UpdateVersion
{
	int major;
	int minor;
	int revision;

	UpdateVersion()
	{
		major = 0;
		minor = 0;
		revision = 0;
	}

	UpdateVersion(const UpdateVersion& item)
	{
		major = item.major;
		minor = item.minor;
		revision = item.revision;
	}

	UpdateVersion& operator=(const UpdateVersion& item)
	{
		major = item.major;
		minor = item.minor;
		revision = item.revision;

		return *this;
	}
};

UpdateVersion parseUpdateVersion(const std::string& versionStr)
{
	UpdateVersion result;
	if (versionStr.empty())
		return result;

	size_t pos1 = versionStr.find('.', 0);
	size_t pos2 = versionStr.find('.', pos1 + 1);
	if (pos1 == -1 || pos2 == -1 || pos2 >= versionStr.length())
		return result;

	result.major = atoi(versionStr.substr(0, pos1).c_str());
	result.minor = atoi(versionStr.substr(pos1 + 1, pos2 - pos1).c_str());
	result.revision = atoi(versionStr.substr(pos2 + 1, versionStr.length() - pos2).c_str());

	return result;
}

int compareUpdateVersion(const UpdateVersion& left, const UpdateVersion& right)
{
	if (left.major != right.major)
	{
		return left.major > right.major ? 1 : -1;
	}
	else if (left.minor != right.minor)
	{
		return left.minor > right.minor ? 1 : -1;
	} 
	else if (left.revision != right.revision)
	{
		return left.revision > right.revision ? 1 : -1;
	}
	else
	{
		return 0;
	}
}

int compareUpdateVersion(const std::string& left, const std::string& right)
{
	UpdateVersion leftVer = parseUpdateVersion(left);
	UpdateVersion rightVer = parseUpdateVersion(right);

	return compareUpdateVersion(leftVer, rightVer);
}

int _tmain(int argc, _TCHAR* argv[])
{
	printf("%d\n", strcmp("1.0.0", "1.0.0"));
	printf("%d\n", strcmp("1.0.0", "1.0.1"));

	printf("%d\n", strcmp("1.0.9", "1.0.10"));
	printf("%d\n", strcmp("1.1.0", "1.0.10"));

	printf("======================\n");

	printf("0 == %d\n", compareUpdateVersion("1.0.0", "1.0.0"));
	printf("0 == %d\n", compareUpdateVersion("2.4.3", "2.4.3"));

	printf("-1 == %d\n", compareUpdateVersion("1.0.0", "1.0.1"));
	printf("-1 == %d\n", compareUpdateVersion("1.0.9", "1.0.10"));
	printf("-1 == %d\n", compareUpdateVersion("1.0.10", "1.1.0"));
	printf("-1 == %d\n", compareUpdateVersion("1.2.10", "2.0.0"));

	printf("1 == %d\n", compareUpdateVersion("1.1.0", "1.0.0"));
	printf("1 == %d\n", compareUpdateVersion("1.0.10", "1.0.0"));
	printf("1 == %d\n", compareUpdateVersion("1.1.0", "1.0.10"));
	printf("1 == %d\n", compareUpdateVersion("100.1.0", "10.0.10"));

	return 0;
}


```