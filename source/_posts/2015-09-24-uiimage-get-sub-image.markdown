---
title: 'iOS图片裁剪scale问题'
layout: post
tags:
    - mac
    - ios
    - uiimage
---

## 问题
资源中有一张cover@2x.png，对图片进行裁剪后图片的大小是对的，但是图片上裁剪的区域不对。

## 先直接看最终的代码吧
```objc
/**
 *  截取部分图像
 *
 *  @param rect 裁剪的区域
 *
 */
-(UIImage *)getSubImage:(CGRect)rect{
    CGImageRef subImageRef = CGImageCreateWithImageInRect(self.CGImage,
														  CGRectMake(rect.origin.x,
																	 rect.origin.y,
																	 rect.size.width * self.scale,
																	 rect.size.height * self.scale));
    CGRect smallBounds = CGRectMake(0, 0, CGImageGetWidth(subImageRef), CGImageGetHeight(subImageRef));
    
    //UIGraphicsBeginImageContext(smallBounds.size);
    UIGraphicsBeginImageContextWithOptions(smallBounds.size, NO, [UIScreen mainScreen].scale);
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextDrawImage(context, smallBounds, subImageRef);
    UIImage* smallImage = [UIImage imageWithCGImage:subImageRef];
    UIGraphicsEndImageContext();
    
    return smallImage;
}
```

## 问题原因
@2x，表示图片的scale是2，但是裁剪的时候，使用UIImage size获取到的其实是图片@2x处理后的大小，也就是除以2之后的大小，所以导致了裁剪的区域错误。

## 解决方案
解决的方案如代码所见，对需要裁剪的高度和宽度都乘以scale就可以了。


## 参考
[UIGraphicsBeginImageContextWithOptions](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIKitFunctionReference/#//apple_ref/c/func/UIGraphicsBeginImageContextWithOptions)