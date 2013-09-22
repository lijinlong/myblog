---
layout: post
title: "cocos2d Configuration class"
description: ""
category: cocos2d
tags: [intro, tutorial, cocos2d]
---
{% include JB/setup %}

cocos2dx version: 3.0preAlpha

##今天发现cocos2dx的Configuration类可以从文件读取配置信息。

本来Configuration类在Cocos2d加载时会保存一些opengl和cocos2d本身的以下信息。今天发现他有一个loadConfigFile方法

{% highlight cpp %}
/** Loads a config file. If the keys are already present, then they are going to be replaced. Otherwise the new keys are added. */
	void loadConfigFile(const char *filename);
{% endhighlight %}

加载文件的格式是plist格式，在data中方你自己的数据，现在只支持format为1

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>data</key>
	<dict>
		<key>your key</key>
		<integer>对用的值</integer>
	</dict>
	<key>metadata</key>
	<dict>
		<key>format</key>
		<integer>1</integer>
	</dict>
</dict>
</plist>
{% endhighlight %}

这样就可以使用Configuration保存自己的数据了，如果你愿意的花，可以从不同的文件读取数据，Configuration会自动合并。可以将每个文件的数据写成一个dict减少Configuration查询key的压力。

你也可以通过setObject方法写入信息，通过getXXX方法读出来
{% highlight cpp %}
/** sets a new key/value pair  in the configuration dictionary */
    void setObject(const char *key, Object *value);
/** returns the value of a given key as a double */
	Object * getObject(const char *key) const;
{% endhighlight %}


