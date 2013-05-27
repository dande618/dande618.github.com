---
layout: post
title: "Android Context"
date: 2013-05-27 20:59
comments: true
categories: android
---
<http://blog.csdn.net/qinjuning/article/details/7310620>

{% img http://img.my.csdn.net/uploads/201203/1/0_1330607569Vj4c.gif %}

Context，中文直译为“上下文”，SDK中对其说明如下：

> Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

从上可知一下三点,即：

1. 它描述的是一个应用程序环境的信息，即上下文。

2. 该类是一个抽象(abstract class)类，Android提供了该抽象类的具体实现类(后面我们会讲到是ContextIml类)。

3. 通过它我们可以获取应用程序的资源和类，也包括一些应用级别操作，例如：启动一个Activity，发送广播，接受Intent信息 等。。

于是，我们可以利用该Context对象去构建应用级别操作(application-level operations) 。

__Context类__    路径： /frameworks/base/core/java/android/content/Context.java

说明：  抽象类，提供了一组通用的API。

源代码(部分)如下：   

{% codeblock lang:java %}
public abstract class Context {
     //...
     public abstract Object getSystemService(String name);  //获得系统级服务
     public abstract void startActivity(Intent intent);     //通过一个Intent启动Activity
     public abstract ComponentName startService(Intent service);  //启动Service
     //根据文件名得到SharedPreferences对象
     public abstract SharedPreferences getSharedPreferences(String name,int mode);
     //...
}
{% endcodeblock %}

Activity类 、Service类 、Application类本质上都是Context子类。

应用程序创建Context实例的情况有如下几种情况：

1. 创建Application对象时， 而且整个App共一个Application对象

2. 创建Service对象时

3. 创建Activity对象时