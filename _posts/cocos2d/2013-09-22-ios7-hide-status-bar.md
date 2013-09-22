---
layout: post
title: "ios7 hide status bar"
description: ""
category: cocos2d 
tags: [cocos2d, ios]
---
{% include JB/setup %}

## ios7隐藏状态栏
参考[cocoschina](http://www.cocoachina.com/bbs/read.php?tid=144039&page=2)

iOS7中通过ViewController重载方法返回枚举值的方法来控制状态栏的隐藏和样式。
首先，需要在Info.plist配置文件中，增加键：UIViewControllerBasedStatusBarAppearance，并设置为YES；
然后，在UIViewController子类中实现以下两个方法：

{% highlight objc %}
- (UIStatusBarStyle)preferredStatusBarStyle
{
    return UIStatusBarStyleLightContent;
}
- (BOOL)prefersStatusBarHidden
{
    return YES;
}
{% endhighlight %}
