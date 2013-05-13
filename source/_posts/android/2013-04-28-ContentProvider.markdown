---
layout: post
title: "Android ContentProvider"
date: 2013-04-28 20:01
comments: true
categories: android
---
ContentProvider是Android不同应用程序之间进行数据交换的标准API。ContentProvider是Android的四大组件之一（Activity、ContentProvider、Service、BroadcastReceiver），他们都需要在Mainfest文件中配置。ContentProvider以Uri对外提供数据，其他应用使用ContentResolver根据Uri去访问操作指定数据。
<!-- more -->

其中ContentProvider负责

+ 组织应用程序的数据；

+ 向其他应用程序提供数据；

ContentResolver则负责

+ 获取ContentProvider提供的数据；

+ 修改/添加/删除更新数据等；















