---
layout: post
title: "android studio 安装无法打开"
date: 2013-05-23 23:01
comments: true
categories: android
---

最近谷歌发布了Android Studio，是一个全新的Android开发环境，基于IntelliJIDEA。可以作为替代eclispe+adt的开发环境。

下载地址 <http://developer.android.com/sdk/installing/studio.html>
<!-- more -->
现在是0.1版，编写布局文件实时预览并适配多屏的功能是亮点。

但是安装后出现可打开程序无反应的情况，似乎是系统变量中JAVA_HOME、CLASSPATH、PATH的配置问题。

但修改后也没有解决。。最后重新安装JDK1.7后就没问题了（原来是1.6）。